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
    public__construct ( void )
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

**protected $\_return\_response;**

标示着 , 是否在产生响应以后 , 不自动输出给客户端 , 而是返回给调用者 . 可以通过Yaf\_Dispatcher::returnResponse来切换开关状态 . 

protected $\_instantly\_flush;

protected $\_default\_module;

protected $\_default\_controller;

protected $\_default\_action;

