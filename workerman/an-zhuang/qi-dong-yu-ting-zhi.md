# 启动与停止

注意Workerman启动停止等命令都是在命令行中完成的 . 要启动Workerman , 首先需要有一个启动入口文件 , 里面定义了服务监听的端口及协议 .

先克隆源码 :

```
git clone https://github.com/walkor/Workerman.git
```

然后测试官方给的三个例子 .

#### 使用HTTP协议对外提供Web服务

**创建http\_test.php文件**

```php
<?php

use Workerman\Worker;
require_once __DIR__ . '/Workerman/Autoloader.php';

# 创建一个监听9933端口的http协议
$http_worker = new Worker('http://0.0.0.0:9933');

# 启动4个进程对外服务
$http_worker->count = 4;

# 接收到浏览器发送的数据时回复Hello Client给浏览器
$http_worker->onMessage = function ($connection, $data) {
    # 像浏览器发送消息
    $connection->send('Hello Client');
};

# 运行Worker
Worker::runAll();
```

**命令行启动**

```
php http_test.php start
```

#### 使用WebSocket协议对外提供服务

```php
<?php

use Workerman\Worker;
require_once __DIR__ . '/Workerman/Autoloader.php';

# 和上一个例子的内容基本相同,除了协议不同.
$http_worker->count = 4;

$http_worker->onMessage = function ($connection, $data) {
    $connection->send('Hello Client', $data);
};

Worker::runAll();
```

在浏览器的控制台测试 :

```js
# 假设服务端ip为127.0.0.1
ws = new WebSocket("ws://127.0.0.1:9933");
ws.onopen = function() {
    alert("连接成功");
    ws.send('Tom');
    alert("给服务端发送一个字符串:Tom");
};
ws.onmessage = function(e) {
    alert("收到服务端的消息：" + e.data);
};
```

#### 直接使用TCP传输数据

**创建tcp\_test.php**

```php
<?php

use Workerman\Worker;
require_once __DIR__ . '/Workerman/Autoloader.php';

$http_worker = new Worker('tcp://0.0.0.0:9933');

$http_worker->count = 4;

$http_worker->onMessage = function ($connection, $data) {
    $connection->send('Hello ' . $data);
};

Worker::runAll();
```

**使用telnet测试**

```
telnet 127.0.0.1 2347
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.
Tom
Hello Tom
```

输入Tom , 服务端就会说Hello Tom .

#### 相关命令

通过前面的例子已经知道了启动命令是什么 , 下面整理一下相关的命令 :

以Debug方式启动

```
php start.php start
```

以守护进程方式启动

```
php start.php start -d
```

停止

```
php start.php stop
```

重启

```
php start.php restart
```

平滑重启

```
php start.php reload
```

查看状态

```
php start.php status
```

查看连接状态\(需要Workerman版本&gt;=3.5.0\)

```
php start.php connections
```

#### debug和daemon方式区别

* 以debug方式启动 , 代码中echo、var\_dump、print等打印函数会直接输出
* 以daemon方式启动 , 代码中echo、var\_dump、print等打印会默认重定向到/dev/null文件 , 可以通过设置`Worker::$stdoutFile = '/your/path/file';`来设置这个文件路径
* 以debug方式启动 , 终端关闭后workerman会随之关闭并退出
* 以daemon方式启动 , 终端关闭后workerman继续后台正常运行

#### 什么是平滑启动

平滑重启不同于普通的重启 , 平滑重启可以做到在不影响用户的情况下重启服务 , 以便重新载入PHP程序 , 完成业务代码更新 . 

平滑重启一般应用于业务更新或者版本发布过程中 , 能够避免因为代码发布重启服务导致的暂时性服务不可用的影响 . 

**注意 : 只有子进程运行过程中载入的文件支持reload , 主进程载入的文件不支持reload。或者说Worker::runAll执行完后workerman运行过程中动态加载的文件支持reload，Worker::runAll执行前就载入的文件代码不支持reload**

#### 平滑重启原理

WorkerMan分为主进程和子进程 , 主进程负责监控子进程 , 子进程负责接收客户端的连接和连接上发来的请求数据 , 做相应的处理并返回数据给客户端 . 当业务代码更新时 , 其实我们只要更新子进程 , 便可以达到更新代码的目的 . 

当WorkerMan主进程收到平滑重启信号时 , 主进程会向其中一个子进程发送安全退出\(让对应进程处理完毕当前请求后才退出\)信号 , 当这个进程退出后 , 主进程会重新创建一个新的子进程\(这个子进程载入了新的PHP代码\) , 然后主进程再次向另外一个旧的进程发送停止命令 , 这样一个进程一个进程的重启 , 直到所有旧的进程全部被置换为止 . 

我们看到平滑重启实际上是让旧的业务进程逐个退出然后并逐个创建新的进程做到的 . 为了在平滑重启时不影响客用户 , 这就要求进程中不要保存用户相关的状态信息 , 即业务进程最好是无状态的 , 避免由于进程退出导致信息丢失 . 



