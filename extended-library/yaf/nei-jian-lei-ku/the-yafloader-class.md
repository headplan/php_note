# The Yaf\_Loader class

**Yaf\_Loader**类为Yaf提供了自动加载功能的全面解决方案 . 它根据类名中包含的路径信息实现类的定位和自动加载.Yaf\_Loader也提供了对传统的require\_once的替代方案 , 相比传统的require\_once , 因为舍弃对require的支持 , 所以性能能有一丁点小优势 . 

在第一次使用的时候 , 将检索Yaf\_Application的实例 ,  Yaf\_Loader实现了单利模式 , 并使用spl\_autoload注册它自己 . 通过 Yaf\_Loader::getInstance\(\)返回它的实例 . 



