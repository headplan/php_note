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
# 有了路由器实例,就能通过它来添加自定义的路由了
$router->addRoute('myRoute', $route);
$router->addRoute('myRoute1',$route);
```

直接添加在配置中定义我们路由 :

```php
[common]
;自定义路由
;顺序很重要
routes.regex.type="regex"
routes.regex.match="#^/list/([^/]*)/([^/]*)#"
routes.regex.route.controller=Index
routes.regex.route.action=action
routes.regex.map.1=name
routes.regex.map.2=value
;添加一个名为simple的路由协议
routes.simple.type="simple"
routes.simple.controller=c
routes.simple.module=m
routes.simple.action=a
;添加一个名为supervar的路由协议
routes.supervar.type="supervar"
routes.supervar.varname=r

[product : common]
;product节是Yaf默认关心的节, 添加一个名为rewrite的路由协议
routes.rewrite.type="rewrite"
routes.rewrite.match="/product/:name/:value"
```

然后在Bootstrap中通过调用`Yaf_Router::addConfig`添加定义在配置中的路由协议 :

```php
<?php

class Bootstrap extends Yaf\Bootstrap_Abstract
{
    public function _initRoute(Yaf\Dispatcher $dispatcher)
    {
        $router = Yaf\Dispatcher::getInstance()->getRouter();
        # 添加配置中的路由
        $router->addConfig(Yaf\Registry::get("config")->routes);
    }
}
```

路由器提供了一些方法来得到和设置包含在它内部的信息 .

```
getCurrentRoute(); # 在路由结束以后,获取起作用的路由协议
# 看函数基本意思也就知道
getRoute();
getRoutes();
```

#### 自定义路由协议

自定义路由协议 , 实现`Yaf_Route_Interface`接口即可 . 

