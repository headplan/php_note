# 开发规范

应用程序目录可以放到任意位置 .

#### 入口文件

和nginx+PHP-FPM下的PHP应用程序一样 , WorkerMan中的应用程序也需要一个入口文件 , 入口文件名没有要求 , 并且这个入口文件是以PHP Cli方式运行的 .

入口文件中是创建监听进程相关的代码 , 例如 :

```php
<?php

use Workerman\Worker;
require_once __DIR__ . '/Workerman/Autoloader.php';

// 创建一个Worker监听2345端口，使用http协议通讯
$http_worker = new Worker("http://0.0.0.0:2345");

// 启动4个进程对外提供服务
$http_worker->count = 4;

// 接收到浏览器发送的数据时回复hello world给浏览器
$http_worker->onMessage = function($connection, $data)
{
    // 向浏览器发送hello world
    $connection->send('hello world');
};

Worker::runAll();
```

#### WorkerMan中的代码规范

类采用首字母大写的驼峰式命名 , 类文件名称必须与文件内部类名相同 , 以便自动加载 :

```php
class UserInfo
{
...
```

使用命名空间 , 命名空间名字与目录路径对应 , 并以开发者的项目根目录为基准 . 例如项目MyApp/ , 类文件MyApp/MyClass.php因为在项目根目录 , 所以命名空间省略 . 如果类文件MyApp/Protocols/MyProtocol.php因为MyProtocol.php在MyApp项目的Protocols目录下 , 所以要加上命名空间`namespace Protocols;`



