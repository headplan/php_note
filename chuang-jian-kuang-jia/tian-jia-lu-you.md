# 添加路由 - 新建PHP框架框架

**参考资料**

* [https://github.com/noahbuscher/Macaw](https://github.com/noahbuscher/Macaw)
* [https://github.com/klein/klein.php](https://github.com/klein/klein.php)
* [https://github.com/laravel/framework](https://github.com/laravel/framework)

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

**创建路由文件夹及文件**

```
 mkdir -p framework/routing
 touch route.php
```

**路由类基本组成**

* 一个存放路由的属性
* 一个设置路由属性的方法
* 还需要一个调度方法 , 调度url的请求与响应



**路由类初始化 , 查看commit -m"Route Class Init"**

