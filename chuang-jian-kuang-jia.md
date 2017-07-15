# 新建PHP框架

> 参考资料 : [http://symfony.com/doc/current/create\_framework/index.html](http://symfony.com/doc/current/create_framework/index.html)

开始之前 , 你需要最新版的PHP , 我知道你是喜新厌旧的 . Web服务器 , 用PHP自带的就好 . 当然Composer是你必备的 , 这是你创作必备的良师益友 , 开始吧 , 给你的框架起个名字 , 建个文件夹 :

```bash
mkdir phplan
cd phplan
vim index.php
```

第一个应用 , 打声招呼 :

```php
<?php
$input = $_GET['name'];
printf('Hello %s', $input);
```

Run起来 :

```
php -S localhost:8888
```

打开浏览器访问一下[http://localhost:8888/index.php?name=Baby](http://localhost:8888/index.php?name=Baby)

为了让第一个应用在query参数`name`没有被定义在URL中的query string中时不报警告 , 并且避免XSS攻击 . 简单重构一下

```php
<?php
$input = isset($_GET['name']) ? $_GET['name'] : 'World';
header('Content-Type: text/html; charset=utf-8');
printf('Hello %s', htmlspecialchars($input, ENT_QUOTES, 'UTF-8'));
```

#### 关于PHP的HTTP操作

Web代码关乎操作HTTP , 我们的框架的基本原则应该围绕着HTTP协议 . 

> HTTP协议描述了客户端（例如一个浏览器）如何与服务器（我们的框架通过web server运行）互动。客户端和服务器端的对话，通过明确定义的messages，requests和responses而具体化：_客户端发送一个请求到服务器，服务器端基于请求返回一个响应_。
>
> 在PHP中，request（请求）通过多个超全局变量呈现（`$_GET`，`$_POST`，`$_FILE`，`$_COOKIE`，`$_SESSION`...），而response（响应）则被若干函数所生成（`echo`，`header`，`setcookie`...）。

现在我们要做的是 , 通过OO层（Object-Oriented layer）替换掉默认的PHP超全局变量以及功能函数 . 

使用Composer引入Symfony的HttpFoundation组件

```
composer require symfony/http-foundation
```

引入了组件 , 就可以使用`Request`和`Response`两个类了 , 继续重构代码

```php
<?php
# 引入Composer自动加载
require_once __DIR__.'/vendor/autoload.php';

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

# createFromGlobals()方法基于当前PHP全局变量创建了一个Request对象
$request = Request::createFromGlobals();

$input = $request->get('name', 'World');

$response = new Response(sprintf('Hello %s', htmlspecialchars($input, ENT_QUOTES, 'UTF-8')));

# send()方法把Response对象发送回客户端(客户端首先输出HTTP头信息,然后是内容)
$response->send();
```



