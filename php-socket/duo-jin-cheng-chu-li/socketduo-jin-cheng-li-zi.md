# Socket多进程例子

这里利用sleep函数模拟进程繁忙的情况 . 使用浏览器作为客户端 , 同时访问就会被卡主 , 因为之前的程序都是单进程的 . 

**服务端代码**

这里在请求中设置sleep参数 , 也就是url中带有sleep参数 , 就sleep\(10\)秒 . 

```php
<?php

if (! extension_loaded('sockets')) {
    die('没扩展哦?');
}

function check_socket($socket)
{
    if ($socket === false) {
        $errorcode = socket_last_error();
        $errormsg = socket_strerror($errorcode);

        return "Socket error with: [$errorcode] $errormsg" . PHP_EOL;
    }
    return 'OK' . PHP_EOL;
}

# 正确的创建
$socket = @socket_create(AF_INET, SOCK_STREAM, SOL_TCP);
echo check_socket($socket);

# 绑定名字
$ip = '127.0.0.1';
$bind = @socket_bind($socket, $ip, 9933);
echo check_socket($bind);

socket_listen($socket, 10);

while (true) {
    $client = socket_accept($socket);
    $buffer = socket_read($client, 1024);
    echo $buffer;

    $html = "HTTP/1.1 200 OK" . PHP_EOL
        . "Content-Type: text/html; charset=utf-8" . PHP_EOL
        . PHP_EOL;

    if (preg_match("/sleep/i", $buffer, $matches)) {
        sleep(10);
        $html .= "繁忙等待";
        socket_write($client, $html);
    } else {
        $html .= "this is server";
        socket_write($client, $html);
    }

    socket_close($client);
}

socket_close($socket);
```





