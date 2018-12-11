# 快速开始

#### 目录结构

```
+ public
  |- index.php //入口文件
  |- .htaccess //重写规则    
  |+ css
  |+ img
  |+ js
+ conf
  |- application.ini //配置文件   
+ application
  |+ controllers
     |- Index.php //默认控制器
  |+ views    
     |+ index   //控制器
        |- index.phtml //默认视图
  |+ modules //其他模块
  |+ library //本地类库
  |+ models  //model目录
  |+ plugins //插件目录
```

#### 入口文件

```php
<?php
define("APP_PATH",  realpath(dirname(__FILE__) . '/../')); /* 指向public的上一级 */
$app = new Yaf_Application(APP_PATH . "/conf/application.ini");
$app->run();
```

#### 重写规则

```
# for apache (.htaccess)
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule .* index.php

# for nginx
server {
  listen ****;
  server_name  domain.com;
  root   document_root;
  index  index.php index.html index.htm;

  if (!-e $request_filename) {
    rewrite ^/(.*)  /index.php/$1 last;
  }
}

# for lighttpd
$HTTP["host"] =~ "(www.)?domain.com$" {
  url.rewrite = (
     "^/(.+)/?$"  => "/index.php/$1",
  )
}
```

#### 配置文件

在Yaf中 , 配置文件支持继承 , 支持分节 . 并对PHP的常量进行支持 . 不用担心配置文件太大造成解析性能问题 , 因为Yaf会在第一个运行的时候载入配置文件 , 把格式化后的内容保持在内存中 . 直到配置文件有了修改 , 才会再次载入 .

**application/conf/application.ini**

```
[product]
;支持直接写PHP中的已定义常量
application.directory=APP_PATH "/application/"
```

#### 默认控制器

在Yaf中 , 默认的模块/控制器/动作 , 都是以Index命名的 , 当然 , 这是可通过配置文件修改的 .

对于默认模块 , 控制器的目录是在application目录下的controllers目录下 , Action的命名规则是"名字+Action"

**application/controllers/Index.php**

```php
<?php
class IndexController extends Yaf_Controller_Abstract {
   /* default action */
   public function indexAction() {
       $this->_view->word = "hello world";
       //or
       // $this->getView()->word = "hello world";
   }
}
```

#### 视图文件

Yaf支持简单的视图引擎 , 并且支持用户自定义自己的视图引擎 , 比如Smarty .

对于默认模块 , 视图文件的路径是在application目录下的views目录中以小写的action名的目录中 .

**application/views/index/index.phtml**

```markdown
<html>
 <head>
   <title>Hello World</title>
 </head>
 <body>
   <?php echo $word;?>
 </body>
</html>
```



