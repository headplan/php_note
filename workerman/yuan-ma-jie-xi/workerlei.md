# Worker类

#### 构造函数

```php
public function __construct($socket_name = '', $context_option = array())
```

初始化一个Worker容器实例 , 可以设置容器的一些属性和回调接口 , 完成特定功能 .

```php
/**
 * Construct.
 *
 * @param string $socket_name
 * @param array  $context_option
 */
public function __construct($socket_name = '', $context_option = array())
{
    // Save all worker instances.
    $this->workerId                    = spl_object_hash($this);
    static::$_workers[$this->workerId] = $this;
    static::$_pidMap[$this->workerId]  = array();

    // Get autoload root path.
    $backtrace                = debug_backtrace();
    $this->_autoloadRootPath = dirname($backtrace[0]['file']);

    // Context for socket.
    if ($socket_name) {
        $this->_socketName = $socket_name;
        if (!isset($context_option['socket']['backlog'])) {
            $context_option['socket']['backlog'] = static::DEFAULT_BACKLOG;
        }
        $this->_context = stream_context_create($context_option);
    }
}
```

初始化参数均为可选参数 .

#### **$socket\_name**

不填写表示不监听任何端口 . 填写则会执行socket监听 .

```
参数格式为<协议>://<监听地址>
```

**协议 - **协议就是前面提到的tcp,udp,http等.

**监听地址**

如果是unix套接字 , 地址为本地一个磁盘路径 .

非unix套接字 , 地址格式为 &lt;本机ip&gt;:&lt;端口号&gt;

&lt;本机ip&gt;可以为`0.0.0.0`表示监听本机所有网卡 , 包括内网ip和外网ip及本地回环`127.0.0.1`

&lt;本机ip&gt;如果以为`127.0.0.1`表示监听本地回环 , 只能本机访问 , 外部无法访问

&lt;本机ip&gt;如果为内网ip，类似`192.168.xx.xx`, 表示只监听内网ip , 则外网用户无法访问

&lt;本机ip&gt;设置的值不属于本机ip则无法执行监听 , 并且提示`Cannot assign requested address`错误

**注意：**&lt;端口号&gt;不能大于65535 . &lt;端口号&gt;如果小于1024则需要root权限才能监听 . 监听的端口必须是本机未被占用的端口 , 否则无法监听 , 并且提示`Address already in use`错误 .

#### $context\_option

一个数组 , 用于传递socket的上下文选项 , 参见套接字上下文选项 . 例如 :

```php
$opts = array(
    'socket' => array(
        'bindto' => '192.168.0.100:7000',
    ),
);
```

一些初始化示例 :

```php
# Worker作为http容器监听处理http请求
$worker = new Worker('http://0.0.0.0:8686');

# Worker作为websocket容器监听处理websocket请求
$worker = new Worker('websocket://0.0.0.0:8686');

# Worker作为tcp容器监听处理tcp请求
$worker = new Worker('tcp://0.0.0.0:8686');

# Worker作为udp容器监听处理udp请求
$worker = new Worker('udp://0.0.0.0:8686');

# Worker监听unix domain套接字
$worker = new Worker('unix:///tmp/my.sock');

# 不执行任何监听的Worker容器,用来处理一些定时任务
$task = new Worker();
$task->onWorkerStart = function($task)
{
    // 每2.5秒执行一次
    $time_interval = 2.5;
    Timer::add($time_interval, function()
    {
        echo "task run\n";
    });
};
```

#### **Worker监听自定义协议的端口**

目录结构 : 

```
├── Protocols              # 这是要创建的Protocols目录
│   └── MyTextProtocol.php # 这是要创建的自定义协议文件
├── test.php               # 这是要创建的test脚本
└── Workerman              # Workerman源码目录,里面代码不动
```

创建协议目录以及自定义的协议类 , 可以继承Workerman中的协议定义接口 , 完成下面的几个方法即可 : 

```php
// 用户自定义协议命名空间统一为Protocols
namespace Protocols;
//简单文本协议，协议格式为 文本+换行
class MyTextProtocol
{
    // 分包功能，返回当前包的长度
    public static function input($recv_buffer)
    {
        // 查找换行符
        $pos = strpos($recv_buffer, "\n");
        // er行符，表示不是一个完整的包，返回0继续等待数据
        if($pos === false)
        {
            return 0;
        }
        // 查找到换行符，返回当前包的长度，包括换行符
        return $pos+1;
    }

    // 收到一个完整的包后通过decode自动解码，这里只是把换行符trim掉
    public static function decode($recv_buffer)
    {
        return trim($recv_buffer);
    }

    // 给客户端send数据前会自动通过encode编码，然后再发送给客户端，这里加了换行
    public static function encode($data)
    {
        return $data."\n";
    }
}s
```



