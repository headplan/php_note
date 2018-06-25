# 流式访问

PHP用户空间中所有的文件I/O处理都是通过php 4.3引入的php流包装层处理的。在内部，扩展代码可以选择使用stdio或posix文件处理和本地文件系统或伯克利域套接字进行通信，或者也可以调用和用户空间流I/O相同的API。

#### 流的概念

通常, 直接的文件描述符相比调用流包装层消耗更少的CPU和内存; 然而, 这样会将实现某个特定协议的所有工作都堆积到作为扩展开发者的你身上. 通过挂钩到流包装层, 你的扩展代码可以透明的使用各种内建的流包装, 比如HTTP, FTP, 以及它们对应的SSL版本, 另外还有gzip和bzip2压缩包装. 通过include特定的PEAR或PECL模块, 你的代码还可以访问其他协议, 比如SSH2, WebDav, 甚至是Gopher!

**打开流**

尽管是一个统一的API , 但实际上依赖于所需的流的类型 , 有四种不同的路径去打开一个流 . 从用户空间角度来看 , 这四种不同的类别如下 : 

```php
<?php

/**
 * fopen包装操作文件/URI方式指定远程文件类资源
 */
$fp = fopen($url, $mode);
$data = file_get_contents($url);
file_put_contents($url, $data);
$lines = file($url);

/**
 * 传输基于套接字的顺序I/O
 */
$fp = fsockopen($host, $port);
$fp = stream_socket_client($uri);
$fp = stream_socket_server($uri, $options);

/**
 * 目录流
 */
$dir = opendir($url);
$files = scandir($url);
$obj = dir($url);

/**
 * 特殊的流
 */
$fp = tmpfile();
$fp = popen($cmd);
proc_open($cmd, $pipes);
```

无论打开的是什么类型的流 , 他们都存储在一个公共的结构体php\_stream中 . 



