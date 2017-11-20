# 远程调试

**xdebug.remote\_autostart**

类型:boolean（布尔型）, 默认值: 0

通常，你需要使用一个指定的HTTP GET/POST变量来启动远程调试 . 当此项被设置为1时，Xdebug将会尝试启动远程调试会话并且连接一个客户端 , 即使GET/POST/COOKIE变量不存在.

**xdebug.remote\_enable**

类型:boolean（布尔型）, 默认值: 0

该设置控制Xdebug是否应该尝试连接一个调试客户端，该调试客户端监听的主机和端口通过 xdebug.remote\_host和 xdebug.remote\_port配置项来设置. 如果不能建立到调试客户端的连接，脚本将继续执行，就像该设置被设置为0一样.

**xdebug.remote\_host**

类型:string（字符串）, 默认值: localhost

选择运行调试客户端的主机, 你可以使用一个主机名或者一个IP地址. 如果xdebug.remote\_connect\_back被启用，此设置将被忽略.

**xdebug.remote\_port**

类型:integer（整型）, 默认值: 9000

Xdebug尝试连接的远程主机的端口. 默认情况下，第三方调试客户端和绑定的调试客户端 的端口都是9000.由于有许多客户端都使用这个端口号，最好不要修改默认值.

**xdebug.remote\_connect\_back**

类型:boolean（布尔型）, 默认值: 0, 在Xdebug 2.1 版中引进

如果启用, xdebug\_remote\_host 设置将被忽略, xdebug 将尝试连接到发出 HTTP 请求的客户端。它检查$\_SERVER\['HTTP\_X\_FORWARDED\_FOR'\]和$\_SERVER\['REMOTE\_ADDR'\] 变量以找出要使用的 IP 地址。

**xdebug.remote\_addr\_header**

类型:string（字符串）, 默认值: "" , 在Xdebug 2.4 版中引进

如果将 xdebug remote\_addr\_header 配置为非空字符串 , 则该值在 $SERVER 全局数组中用作键, 以确定使用哪个标头来查找 "连接回" 所使用的 IP 地址或主机名。此设置仅与 xdebug remote\_connect\_back 结合使用, 否则将被忽略。

**xdebug.remote\_handler**

类型:string（字符串）, 默认值: dbgp

可以是’php3’，它将选择较老的PHP 3 风格调试器输出 . GDB启用类似于GDB的调试器接口或者’dbgp’ – 调试器协议. DBGp协议被各种客户端广泛支持 .

**注意 **: Xdebug 2.1和以后的版本只支持’dbgp’协议 . 

**xdebug.remote\_cookie\_expire\_time**

类型:integer（整型）, 默认值: 3600, 在Xdebug 2.1 版中引进

该设置可以用来增加\(或减少\)远程调试会话的存活时间。

**xdebug.remote\_log**

类型:string（字符串）, 默认值:

如果该项被设置为一个值, 它将被用作一个文件名，所有的远程调试通信信息将被记录在该文件中. 文件通过以追加模式打开因此先前的记录不会被覆盖. 没有提供并发保护机制. 文件的格式形如 : 

```
Log opened at 2007-05-27 14:28:15
-> <init xmlns="urn:debugger_protocol_v1" xmlns:xdebug="http://xdebug.org/dbgp/x ... ight></init>

<- step_into -i 1
-> <response xmlns="urn:debugger_protocol_v1" xmlns:xdebug="http://xdebug.org/db ... ></response>
```



