# 新建PHP框架

> 参考资料 : http://symfony.com/doc/current/create\_framework/index.html

开始之前 , 你需要最新版的PHP , 我知道你是喜新厌旧的 . Web服务器 , 用PHP自带的就好 . 当然Composer是你必备的 , 这是你创作必备的良师益友 , 开始吧 , 给你的框架起个名字 , 建个文件夹 :

```bash
mkdir phplan
cd phplan
vim index.php
```

第一个应用 , 打声招呼 :

```
<?php
$input = $_GET['name'];
printf('Hello %s', $input);
```

Run起来 :

```
php -S localhost:8888
```

打开浏览器访问一下[http://localhost:8888/index.php?name=Baby](http://localhost:8888/index.php?name=Baby)

