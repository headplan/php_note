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

