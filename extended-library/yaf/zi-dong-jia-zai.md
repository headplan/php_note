# 自动加载

Yaf在自启动的时候 , 会通过SPL注册一个自己的Autoloader , 出于性能的考虑 , 对于框架相关的MVC类 , Yaf Autoloader只以目录映射的方式尝试一次 . 



