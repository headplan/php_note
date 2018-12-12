# 路由和路由协议

路由器主要负责解析一个请求并且决定什么module、controller、action被请求 ; 同时也定义了一种方法来实现用户自定义路由 , 这也使得它成为最重要的一个MVC组件 .

为了方便自定义路由 , Yaf现在采用了更为灵活的路由器和路由协议分离的模式 . 也就是一个固定不变的路由器 , 配合各种可自定义的路由协议 , 来实现灵活多变的路由策略 .

**路由组件有两个部分** : 路由器\(Yaf\_Router\)和路由协议\(Yaf\_Route\_Abstract\) .

**路由协议**事实上主要负责匹配我们预先定义好的路由协议,意思就是我们只有一个路由器 , 但我们可以有许多路由协议 .

**路由器**主要负责管理和运行路由链 , 它根据路由协议栈倒序依次调用各个路由协议 , 一直到某一个路由协议返回成功以后 , 就匹配成功.

> 路由注册的顺序很重要 , 最后注册的路由协议 , 最先尝试路由 , 这是个陷阱 . 请注意 .

#### 路由执行流程

路由的过程发生在派遣过程的**最开始** , 并且路由解析仅仅发生一次 .

路由过程在控制器动作\(Controller, Action\)被派遣之前被执行 , 一旦路由成功 , 路由器将会把解析出得到的信息传递给请求对象\(Yaf\Request\_Abstract object\) , 这些信息包括moduel、controller、action、用户params等 . 然后派遣器\(Yaf\Dispatcher\)就会按照这些信息派遣正确的控制器动作 .

前面插件中提到的Hook中 , 就有路由器插件的钩子 , 就是routerStartup和routerShutdown , 他们在路由解析前后分别被调用 .

#### 默认情况

默认情况下 , 路由器是Yaf\_Router , 而默认使用的路由协议是Yaf\_Route\_Static , 是基于HTTP路由的 , 它期望一个请求是HTTP请求并且请求对象是使用Yaf\_Request\_Http .

#### 使用路由

不同的路由协议 :

```
Yaf_Route_Simple
Yaf_Route_Supervar
Yaf_Route_Static
Yaf_Route_Map
Yaf_Route_Rewrite
Yaf_Route_Regex
```

在添加路由协议之前 , 必须要得到一个路由器实例 . 通过派遣器的getRouter方法来得到默认的路由器 : 

```php
<?php
# 通过派遣器得到默认的路由器
$router = Yaf\Dispatcher::getInstance()->getRouter();
```



