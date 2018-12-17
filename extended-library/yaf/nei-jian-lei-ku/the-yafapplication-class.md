# The Yaf\_Application class

Yaf\_Application代表一个产品/项目 , 是Yaf运行的主导者 , 真正执行的主题 . 它负责接收请求 , 协调路由 , 分发 , 执行 , 输出 .

Yaf\_Application实现了单例模式 . Yaf\_Application不能够被序列化和反序列化 . 使用PHPUnit来为Yaf写一些测试用例的时候 , 可以使用PHPUnit的@backupGlobals注释来控制全局变量的备份和恢复操作 , 来解决序列化的问题 .

```php
final Yaf_Application
{
    /* 属性 */
    protected $config ;
    protected $dispatcher ;
    protected static $_app ;
    protected $_modules ;
    protected $_running ;
    protected $_environ ;
    /* 方法 */
    public static void app ( void )
    public void bootstrap ([ Yaf_Bootstrap_Abstract $bootstrap ] )
    public Yaf_Application clearLastError ( void )
    private void __clone ( void )
    public __construct ( mixed $config [, string $envrion ] )
    public void __destruct ( void )
    public void environ ( void )
    public void execute ( callable $entry , string $... )
    public Yaf_Application getAppDirectory ( void )
    public Yaf_Config_Abstract getConfig ( void )
    public Yaf_Dispatcher getDispatcher ( void )
    public string getLastErrorMsg ( void )
    public int getLastErrorNo ( void )
    public array getModules ( void )
    public void run ( void )
    public Yaf_Application setAppDirectory ( string $directory )
    private void __sleep ( void )
    private void __wakeup ( void )
}
```

#### 属性

* config : 全局配置实例
* dispatcher : Yaf\_Dispatcher实例
* \_app : Yaf\_Application通过特殊的方式实现了单利模式 , 此属性保存当前实例
* \_modules : 存在的模块名, 从配置文件中yap.modules读取
* \_running : bool值 , 指明当前的Yaf\_Application是否已经运行
* \_environ : 当前的环境名 , 也就是Yaf\_Application在读取配置的时候 , 获取的配置节名字

---

#### 方法

`public __construct ( mixed $config [, string $envrion ] )`- 初始化一个Yaf\_Application , 如果$config是一个INI文件 , 那么$envrion指明要读取的配置节 .

**参数**

**$config**

关联数组的配置 , 或者一个指向ini格式的配置文件的路径的字符串 , 或者是一个Yaf\_Config\_Abstract实例 .

如果是一个ini配置文件 , 那配置文件中应该有一个定义了yaf.environ的配置节 . 这个在生产环境中是默认的 .

```asciidoc
[product]
;this one should alway be defined, and have no default value
application.directory=APPLICATION_PATH
...
```

> 使用了ini配置文件作为应用配置的容器 , 可以打开yaf.cache\_config来提升性能

**$envrion**

可以用来切换配置节 .

---

`public void bootstrap ([ Yaf_Bootstrap_Abstract $bootstrap ] )`

指示Yaf\_Application去寻找Bootstrap类 , 并按照声明的顺序 , 执行所有在Bootstrap类中定义的以\_init开头的方法 . 如果没有提供变量$bootstrap , Yaf默认会去application.directory中寻找Bootstrap , 当然这个可以在配置文件中修改 .

**$bootstrap**

可选参数 , 默认会自动寻找引导类 .

```php
<?php 
class Bootstrap extends Yaf_Bootstrap_Abstract 
{ 
    public function _initSession(Yaf_Dispatcher $dispatcher) 
    { 
        $session = new Vendor\Session(); 
        $session->start(); 
    } 

    public function _initDatabase(Yaf_Dispatcher $dispatcher) 
    { 
        $config = Yaf_Application::app()->getConfig()->application->database; 
        Yaf_Registry::set('db', Vendor\Database($config)); 
    } 
}
```

> Bootstrap并不会调用run , 所以还需要在bootstrap以后调用Application::run来运行Yaf\_Application实例

---

`public static void app ( void )`- 获取当前的Yaf\_Application实例 , 方法不需要参数 .

```php
<?php
$app = new Yaf\Application(APPLICATION_PATH . "/config/application.ini");
$app->bootstrap()->run();
dump(Yaf\Application::app());
```

---

`public void Yaf_Application::environ ( void )` - 获取当前Yaf\_Application的环境名

它被定义在yaf.environ , 默认值为"product" .

```php
<?php
$config = array(
    "application" => array(
        "directory" => realpath(dirname(__FILE__)) . "/application",
    ),
);

/** Yaf_Application */
$application = new Yaf_Application($config);
print_r($application->environ());
```

---

`public void Yaf_Application::run ( void )` - 运行一个Yaf\_Application , 开始接受并处理请求 , 分发路由 , 做出相应的响应 . 最终将响应返回给客户端 .

---

`public void Yaf_Application::execute ( callable $entry , string $... )`

这个方法通常用于在cron任务中运行Yaf\_Application . 在cron任务中也可以使用autoloader和Bootstrap机制 .

**$entry**

一个有效的回调函数

**$...**

零个或者多个要传递给函数的参数

```php
<?php
function main($argc, $argv) {
}

$config = array(
    "application" => array(
        "directory" => realpath(dirname(__FILE__)) . "/application",
    ),
);

/** Yaf_Application */
$application = new Yaf_Application($config);
$application->execute("main", $argc,  $argv);
```

---

`public Yaf_Application Yaf_Application::setAppDirectory ( string $directory )`- 改变/设置应用目录 .

`public Yaf_Application Yaf_Application::getAppDirectory ( void )` - 获取应用的目录 . 





`public Yaf_Config_Abstract Yaf_Application::getConfig ( void )` - 获取 Yaf\_Config\_Abstract 的实例 , 读取配置 . 

---

```php
private void __clone ( void ) - Yaf_Application不能被克隆
private void __sleep ( void ) - Yaf_Application不能被序列化
private void __wakeup ( void ) - Yaf_Application不能被反序列化
```



