# 理解WebSocket协议

WebSocket 协议在2008年诞生 , 2011年成为国际标准 . 所有浏览器都已经支持了 .

它的最大特点就是 , 服务器可以主动向客户端推送信息 , 客户端也可以主动向服务器发送信息 , 是真正的双向平等对话 , 属于服务器推送技术的一种 .

![](/assets/websocket.png)

其他特点包括：

* 建立在 TCP 协议之上，服务器端的实现比较容易。

* 与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。

* 数据格式比较轻量，性能开销小，通信高效。

* 可以发送文本，也可以发送二进制数据。

* 没有同源限制，客户端可以与任意服务器通信。

* 协议标识符是ws（如果加密，则为wss），服务器网址就是 URL。

```
ws://example.com:80/some/path
```

![](/assets/wws.png)

---

#### 客户端

可以把Websocket理解为浏览器内嵌的Socket客户端 .

```js
# 尝试连接服务端
let ws = new WebSocket("ws://localhost:9933");

# 其他API
ws.onopen = function(evt) {
    console.log("Connection open ...");
    ws.send("Hello WebSockets!");
};

ws.onmessage = function(evt) {
    console.log( "Received Message: " + evt.data);
    ws.close();
};

ws.onclose = function(evt) {
    console.log("Connection closed.");
};
```

#### 服务端握手

客户端指定服务器发送一个握手请求 , 如果服务器返回合法的http头 , 则握手成功 . 也就是前面的`new WebSocket`

* 客户端会发送一个字段“Sec-WebSocket-Key:XXXX”
* 服务端需要截取此值 . 把该值累加字符串258EAFA5-E914-47DA-95CA-C5AB0DC85B11
* 然后进行sha1
* 最后再base64\_encode
* 再拼写正确的协议 , 响应给客户端 . 

```php
if (preg_match("/Sec-WebSocket-Key: (.*)\r\n/i",$buf,$match)) {
    $key = base64_encode(sha1($match[1] . '258EAFA5-E914-47DA-95CA-C5AB0DC85B11',true));
}
```

最后拼好的协议内容 :

```php
$res= "HTTP/1.1 101 Switching Protocol".PHP_EOL
    . "Upgrade: WebSocket" . PHP_EOL
    . "Connection: Upgrade" . PHP_EOL
    . "WebSocket-Location: ws://127.0.0.1:9090" . PHP_EOL
    . "Sec-WebSocket-Accept: " . $key . PHP_EOL . PHP_EOL; // 注意这里是两个换行
```

然后注释掉socket\_close\(\) , 在websocket中 , 服务端一般不主动关闭连接 .

再次重启服务 , 刷新客户端 , 握手成功 .

#### 交互过程

握手成功后的交互 , 就稍微复杂一些了 :

![](/assets/websockjaohu.png)

**第一个字节**

最高位用于描述消息是否结束,如果为1则该消息为消息尾部,如果为零则还有后续数据包;后面3位是用于扩展定义的,如果没有扩展约定的情况则必须为0

**第二个字节**

消息的第二个字节主要用一描述掩码和消息长度,最高位用0或1来描述是否有掩码处理 .

**服务端的信息处理**

```php
socket_write($client, buildMsg("Hi,WebSocket!"));

function buildMsg($msg)
{
    $frame = [];
    $frame[0] = '81';
    $len = strlen($msg);
    if ($len < 126) {
        $frame[1] = $len < 16 ? '0' . dechex($len) : dechex($len);
    } else if ($len < 65025) {
        $s = dechex($len);
        $frame[1] = '7e' . str_repeat('0', 4 - strlen($s)) . $s;
    } else {
        $s = dechex($len);
        $frame[1] = '7f' . str_repeat('0', 16 - strlen($s)) . $s;
    }
    $data = '';
    $l = strlen($msg);
    for ($i = 0; $i < $l; $i++) {
        $data .= dechex(ord($msg{$i}));
    }
    $frame[2] = $data;
    $data = implode('', $frame);
    return pack("H*", $data);
}
```

现在 , 客户端的 , 也就是ws , 已经可以获取到信息了 :

```js
ws.onmessage = function(evt) {
    console.log( "Received Message: " + evt.data);
    ws.close();
};
```

**接收客户端发来的消息**

同样 , 消息也需要解析 :

```php
function getMsg($buffer)
{
    $res = '';
    $len = ord($buffer[1]) & 127;
    if ($len === 126) {
        $masks = substr($buffer, 4, 4);
        $data = substr($buffer, 8);
    } else if ($len === 127) {
        $masks = substr($buffer, 10, 4);
        $data = substr($buffer, 14);
    } else {
        $masks = substr($buffer, 2, 4);
        $data = substr($buffer, 6);
    }
    for ($index = 0; $index < strlen($data); $index++) {
        $res .= $data[$index] ^ $masks[$index % 4];
    }
    return $res;
}
```

在客户端添加一个按钮 , 来触发消息的发送 :

```js
<button onclick="send()">发送消息</button>
function send()
{
    ws.send('Hi,Server!');
}
```

但是 , 现在是收不到消息的 . 而且 , 刷新客户端页面 , 可以在控制台中看到 , 连接被中断 , 这个 , 也需要在服务端进行处理 . 看一下服务端代码 :

```
$client = socket_accept($socket);
$buffer = socket_read($client, 9999);
```

首先 , 我们连入了ws客户端 , 并且读取了它 , 之后 , 我们并没有关闭 :

```
//socket_close($client);
```

所以 , 我们一直都在连接同一个socket\_accept . 先来看一个Socket函数 .

#### socket\_select

在超时范围内 , 在给定的socket数组上运行select\(\)系统调用 .

```php
int socket_select ( array &$read , array &$write , array &$except , int $tv_sec [, int $tv_usec = 0 ] )
```

socket\_select\(\)接收连入的socket数组并等待它们更改状态 . 这些socket资源数组实际上是所谓的文件描述符集 , 可以理解为$read,$write,$except . 监视他们的状态 .

PHP的socket\_select函数也是调用系统的select函数实现的 . PHP中socket\_select\(\)函数传入的read和write数组是引用传入的 , 所以每次调用socket\_select\(\)后read和write或者except数组中会包含最新的可以使用的资源数组 . 传入的是要监视的 , 而调用socket\_select后得到的是可以用的 .

**read**

在读取数组中列出的socket将被监视 , 以查看字符是否可用于读取\(更确切地说 , 查看读取是否不会阻塞\) . 特别是 , 在文件末尾还可以使用套接字资源 . 在这种情况下 , socket\_read \(\) 将返回一个 "零"长度字符串 . 

**write**

将监视写入数组中列出的套接字 , 以查看写入是否不会阻塞 . 

**except**

除了数组中列出的套接字其他的都将被监视异常 . 

**tv\_sec**

tv\_sec 和 tv\_usec 一起形成超时参数 . 超时是 socket\_select\(\) 返回之前经过的时间量的上界 . tv\_sec 可能为零 , 导致 socket\_select\(\) 立即返回 . 这对轮询很有用 . 如果 tv\_sec 为 NULL \(无超时\) , socket\_select\(\) 可以无限期地阻塞 . 

