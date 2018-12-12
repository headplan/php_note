# 自动加载

Yaf在自启动的时候 , 会通过SPL注册一个自己的Autoloader , 出于性能的考虑 , 对于框架相关的MVC类 , Yaf Autoloader只以目录映射的方式尝试一次 .

> 但是要注意的一点是 , 从2.1.18开始 , Yaf支持在PHP脚本中触发对Controller的自动加载 , 但是因为Controller的定位需要根据Module路由结果来判断 , 这就造成了在Bootstrap或者RouteStarrup之前 , 无法确定 . 所以 , 对于Controller的加载 , Yaf将只会尝试去加载默认Module的Controller , 也就是只在"{项目路径}/controllers" 目录下寻找 .



