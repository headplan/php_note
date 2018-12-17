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

---

`public static void app ( void )`- 获取当前的Yaf\_Application实例 , 方法不需要参数 .

```php
<?php
$app = new Yaf\Application(APPLICATION_PATH . "/config/application.ini");
$app->bootstrap()->run();
dump(Yaf\Application::app());
```

public void bootstrap \(\[ Yaf\_Bootstrap\_Abstract $bootstrap \] \) -

