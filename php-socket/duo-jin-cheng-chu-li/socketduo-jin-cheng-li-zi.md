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

运行服务端 , 访问 :

```
http://127.0.0.1:9933/?sleep
http://127.0.0.1:9933/
```

就会发现 , 第一个链接一直在等待 , 第二个也同样在等待 , 因为是单进程 . 现在来改造成多进程 , 根据官方给的简单示例改造一下代码 :

```php
while (true) {
    $client = socket_accept($socket);

    # 创建子进程
    $pid = pcntl_fork();

    # 父进程和子进程都会执行下面代码
    if ($pid == -1) {
        # 错误处理:创建子进程失败时返回-1.
        exit('创建子进程失败');
    } elseif ($pid) {
        # 父进程会得到子进程号,所以这里是父进程执行的逻辑
        # 等待子进程中断,防止子进程成为僵尸进程
        # pcntl_wait($status);

        socket_close($client);
    } else {
        # 子进程得到的$pid为0,所以这里是子进程执行的逻辑
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
}
```

两个查看进程的命令 :

```
# 根据名称查询进程
ps -ef | grep pcntl_server.php | grep -v grep

# 根据占用端口站看进程
netstat -anp | grep 9933
```

查看进程可以看到 , 刷新浏览器就会增加 , 前面的sleep\(10\)也解决了 . 这就和apache还有nginx的多进程一样了 , 还有个问题就是僵尸进程的问题 , 一般apache或者nginx都会有设置最大进程数的配置 , 这里可以使用多进程函数pcntl\_wait简单模拟一下 : 

```

```



