# 使用插件

Yaf支持用户定义插件来扩展Yaf的功能 , 这些插件都是一些类 . 它们都必须继承自Yaf\_Plugin\_Abstract . 插件要发挥功效 , 也必须现实的在Yaf中进行注册 , 然后在适当的时机Yaf就会调用它 .

#### Yaf支持的Hook

Yaf定义了6个Hook :

**routerStartup** : 在路由之前触发 . Hook事件中最早的一个 , 但是一些全局自定的工作, 还是应该放在Bootstrap中去完成 .

**routerShutdown** : 路由结束之后触发 . 此时路由一定正确完成 , 否则这个事件不会触发 .

**dispatchLoopStartup** : 分发循环开始之前被触发 .

**preDispatch** : 分发之前触发 . 如果在一个请求处理过程中 , 发生了forward , 则这个事件会被触发多次 .

**postDispatch** : 分发结束之后触发 . 此时动作已经执行结束 , 视图也已经渲染完成 . 和preDispatch类似 , 此事件也可能触发多次 .

**dispatchLoopShutdown** : 分发循环结束之后触发 . 此时表示所有的业务逻辑都已经运行完成 , 但是响应还没有发送 .

#### 定义插件

插件类需要继承自`Yaf\Plugin_Abstract` . 对于前面的Hook的使用 , 只需要在插件类中定义和上面事件同名的方法 , 那么这个方法就会在该事件触发的时候被调用 .

插件方法 , 可以接受俩个参数 , Yaf\_Request\_Abstract实例和Yaf\_Response\_Abstract实例 .

```php
<?php

class UserPlugin extends Yaf\Plugin_Abstract
{
     public function routerStartup(Yaf\Request_Abstract $request, Yaf\Response_Abstract $response) {}
     public function routerShutdown(Yaf\Request_Abstract $request, Yaf\Response_Abstract $response) {}
}
```

#### 注册插件

插件要生效 , 还需要向Yaf\Dispatcher注册 , 一般插件的注册都会放在Bootstrap中进行 . 例如 :

```php
<?php

class Bootstrap extends Yaf\Bootstrap_Abstract
{
    public function _initPlugin(Yaf\Dispatcher $dispatcher)
    {
        $user = new UserPlugin();
        $dispatcher->registerPlugin($user);
    }
}
```



