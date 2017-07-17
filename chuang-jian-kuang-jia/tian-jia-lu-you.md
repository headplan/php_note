# 路由组件

**参考资料**

* [https://github.com/noahbuscher/Macaw](https://github.com/noahbuscher/Macaw)
* [https://github.com/klein/klein.php](https://github.com/klein/klein.php)
* [https://github.com/laravel/framework](https://github.com/laravel/framework)
* [http://www.symfony.com/doc/current/components/routing.html](http://www.symfony.com/doc/current/components/routing.html)

**路由组件**

我们先来看看Symfony的路由组件 .

```
composer require symfony/routing
```

不同于使用一个URL映射数组 , Routing组件依赖一个`RoutingCollection`实例

```
use Symfony\Component\Routing\RouteCollection; 
$routes = new RouteCollection();
```

路由集合\(RouteCollection\)中的每一个入口 , 都是通过一个名字\(如,name\)和一个`Route`实例来定义的 . 该实例则通过一个route pattern\(路由匹配\)和一个路由属性\(route attributes\)的“默认值”数组来定义

```
use Symfony\Component\Routing\Route;

$routes->add('hello', new Route('/hello/{name}', array('name' => 'World')));
$routes->add('bye', new Route('/about'));
```

基于存放在`RouteCollection`实例中的信息 , 使用`UrlMatcher`实例能够匹配URL路径

```
use Symfony\Component\Routing\RequestContext;
use Symfony\Component\Routing\Matcher\UrlMatcher;

$context = new RequestContext();
$context->fromRequest($request);
$matcher = new UrlMatcher($routes, $context);

$attributes = $matcher->match($request->getPathInfo());
```

`match()`方法接收一个request路径 , 返回一个属性数组 .

```
print_r($matcher->match('/hello'));
/* Gives:
array (
  'name' => 'World',
  '_route' => 'hello',
);
*/
```

> URL matcher在匹配不到路由时会抛出一个异常
>
> ```
> $matcher->match('/not-found');
>  
> // throws a Symfony\Component\Routing\Exception\ResourceNotFoundException
> ```

**重构框架 , 引入路由 , commit -m"Routing"**

更新的重点部分

* 路由名称被当做模板名称
* 500错误已被正确管理
* 请求的属性被提取出来了 , 模板内容更加纯粹 , 不过之前也已经提取出来了
* 路由配置已被移入属于它的一个文件routes/web.php

---

**创建路由文件夹及文件**

```
 mkdir -p framework/routing
 touch route.php
```

**路由类基本组成**

* 一个存放路由的属性
* 一个设置路由属性的方法
* 还需要一个调度方法 , 调度url的请求与响应

路由类初始化 , 查看commit -m"Route Class Init"

