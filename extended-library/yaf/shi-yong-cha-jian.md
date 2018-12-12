# 使用插件

Yaf支持用户定义插件来扩展Yaf的功能 , 这些插件都是一些类 . 它们都必须继承自Yaf\_Plugin\_Abstract . 插件要发挥功效 , 也必须现实的在Yaf中进行注册 , 然后在适当的时机Yaf就会调用它 .

#### Yaf支持的Hook

Yaf定义了6个Hook :

**routerStartup** : 在路由之前触发 . Hook事件中最早的一个 , 但是一些全局自定的工作, 还是应该放在Bootstrap中去完成 .

**routerShutdown** : 路由结束之后触发 . 此时路由一定正确完成 , 否则这个事件不会触发 .

**dispatchLoopStartup** : 分发循环开始之前被触发 .

preDispatch : 分发之前触发 . 如果在一个请求处理过程中 , 发生了forward , 则这个事件会被触发多次 . 

