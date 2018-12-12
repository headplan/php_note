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

实例化成功之后 , 所有在Bootstrap类中定义的 , 以\_init开头的方法 , 都会被依次调用 , 而这些方法都可以接受一个`Yaf_Dispatcher`实例作为参数 .

> 也可以通过在配置文件中修改`application.bootstrap`来变更Bootstrap类的位置 .

**Bootstrap类的例子**

```php
<?php

class Bootstrap extends Yaf\Bootstrap_Abstract
{
    public function _initConfig()
    {
        $config = Yaf\Application::app()->getConfig();
        Yaf\Registry::set("config", $config);
    }

    public function _initDefaultName(Yaf\Dispatcher $dispatcher)
    {
        $dispatcher->setDefaultModule("Index")->setDefaultController("Index")->setDefaultAction("index");
    }
}
```

> 根据Bootstrap类中方法的定义出现顺序 , 决定了它们的被调用顺序 . 比如对于上面的例子 , `_initConfig`会第一个被调用 .



