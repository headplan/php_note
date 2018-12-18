# The Yaf\_Dispatcher class

**Yaf\_Dispatcher**用于初始化处理请求的运行环境 , 它协调路由来的请求 , 并分发和执行发现的动作 , 然后收集动作产生的响应 , 输出响应给请求者 , 并在整个过程完成以后返回响应 .

**Yaf\_Dispatcher**是单例模式运行的 , 也就是说自始至终只生成一个**Yaf\_Dispatcher**实例 , 因此 , 可以把它看成是在分发过程中生成的对象的注册表 , 可以从中获取到分发过程中产生的对象 .

```php
final Yaf_Dispatcher {
    /* 属性 */
    protected $_router;
    protected $_view;
    protected $_request;
    protected $_plugins;
    protected static $_instance;
    protected $_auto_render;
    protected $_return_response;
    protected $_instantly_flush;
    protected $_default_module;
    protected $_default_controller;
    protected $_default_action;
    /* 方法 */
    public Yaf_Dispatcher autoRender ( bool $flag )
    public Yaf_Dispatcher catchException ([ bool $flag ] )
    private void __clone ( void )
    public __construct ( void )
    public bool disableView ( void )
    public Yaf_Response_Abstract dispatch ( Yaf_Request_Abstract $request )
    public Yaf_Dispatcher enableView ( void )
    public Yaf_Dispatcher flushInstantly ( bool $flag )
    public Yaf_Application getApplication ( void )
    public static Yaf_Dispatcher getInstance ( void )
    public Yaf_Request_Abstract getRequest ( void )
    public Yaf_Router getRouter ( void )
    public Yaf_View_Interface initView ( string $templates_dir [, array $options ] )
    public Yaf_Dispatcher registerPlugin ( Yaf_Plugin_Abstract $plugin )
    public Yaf_Dispatcher returnResponse ( bool $flag )
    public Yaf_Dispatcher setDefaultAction ( string $action )
    public Yaf_Dispatcher setDefaultController ( string $controller )
    public Yaf_Dispatcher setDefaultModule ( string $module )
    public Yaf_Dispatcher setErrorHandler ( call $callback , int $error_types )
    public Yaf_Dispatcher setRequest ( Yaf_Request_Abstract $request )
    public Yaf_Dispatcher setView ( Yaf_View_Interface $view )
    private void __sleep ( void )
    public Yaf_Dispatcher throwException ([ bool $flag ] )
    private void __wakeup ( void )
}
```

#### 属性

**protected $\_router;**

路由器 , 在Yaf0.1之前 , 路由器是可更改的 , 但是Yaf0.2以后 , 随着路由器和路由协议的分离 , 各种路由都可以通过配置路由协议来实现 , 也就取消了自定义路由器的功能 .

**protected $\_view;**

当前的视图引擎 , 可以通过Yaf\_Dispatcher::setView来替换视图引擎为自定义视图引擎\(比如Smary/Firekylin等常见引擎\)

**protected $\_request;**

当前的请求 .

**protected $\_plugins;**

已经注册的插件 , 插件一经注册 , 就不能更改和删除 .

**protected static $\_instance;**

Yaf\_Dispatcher实现了单利模式 , 此属性保存当前实例 .

**protected $\_auto\_render;**

标示着 , 是否开启自动渲染 . 通过Yaf\_Dispatcher::autoRender来切换开关状态 .

**protected $\_return\_response;**

标示着 , 是否在产生响应以后 , 不自动输出给客户端 , 而是返回给调用者 . 可以通过Yaf\_Dispatcher::returnResponse来切换开关状态 .

**protected $\_instantly\_flush;**

标示着, 是否在有输出的时候 , 直接响应给客户端 , 不写入Yaf\_Response\_Abstract对象 . 如果此属性为TRUE , 那么将忽略Yaf\_Dispatcher::$\_return\_response

**protected $\_default\_module;**

默认的模块名 , 在路由的时候 , 如果没有指明模块 , 则会使用这个值 , 也可以通过配置文件中的yaf.dispatcher.defaultModule来指定

**protected $\_default\_controller;**

默认的控制器名 , 在路由的时候 , 如果没有指明控制器 , 则会使用这个值 , 也可以通过配置文件中的yaf.dispatcher.defaultController来指定

**protected $\_default\_action;**

默认的动作名 , 在路由的时候 , 如果没有指明动作 , 则会使用这个值 , 也可以通过配置文件中的yaf.dispatcher.defaultAction来指定

---

#### 方法

```php
private void __clone ( void ) - 不能被克隆
private void __sleep ( void ) - 不能被序列化
private void __wakeup ( void ) - 不能被反序列化
```

```php
public __construct ( void ) - Yaf_Dispatcher构造函数
```

---

```php
public static Yaf_Dispatcher Yaf_Dispatcher::getInstance ( void )
```

获取当前的Yaf\_Dispatcher实例 .

```php
<?php
$dispatcher = Yaf_Dispatcher::getInstance();
```

---

```php
public bool Yaf_Dispatcher::disableView ( void )
```

关闭自动渲染 . 在一些用户自己会输出信息的情况下使用 .

> 可以在一个action中仅仅返回FALSE来阻止当前action对应视图的自动渲染

```php
<?php

class IndexController extends Yaf_Controller_Abstract
{
    /**
     * Controller的init方法会被自动首先调用
     */  
    public function init()
    {
        /**
         * 如果是Ajax请求, 则关闭HTML输出
         */
        if ($this->getRequest()->isXmlHttpRequest()) {
            Yaf_Dispatcher::getInstance()->disableView();
        }
    }
}
```

---

```php
public Yaf_Dispatcher Yaf_Dispatcher::enableView ( void )
```

开启自动Render . 默认是开启的 , 在动作执行完成以后 , Yaf会自动render以动作名命名的视图模板文件 .

成功返回Yaf\_Dispatcher , 失败返回FALSE .

```php
<?php

class IndexController extends Yaf_Controller_Abstract
{
    /**
     * Controller的init方法会被自动首先调用
     */
    public function init()
    {
        /**
         * 如果不是Ajax请求, 则开启HTML输出
         */
        if (!$this->getRequest()->isXmlHttpRequest()) {
            Yaf_Dispatcher::getInstance()->enableView();
        }
    }
}
```

---

```php
public Yaf_Dispatcher Yaf_Dispatcher::autoRender ( bool $flag )
```

开启/关闭自动渲染功能 . 在开启的情况下\(Yaf默认开启\) , Action执行完成以后 , Yaf会自动调用View引擎去渲染该Action对应的视图模板 .

**$flag**

成功返回Yaf\_Dispatcher , 失败返回FALSE .

```php
<?php

class IndexController extends Yaf_Controller_Abstract
{
    /**
     * Controller的init方法会被自动首先调用
     */
    public function init()
    {
        /**
         * 如果不是Ajax请求, 则开启HTML输出
         */
        if ($this->getRequest()->isXmlHttpRequest()) {
            Yaf_Dispatcher::getInstance()->autoRender(false);
        }
    }
}
```

---

```php
public Yaf_Dispatcher Yaf_Dispatcher::returnResponse ( bool $flag )
```

是否返回Response对象 , 如果启用 , 则Response对象在分发完成以后不会自动输出给请求端 , 而是交给自己控制输出 .

**$flag**

成功返回Yaf\_Dispatcher , 失败返回FALSE

```php
$application = new Yaf_Application("config.ini");
# 关闭自动响应,交给rd自己输出
$response = $application->getDispatcher()->returnResponse(TRUE)->getApplication()->run();
# 输出响应
$response->response();
```

---

```php
public Yaf_Dispatcher Yaf_Dispatcher::flushInstantly ( bool $flag )
```

切换自动响应 . 在Yaf\_Dispatcher::enableView\(\)的情况下 , 会使得Yaf\_Dispatcher调用Yaf\_Controller\_Abstract::display方法 , 直接输出响应给请求端 .

**$flag**

成功返回Yaf\_Dispatcher , 失败返回FALSE

```php
$application = new Yaf_Application("config.ini");
# 立即输出响应
Yaf_Dispatcher::getInstance()->flushInstantly(TRUE);
# 此时会调用Yaf_Controller_Abstract::display方法
$application->run();
```

---

```php
public Yaf_Dispatcher Yaf_Dispatcher::setErrorHandler ( call $callback , int $error_types )
```

设置错误处理函数 , 一般在application.dispatcher.throwException关闭的情况下 , Yaf会在出错的时候触发错误 , 这个时候 , 如果设置了错误处理函数 , 则会把控制交给错误处理函数处理 . 因此 , 当错误发生的时候这个错误处理函数将被调用 .

**$callback**

错误处理函数 , 这个函数需要最少接受俩个参数 : 错误代码\($error\_code\)和错误信息\($error\_message\) , 可选的还可以接受三个参数 :  错误文件\($err\_file\) , 错误行\($err\_line\)和错误上下文\($errcontext\) . 

**$error\_types**

要捕获的错误类型 . 

成功返回Yaf\_Dispatcher , 失败返回FALSE

```php
<?php
/** 
 * 一般可放在Bootstrap中定义错误处理函数
 */
function myErrorHandler($errno, $errstr, $errfile, $errline)
{
    switch ($errno) {
    case YAF_ERR_NOTFOUND_CONTROLLER:
    case YAF_ERR_NOTFOUND_MODULE:
    case YAF_ERR_NOTFOUND_ACTION:
         header("Not Found");
    break;

    default:
        echo "Unknown error type: [$errno] $errstr<br />\n";
        break;
    }

    return true;
}

Yaf_Dispatcher::getInstance()->setErrorHandler("myErrorHandler");
```



