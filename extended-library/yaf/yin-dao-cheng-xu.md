# 引导程序

Bootstrap也叫做引导程序 . 它是Yaf提供的一个全局配置的入口 , 在Bootstrap中 , 可以做很多全局自定义的工作 .

#### 引导的使用

在一个`Yaf_Application`被实例化之后 , 运行`Yaf_Application::run`之前 , 可以运行`Yaf_Application::bootstrap` .

```php
<?php
$app = new Yaf_Application("conf.ini");
$app->bootstrap()->run();
```

当Bootstrap被调用的时刻 , `Yaf_Application`就会默认的在`APPLICATION_PATH`下 , 寻找`Bootstrap.php` , 而这个文件中 , 必须定义一个Bootstrap类 , 而这个类也必须继承自`Yaf_Bootstrap_Abstract` .

