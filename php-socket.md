# PHP Socket

> 本地代码 , Learning\_PHP\_Socket

Socket扩展是基于流行的BSD sockets , 实现了和socket通讯功能的底层接口 , 它可以和客户端一样当做一个socket服务器 .

在开始Socket编程之前 , 可以先了解一些基本概念 , 可以查看 :

> [http://www.unixguide.net/network/socketfaq/](http://www.unixguide.net/network/socketfaq/)

完整文档在 :

> [http://php.net/manual/zh/ref.sockets.php](http://php.net/manual/zh/ref.sockets.php)

下面描述的socket函数只是PHP扩展的一部分 , 编译PHP时必须在配置中添加--enable-sockets配置项来启用 .

预定义常量 :

> [http://php.net/manual/zh/sockets.constants.php](http://php.net/manual/zh/sockets.constants.php)

Socket Errors :

Socket扩展编写的目的是提供一个面向功能强大的BSD Socket的可用的接口 . 它能确保这些函数在Win32和Unix平台上都能很好的工作 . 在特定条件下 , 大部分socket函数如果发生错误都会发出一个 E\_WARNING信息描述错误内容 . 有时可能并不会如开发者所愿 . 例如 , 因为连接突然中断 , socket\_read\(\)函数可能会突然发出一个 E\_WARNING . 通常会使用@操作符来压制异常 , 然后在程序中用socket\_last\_error\(\)来捕获错误代码 . 你可以调用socket\_strerror\(\)函数通过错误代码获取错误描述 . 查看函数描述获取更多信息 .

