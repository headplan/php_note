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

