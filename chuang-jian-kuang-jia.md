# 框架技术

> 参考资料 : [http://symfony.com/doc/current/create\_framework/index.html](http://symfony.com/doc/current/create_framework/index.html)
>
> 中文资料 : [http://www.symfonychina.com/doc/current/create\_framework/index.html](http://www.symfonychina.com/doc/current/create_framework/index.html)

开始之前 , 你需要最新版的PHP , 我知道你是喜新厌旧的 . Web服务器 , 用PHP自带的就好 . 当然Composer是你必备的 , 这是你创作必备的良师益友 , 开始吧 , 给你的框架起个名字 , 建个文件夹 :

```bash
mkdir headplan
cd headplan
composer init
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

引入了组件 , 就可以使用`Request`和`Response`两个类了 . 继续重构代码

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

HttpFoundation还解决了安全问题 .

#### 前端控制器

> 把一个单一PHP脚本暴露给（exposing）末级用户是一个被称之为前端控制器的设计模式 .

为了拥有多个页面可以访问 , 我们可以把前面的代码中公共的部分提取出来 , 创建一个文件引入到新建的页面当中 .

**index.php**

```
require_once __DIR__.'/vendor/autoload.php';

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

$request = Request::createFromGlobals();
$response = new Response();
```

**hello.php**

```
require_once __DIR__.'/index.php';

$input = $request->get('name', 'World');

$response->setContent(sprintf('Hello %s', htmlspecialchars($input, ENT_QUOTES, 'UTF-8')));
$response->send();
```

**about.php**

```
require_once __DIR__.'/index.php';

$response->setContent('About!');
$response->send();
```

对于所有页面 , 我们仍要保留`send()`方法 , 页面也不像个模板 . 还有 , 添加一个新页 , 必须创建一个新的PHP脚本 , 其名字通过URL完全暴露给末级用户 , 也就是PHP脚本名称与客户端URL之间是直接的映射关系 .

但是我们要做的是通过把所有客户端请求发送\(routing\)到一个独立PHP脚本来实现 , 也就是利用前端控制器设计模式 .

**继续重构代码 , 查看commit -m"Front Controller"**

通过自定义map数组和获取请求的pathinfo引入不同的文件 . 其他页面就不需要require入口文件 , 也不需要send\(\)函数响应 , 只写和自己相关的要响应的内容就好了 .

不存在的页面 , 也就是不在map中的文件 , 就会直接返回404状态 , 和Not Found内容 .

现在测试脚本 , 就可以模拟URL请求来测试了

```
$request = Request::create('/hello?name=Baby');
```

**框架目录结构 , 查看commit -m"New Directory"**

```
./
├── composer.json
├── composer.lock
├── public/
│   └── index.php
├── src/
│   └── views/
│       ├── about.php
│       └── hello.php
└── vendor
    ├── autoload.php
    ├── composer/
    └── symfony/
```

新的目录结构 , 我们把about和hello页面放到了views视图目录下 , 所以也要将全部页面转换成“模板” , 现在页面重复的部分就是把内容输出给response , 响应Content的内容 .

```
# 修改入口文件
ob_start();
include $map[$path];
$response->setContent(ob_get_clean());
```

然后把about和hello页面改成标签的方式就可以了 .

> 别忘了修改自动加载和map的引入路径

**让模板更具可读性 , extract请求部分 , 查看commit -m"Extrace Query parameters"**

依靠$map的URL映射 , 把URL从“负责生成响应的”代码解耦 .

#### 添加路由

开启新篇章 .

