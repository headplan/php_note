# 理解RPC远程调用

远程调用 , 即Remote Procedure Call . 也就是RPC调用 .

简单理解 :

1.本地调用 , 远程机器执行 .

2.某些调用形式 , 用浏览器访问可以看到xml描述 .

3.某些语言中调用形式 , 需要在本地生成一个伪调用类 .

4.如果PHP做服务端 , java等其他语言也可以调用 .

#### 相关代码

> 代码查看本地rpc\_server.php等 .

通过浏览器访问rpc服务 , 其中判断浏览器过来的url , 然后判断指定文件是否存在 , 这个文件就是我们提供的远程调用的服务 . 比如 , 新建service文件夹 , 创建一个news.php文件 , 创建NewsService类作为被调用的服务 .

```php
<?php

class NewsService
{
    public function display()
    {
        return 'I\'m RPC Server';
    }

    public function show()
    {
        return 'Github被微软收购.';
    }
}
```

服务端为了方便测试 , 判断如果是通过浏览器过来的 , 或者是带有get的请求就返回上面的News服务中的内容 :

```php
# 判断一下浏览器访问,也就是HTTP协议
if (preg_match("/GET\s\/(.*?)\sHTTP\/1.1/i", $buffer,$matches)) {
    $path = $matches[1];
    if (file_exists($path)) {
        require_once $path;
        # 获取当前已经被定义的所有类,这里也包含了PHP内部定义的类
        $class = get_declared_classes();
        # 指针指到最后,也就是我们require_once进来的类
        $obj_class = end($class);

        $obj = new $obj_class();

        $result = '';
        # 遍历前面实例化对象中的方法,拼接个json串
        foreach (get_class_methods($obj) as $method) {
            if ($result != '') $result .= ',';
            $result .= '"method":"' . $method . '"';
        }

        $result = '{' . $result . '}';
        socket_write($client, $html.$result);
    } else {
        socket_write($client, $html.'Not Find Service!');
    }
} else {
    socket_write($client, $html.'404');
}
```

现在 , 通过浏览器测试 , 就能访问到一个拼接的串 , 格式可能有错误 , 只测试 :

```js
{"method":"display","method":"show"}
```

**创建自定义客户端**

```php
<?php

$socket = @socket_create(AF_INET, SOCK_STREAM, SOL_TCP);

$ip = '127.0.0.1';
socket_connect($socket, $ip, 9933);

# 前面判断了http协议,这里添加过判断的内容
$http = 'GET /service/news.php HTTP/1.1' . PHP_EOL;
# 在添加一个自定义的协议,在服务端也添加判断是来自自定义的客户端
$http .= 'GET display HEADPLAN/0.1' . PHP_EOL;

socket_write($socket, $http);
$buffer = socket_read($socket, 8024);
echo $buffer;

socket_close($socket);
```

**修改服务端**

添加对自定义协议的判断 , 然后直接把$result结果发给客户端 :

```php
# 判断是否为自定义客户端的请求
if (preg_match("/GET\s(.*?)\sHEADPLAN\/0.1/i", $buffer, $matches)) {
    $method = $matches[1];
    $result = $obj->$method();
    socket_write($client, $result);
} else {
    # 遍历前面实例化对象中的方法,拼接个json串
    foreach (get_class_methods($obj) as $method) {
        if ($result != '') $result .= ',';
        $result .= '"method":"' . $method . '"';
    }

    $result = '{' . $result . '}';
    socket_write($client, $html.$result);
}
```

现在 , 在远程运行客户端 , 就能接受到服务端中news类里的方法发来的信息了 .

**简单改造客户端**

通过服务端提供的接口 , 或者说服务端地址 , 直接调用NewsService类中的方法 .

```php
<?php

class RpcClient
{
    public $service = [];
    public $request = '';

    public function __construct($service)
    {
        # 解析服务端地址
        $this->service = parse_url($service);

    }

    public function __call($method, $args)
    {
        $socket = @socket_create(AF_INET, SOCK_STREAM, SOL_TCP);
        # 这里直接用了host地址,也就是IP地址,因为没绑域名
        socket_connect($socket, $this->service['host'], $this->service['port']);

        # 拼接http协议
        $this->request = 'GET '. $this->service['path'] .' HTTP/1.1' . PHP_EOL;
        # 添加自定义协议
        $this->request .= 'GET ' . $method . ' HEADPLAN/0.1' . PHP_EOL;

        socket_write($socket, $this->request);

        $buffer = socket_read($socket, 8024);

        socket_close($socket);

        return $buffer;
    }
}

$client = new RpcClient('http://127.0.0.1:9933/service/news.php');
echo $client->display();
echo $client->show();
```

这里没有判断方法存在 , 也没有使用\_\_call的第二个参数相关的内容 , 这里不再延伸 , 只做后期使用框架进行理解 . 

