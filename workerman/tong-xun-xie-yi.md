# 通讯协议

#### 通讯协议的作用

由于TCP是基于流的 , 客户端发送的请求数据是像水流一样流入到服务端 , 服务端探测到有数据到来后应该检查数据是否是完整的 , 因为可能只是一个请求的部分数据到达服务端 , 甚至可能是多个请求连在一起到达服务端 . 如何判断请求是否全部到达或者从多个连在一起的请求中分离请求 , 就需要规定一套通讯协议 .

#### 在WorkerMan中为什么要制定协议

传统PHP开发都是基于Web的 , 基本上都是HTTP协议 , HTTP协议的解析处理都由WebServer独自承担了 , 所以开发者不会关心协议方面的事情 . 然而当我们需要基于非HTTP协议开发时 , 开发者就需要考虑协议的事情了 .

#### WorkerMan已经支持的协议

WorkerMan目前已经支持HTTP、websocket、text协议、frame协议、ws协议

```php
use Workerman\Worker;
// websocket://0.0.0.0:2345 表明用websocket协议监听2345端口
$websocket_worker = new Worker('websocket://0.0.0.0:2345');

// text协议
$text_worker = new Worker('text://0.0.0.0:2346');

// frame协议
$frame_worker = new Worker('frame://0.0.0.0:2347');

// tcp Worker,直接基于socket传输,不使用任何应用层协议
$tcp_worker = new Worker('tcp://0.0.0.0:2348');

// udp Worker,不使用任何应用层协议
$udp_worker = new Worker('udp://0.0.0.0:2349');

// unix domain Worker,不使用任何应用层协议
$unix_worker = new Worker('unix:///tmp/wm.sock');
```

#### 使用自定义的通讯协议

当WorkerMan自带的通讯协议满足不了开发需求时 , 开发者可以定制自己的通讯协议 , 定制方法见下一节内容 .

**提示 : **

Workerman内置了一个text协议 , 协议格式为文本+换行符 . text协议开发调试都非常简单 , 可用于绝大多数自定义协议的场景 , 并且支持telnet调试 . 如果开发者要开发自己的应用协议 , 可以直接使用text协议 , 不用再单独开发 .

---

#### 定制通讯协议

实际上制定自己的协议是比较简单的事情 . 简单的协议一般包含两部分 :

* 区分数据边界的标识
* 数据格式定义

#### 定义协议

这里假设区分数据边界的标识为换行符"\n"\(注意请求数据本身内部不能包含换行符\) , 数据格式为Json , 例如下面是一个符合这个规则的请求包 .

```php
{"type":"message","content":"hello"}
\n
```

注意上面的请求数据末尾有一个换行字符\(在PHP中用**双引号**字符串"\n"表示\) , 代表一个请求的结束 .

**接下来开始实现**

假设协议的名字叫JsonNL , 所在项目为MyApp :

* 创建协议文件 , 放到项目的Protocols文件夹 , 例如文件MyApp/Protocols/JsonNL.php
* 实现JsonNL类 , 以`namespace Protocols;`为命名空间 , 必须实现三个静态方法分别为 input、encode、decode

Workerman会自动调用这三个静态方法 , 用来实现分包、解包、打包 .

#### Workerman与协议类交互流程

* 假设客户端发送一个数据包给服务端 , 服务端收到数据\(可能是部分数据\)后会立刻调用协议的`input`方法 , 用来检测这包的长度 , `input`方法返回长度值`$length`给workerman框架 . 
* Workerman框架得到这个`$length`值后判断当前数据缓冲区中是否已经接收到`$length`长度的数据 , 如果没有就会继续等待数据 , 直到缓冲区中的数据长度不小于`$length`
* 缓冲区的数据长度足够后 , Workerman就会从缓冲区截取出`$length`长度的数据\(即**分包**\) , 并调用协议的`decode`方法**解包** , 解包后的数据为`$data`
* 解包后Workerman将数据$data以回调onMessage\($connection, $data\)的形式传递给业务 , 业务在onMessage里就可以使用$data变量得到客户端发来的完整并且已经解包的数据了 . 
* 当onMessage里业务需要通过调用$connection-&gt;send\($buffer\)方法给客户端发送数据时 , workerman会自动利用协议的encode方法将$buffer打包后再发给客户端

**MyApp/Protocols/JsonNL.php的实现**

```php
<?php

namespace Protocols;

class JsonNL
{
    /**
     * 检查包的完整性
     * 如果能够得到包长,则返回包的在buffer中的长度,否则返回0继续等待数据
     * 如果协议有问题,则可以返回false,当前客户端连接会因此断开
     * @param string $buffer
     * @return int
     */
    public static function input($buffer)
    {
        # 获得换行字符"\n"位置
        $pos = strpos($buffer, "\n");
        # 没有换行符,无法得知包长,返回0继续等待数据
        if($pos === false)
        {
            return 0;
        }
        # 有换行符,返回当前包长(包含换行符)
        return $pos+1;
    }

    /**
     * 打包,当向客户端发送数据的时候会自动调用
     * @param string $buffer
     * @return string
     */
    public static function encode($buffer)
    {
        # json序列化,并加上换行符作为请求结束的标记
        return json_encode($buffer)."\n";
    }

    /**
     * 解包,当接收到的数据字节数等于input返回的值(大于0的值)自动调用
     * 并传递给onMessage回调函数的$data参数
     * @param string $buffer
     * @return string
     */
    public static function decode($buffer)
    {
        # 去掉换行,还原成数组
        return json_decode(trim($buffer), true);
    }
}
```

现在JsonNL协议实现完毕 , 可以在MyApp项目中使用 :

```php
use Workerman\Worker;
require_once '/your/path/Workerman/Autoloader.php'
$json_worker = new Worker('JsonNL://0.0.0.0:1234');
$json_worker->onMessage = function($connection, $data) {

    // $data就是客户端传来的数据，数据已经经过JsonNL::decode处理过
    echo $data;

    // $connection->send的数据会自动调用JsonNL::encode方法打包，然后发往客户端
    $connection->send(array('code'=>0, 'msg'=>'ok'));

};
Worker::runAll();
...
```



