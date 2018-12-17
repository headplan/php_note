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



