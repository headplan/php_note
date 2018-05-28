# 理解TCP协议

![](/assets/qicengmoxing.png)

#### 对应的TCP/IP五层模型

![](/assets/wucengmxing.png)

TCP（Transmission Control Protocol 传输控制协议）是一种面向连接的、可靠的、基于字节流的传输层通信协议 .

Socket可以理解为应用层与传输层沟通的API .

#### 再来一遍三次握手

第一次握手：客户端发送syn包\(syn=j\)到服务器，并进入SYN\_SEND状态，等待服务器确认；

第二次握手：服务器收到syn包，必须确认客户的SYN（ack=j+1），同时自己也发送一个SYN包（syn=k），即SYN+ACK包，此时服务器进入SYN\_RECV状态；

第三次握手：客户端收到服务器的SYN＋ACK包，向服务器发送确认包ACK\(ack=k+1\)，此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手。

**使用抓包工具 , 看一下握手的具体流程 . **

抓包工具可以使用图形界面的Wireshark , 或者是tcpdump .

使用Wireshark , 选择捕获的网口卡 , 也就是抓本地的包 , 本地应该监听lo0 , 即链路层头是BSD loopback . 

也可以上传到服务器上查看 . 

![](/assets/sanciwoshouzhuabao.png)



