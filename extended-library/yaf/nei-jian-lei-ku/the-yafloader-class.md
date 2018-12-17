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

文件夹首字母大小写控制 , yaf.lowcase\_path . 也可以通过改变配置项yaf.name\_suffix来将类改为通过前缀识别 .

