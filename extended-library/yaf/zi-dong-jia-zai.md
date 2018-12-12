# 自动加载

Yaf在自启动的时候 , 会通过SPL注册一个自己的Autoloader , 出于性能的考虑 , 对于框架相关的MVC类 , Yaf Autoloader只以目录映射的方式尝试一次 .

> 但是要注意的一点是 , 从2.1.18开始 , Yaf支持在PHP脚本中触发对Controller的自动加载 , 但是因为Controller的定位需要根据Module路由结果来判断 , 这就造成了在Bootstrap或者RouteStarrup之前 , 无法确定 . 所以 , 对于Controller的加载 , Yaf将只会尝试去加载默认Module的Controller , 也就是只在"{项目路径}/controllers" 目录下寻找 .

**Yaf目录映射规则**

后缀式还是前缀式 , 通过配置中的yaf.name\_suffix来切换 .

控制器\(Controller\) - 默认模块下为{项目路径}/controllers/ , 否则为{项目路径}/modules/{模块名}/controllers/

数据模型\(Model\) - {项目路径}/models/

插件\(Plugin\) - {项目路径}/plugins/

#### 全局类和本地类

Yaf为了方便在一台服务器上部署的不同产品之间共享公司级别的共享库 , 支持全局类和本地类两种加载方式 .

**全局类** - 所有产品之间共享的类 , 这些类库的路径是通过yaf.library在php.ini配置的 . 如果PHP在编译的时候 , 支持了with-config-file-scan-dir,也可以写在单独的yaf.ini中 . 

