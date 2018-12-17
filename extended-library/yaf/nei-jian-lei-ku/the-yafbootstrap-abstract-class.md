# The Yaf\_Bootstrap\_Abstract class

Bootstrap是用来在Application运行\(run\)之前做一些初始化工作的机制 . 可以通过继承Yaf\_Bootstrap\_Abstract 来定义自己的Bootstrap类 . 在Bootstrap类中所有以"\_init"开头的公有的方法 , 都会被按照定义顺序依次在Yaf\_Application::bootstrap\(\)被调用的时刻调用 .

> 这些方法 , 都可以接受一个Yaf\_Dispatcher参数 .

```
<?php

class Bootstrap extends Yaf\Bootstrap_Abstract
{
    public function _initConfig()
    {
        //把配置保存起来
        $arrConfig = Yaf\Application::app()->getConfig();
        Yaf\Registry::set('config', $arrConfig);
    }

    public function _initPlugin(Yaf\Dispatcher $dispatcher)
    {
        //注册一个插件
        $objSamplePlugin = new SamplePlugin();
        $dispatcher->registerPlugin($objSamplePlugin);
    }

    public function _initRoute(Yaf\Dispatcher $dispatcher)
    {
        //在这里注册自己的路由协议,默认使用简单路由
    }

    public function _initView(Yaf\Dispatcher $dispatcher)
    {
        //在这里注册自己的view控制器，例如smarty,firekylin
    }

    public function _initHelper()
    {
        Yaf\Loader::import(APPLICATION_PATH . '/system/functions.php');
    }
}
```



