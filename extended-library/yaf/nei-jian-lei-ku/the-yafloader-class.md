# The Yaf\_Loader class

**Yaf\_Loader**类为Yaf提供了自动加载功能的全面解决方案 . 它根据类名中包含的路径信息实现类的定位和自动加载.Yaf\_Loader也提供了对传统的require\_once的替代方案 , 相比传统的require\_once , 因为舍弃对require的支持 , 所以性能能有一丁点小优势 .

在第一次使用的时候 , 将检索Yaf\_Application的实例 ,  Yaf\_Loader实现了单利模式 , 并使用spl\_autoload注册它自己 . 通过 **Yaf\_Loader::getInstance\(\)**返回它的实例 .

Yaf\_Loader加载一个类时仅仅尝试一次 , 如果失败了 , 后面的操作将取决于**yaf.use\_spl\_auload** , 如果这个配置项为On , **Yaf\_Loader::autoload\(\)**将会返回FALSE , 从而把机会让给其他的自动加载功能 . 如果这个配置项为Off\(默认\) , **Yaf\_Loader::autoload\(\)** 将会返回TRUE , 最重要的是将会抛出一个非常有用的警告\(对于找出一个类加载失败非常有用\) .

> 通常保持**yaf.use\_spl\_autoload**保持关闭 , 除非有一些library有自己的autoload机制 , 并且是无法改写的 .

默认情况下 , Yaf\_Loader收集所有library\(类定义的脚本\)储存进在php.ini\(yaf.library\)定义的公共类库之中 .

如果想使用Yaf\_Loader搜索本地类库\(定义在application.ini . 默认情况下是application.directory . "/libraray"\) , 需要使用 `Yaf_Loader::registerLocalNameSpace()`注册本地类前缀 .

```php
# Example自动加载例子
class Foo_Bar_Test =>
// APPLICATION_PATH/library/Foo/Bar/Test.php

class \Foo\Bar\Dummy =>
// APPLICATION_PATH/library/Foo/Bar/Dummy.php

Controller Classes =>
// APPLICATION_PATH/controllers/

Model Classes =>
// ***Model

class DummyPlugin =>
  // APPLICATION_PATH/plugins/Dummy.php

class A_B_TestModel =>
  // APPLICATION_PATH/models/A/B/Test.php
```

文件夹首字母大小写控制 , **yaf.lowcase\_path** . 也可以通过改变配置项yaf.name\_suffix来将类改为通过前缀识别 .

```php
Yaf_Loader {
    /* 属性 */
    protected $_local_ns;
    protected $_library;
    protected $_global_library;
    static $_instance;
    /* 方法 */
    public void autoload ( void )
    public void clearLocalNamespace ( void )
    private void __clone ( void )
    private __construct ( void )
    public static void getInstance ( void )
    public Yaf_Loader getLibraryPath ([ bool $is_global = false ] )
    public void getLocalNamespace ( void )
    public static void import ( void )
    public void isLocalName ( void )
    public void registerLocalNamespace ([ mixed $prefix ] )
    public Yaf_Loader setLibraryPath ( string $directory [, bool $is_global = false ] )
    private void __sleep ( void )
    private void __wakeup ( void )
}
```

#### 属性

**protected $\_local\_ns;**

本地类的类名前缀 , 此属性通过`Yaf_Loader::registerLocalNamespace`来添加新的值 .

**protected $\_library;**

默认情况下 , 它的值是application.directory . "/library" , 可以通过修改application.ini\(application.library\)或者调用`Yaf_Loader::setLibraryPath()`改变它 .

**protected $\_global\_library;**

全局类加载路径 , 属性的值来自php.ini中的yap.library , 可以通过修改php.ini\(yap.library\)或者调用`Yaf_Loader::setLibraryPath()`改变它 .

**static $\_instance;**

Yaf\_Loader实现了单利模式 , 一般的它由Yaf\_Application负责初始化 . 此属性保存当前实例 .

---

#### 方法

```php
private __construct ( void ) - 不能被初始化
private void __clone ( void ) - 不能被克隆
private void __sleep ( void ) - 不能被序列化
private void __wakeup ( void ) - 不能被反序列化
```

---

```php
public static void Yaf_Loader::getInstance ( string  $_library = NULL, string  $_global_library = NULL );
```

获取当前的Yaf\_Loader实例 .

**$\_library**

本地\(自身\)类库目录 , 如果留空 , 则返回已经实例化过的Yaf\_Loader实例 .

**$\_global\_library**

全局类库目录 , 如果留空则会认为和$local\_library\_directory相同 .

```php
$loader = Yaf\Loader::getInstance('abc','def');
dump($loader);
```

---

```php
public static boolean Yaf_Loader::import( string  $file_name );
```

导入一个PHP文件 , 因为Yaf\_Loader::import只是专注于一次包含 , 所以要比传统的require\_once性能好一些 .

成功返回TRUE , 失败返回FALSE .

**$file\_name**

要载入的文件路径 , 可以为绝对路径和相对路径 . 如果为相对路径 , 则会以应用的本地类目录\(yaf.library\)为基目录 .

```php
<?php
# 绝对路径
Yaf_Loader::import("/usr/local/foo.php);

# 相对路径,会在APPLICATION_PATH."/library"下加载
Yaf_loader::import("plugins/User.php");
```

---

```php
public static boolean Yaf_Loader::autoload( string $class_name );
```

载入一个类 , 这个方法被Yaf用作自动加载类的方法 , 当然也可以手动调用 .

**$class\_name**

要载入的类名 , 类名必须包含路径信息 , 也就是下划线分隔的路径信息和类名 . 载入的过程中 , 首先会判断这个类名是否是本地类 , 如果是本地类 , 则使用本地类类库目录 , 否则使用全局类目录 . 然后判断yaf.lowcase\_path是否开启 , 如果开启 , 则会把类名中的路径部分全部小写 . 然后加载 , 执行 .

成功返回TRUE .

> 在php.ini中的yaf.use\_spl\_autoload关闭的情况下 , 即使类没有找到 , Yaf\_Loader::autoload也会返回TRUE , 剥夺其后面的自动加载函数的执行权利 .

```php
<?php
Yaf_Loader::autoload("Baidu_ST_Dummy_Bar");
```



