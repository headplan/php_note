# 理解MySQL网络协议

这里以MySQL为例 , 连接MySQL服务可以使用TCP协议 , 例如使用MySQL的客户端连接 , 网络连接 . 如果是在本机 , 也可以使用sock协议 .

这里我们使用自己的服务端当成MySQL服务器 , 然后使用MySQL客户端去连接服务 :

```php
$headplan_worker->onConnect = function ($connection)
{
    echo 'I am MySQL' . PHP_EOL;
};
```

在控制台中可以看到 , 输出的结果I am MySQL . 但是 , 后面的onMessage回调没有被触发 . 也就说明 , 我们的客户端并没有向服务端发送任何消息 , 而是先由服务端向客户端发送消息 , 和我们使用浏览器客户端不一样的是 , 刚好相反 . 简单了解一下握手流程 :

#### **MySQL交互过程**

MySQL客户端与服务器的交互主要分为两个阶段 : 握手认证阶段和命令执行阶段 .

**握手认证阶段**

握手认证阶段为客户端与服务器建立连接后进行 , 交互过程如下 :

* 服务器-&gt;客户端 - 握手初始化消息
* 客户端-&gt;服务器 - 登录认证消息
* 服务器-&gt;客户端 - 认证结果消息

**命令执行阶段**

客户端认证成功后 , 会进入命令执行阶段 , 交互过程如下 :

* 客户端-&gt;服务器 - 执行命令消息
* 服务器-&gt;客户端 - 命令执行结果

**MySQL客户端与服务器的完整交互过程**

![](/assets/mysql_jiaohu.png)

更多报文相关内容 :

[https://www.cnblogs.com/davygeek/p/5647175.html](https://www.cnblogs.com/davygeek/p/5647175.html)

通常 , 在理解了MySQL报文之后 , 就可以使用Workerman去实现一个类似MyCat一样的MySQL数据库中间件 , 比如我们有N个数据库 , 通过程序去访问 , 执行一个SQL语句 , 到底是由哪一台服务器去执行 ,  就是我们的中间件 , 也可以理解为我们用Workerman去创建的那个服务 , 然后由其决定使用哪一个MySQL服务器 , 这里就要让这个中间件服务去构建一个完整并且合法的报文去和我们的客户端交互了 .

> 这里只为使用Workerman代理做铺垫 , MySQL报文 , 以及中间件实现等内容在MySQL\_Note中查看 .

#### 代理

MySQL数据库通常只能在内容访问 , 外网一般都不能进行访问 , 这里就用到了代理 . 一般都会通过内网等防火墙设置 , 让固定的web服务器去访问 . 现在 , 我们要使用本地开发的程序去访问线上的真实的内网数据库 , 又不去映射防火墙 , 就可以使用Workerman创建一个代理 , 去访问真实的数据库 .

在手册中 , 已经提供了一个例子 , 在Workerman作为客户端中 , 可以找到 , workerman作为mysql代理 .

```php
<?php

use Workerman\Worker;
use Workerman\Connection\AsyncTcpConnection;

require_once __DIR__ . '/Workerman/Autoloader.php';

# 真实的mysql地址,假设这里是本机3306端口
$REAL_MYSQL_ADDRESS = 'tcp://127.0.0.1:3306';

# 代理监听本地9933端口
$proxy = new Worker('tcp://0.0.0.0:9933');

$proxy->onConnect = function ($connection)
{
    global $REAL_MYSQL_ADDRESS;

    # 异步建立一个到实际mysql服务器的连接
    $connection_to_mysql = new AsyncTcpConnection($REAL_MYSQL_ADDRESS);

    # MySQL连接发来数据时,转发给对应客户端的连接
    $connection_to_mysql->onMessage = function ($connection_to_mysql, $buffer) use ($connection)
    {
        $connection->send($buffer);
    };

    # MySQL连接关闭时,关闭对应的代理到客户端的连接
    $connection_to_mysql->onClose = function ($connection_to_mysql) use ($connection)
    {
        $connection->close();
    };

    # MySQL连接上发生错误时,关闭对应的代理到客户端的连接
    $connection_to_mysql->onError = function ($connection_to_mysql) use ($connection)
    {
        $connection->close();
    };

    # 执行异步连接
    $connection_to_mysql->connect();

    # 客户端发来数据时,转发给对应的mysql连接
    $connection->onMessage = function ($connection, $buffer) use ($connection_to_mysql)
    {
        $connection_to_mysql->send($buffer);
    };

    # 客户端连接断开时,断开对应的mysql连接
    $connection->onClose = function ($connection) use ($connection_to_mysql)
    {
        $connection_to_mysql->close();
    };

    # 客户端连接发生错误时,断开对应的mysql连接
    $connection->onError = function ($connection) use ($connection_to_mysql)
    {
        $connection_to_mysql->close();
    };
};

# 运行Worker
Worker::runAll();
```

#### 测试

运行上面的代理服务器 , `php mysql_proxy.php start`

然后 , 使用MySQL客户端 , 访问代理的端口 . 

```php
mysql -uroot -P9933 -h127.0.0.1 -p
```



