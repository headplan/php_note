# 理解HTTP协议

浏览器其实就是一个支持HTTP协议的客户端 , 改造一下前面PHP Socket代码 .

先看一下HTTP通信的基本步骤 , 也就是浏览器和服务器之间的通信过程 :

**1. 建立TCP连接**

在HTTP工作开始之前 , Web浏览器首先要通过网络与Web服务器建立连接 , 该连接是通过TCP来完成的 , 该协议与IP协议共同构建Internet , 即著名的TCP/IP协议族 , 因此Internet又被称作是TCP/IP网络 . HTTP是比TCP更高层次的应用层协议 , 根据规则 , 只有低层协议建立之后才能进行更高层协议的连接 , 因此 , 首先要建立TCP连接 , 一般TCP连接的端口号是80 .

这里的客户端换成了 , 浏览器 , 也就是前面代码中客户端代码的这个部分 :

```
$socket = @socket_create(AF_INET, SOCK_STREAM, SOL_TCP);
$ip = '127.0.0.1';
socket_connect($socket, $ip, 9933);
```

**2. Web浏览器向Web服务器发送请求命令**

一旦建立了TCP连接 , Web浏览器就会向Web服务器发送请求命令 .

**3. Web浏览器发送请求头信息**

浏览器发送其请求命令之后 , 还要以头信息的形式向Web服务器发送一些别的信息 , 之后浏览器发送了一空白行来通知服务器 , 它已经结束了该头信息的发送 .

这里在浏览器中输入地址 , 即IP地址+端口号 , 然后访问 , 在运行的server.php脚本下 , 即可看到浏览器发送的请求信息 :

```
GET / HTTP/1.1
Host: 127.0.0.1:9933
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/66.0.3359.181 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,la;q=0.7
Cookie: lang=zh-CN
```

**4. Web服务器响应**

客户机向服务器发出请求后 , 服务器会客户机回送应答 , HTTP/1.1 200 OK , 应答的第一部分是协议的版本号和应答状态码 .

**5. Web服务器发送响应头信息**

正如客户端会随同请求发送关于自身的信息一样 , 服务器也会随同响应向用户发送关于它自己的数据及被请求的文档 .

**6. Web服务器向浏览器发送数据**

Web服务器向浏览器发送头信息后 , 它会发送一个空白行来表示头信息的发送到此为结束 , 接着 , 它就以Content-Type应答头信息所描述的格式发送用户所请求的实际数据 .

**7. Web服务器关闭TCP连接**  
一般情况下 , 一旦Web服务器向浏览器发送了请求数据 , 它就要关闭TCP连接 , 然后如果浏览器或者服务器在其头信息加入了这行代码 : Connection:keep-alive . TCP连接在发送后将仍然保持打开状态 , 于是 , 浏览器可以继续通过相同的连接发送请求 . 保持连接节省了为每个请求建立新连接所需的时间 , 还节约了网络带宽 .

现在修改之前的服务端代码 , 把响应部分的报文内容添加上 , 就能在浏览器里访问了 .

> 查看相关代码 .



