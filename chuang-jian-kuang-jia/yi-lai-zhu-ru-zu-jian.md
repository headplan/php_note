# 依赖注入组件

上一章通过从同名组件中继承`HttpKernel`而清空了`Framework`类 . 现在 , 可以把前端控制器中的一些内容一到这里了 :

```php
class Framework extends HttpKernel
{
    public function __construct($routes)
    {
        # 实例化一个请求堆栈
        $requestStack = new RequestStack();

        # 根据路由配置匹配请求的URL路径
        $context = new RequestContext();
        $matcher = new UrlMatcher($routes, $context);

        # 控制器解析器和控制器参数解析器
        $controllerResolver = new ControllerResolver();
        $argumentResolver = new ArgumentResolver();

        # 订阅事件
        $dispatcher = new EventDispatcher();
        # RouterListener实现匹配进入的请求,再以路由参数来装载请求的属性
        $dispatcher->addSubscriber(new RouterListener($matcher, $requestStack));
        # 确保响应兼容HTTP协议
        $dispatcher->addSubscriber(new ResponseListener('UTF-8'));
        # 支持流响应
        $dispatcher->addSubscriber(new StreamedResponseListener());
        # 返回字符串
        $dispatcher->addSubscriber(new Events\StringResponseListener());
        # 自定义异常
        $listener = new ExceptionListener(
            'Headplan\\Framework\\Controllers\\ErrorController::exceptionAction'
        );
        $dispatcher->addSubscriber($listener);

        parent::__construct($dispatcher, $controllerResolver, $requestStack, $argumentResolver);
    }
}
```

前端控制器中的大部分代码已经移动到了框架中 , 我们的目的也是尽可能的减少前端控制器中的逻辑 , 让多个前端控制器具有不同的配置信息 , 框架更具可配置性 .

现在遇到了下面几个问题 :

* 原来的监听已废弃 , 现在自定义监听也只能在框架文件中注册 , 可能需要添加一个`Framework::getEventDispatcher()`方法
* 不再能改变`UrlMatcher`的实现 , 也无法改变`ControllerResolver`的实现
* 测试已经废弃 , 已经无法模拟内部对象
* 不再能改变“传入`ResponseListener`监听的charset字符集\(虽然可以在构造时传入\)

以上的解决方案 , 依赖注入容器 :

```
composer require symfony/dependency-injection
```

> 更轻量的容器还可以考虑[https://github.com/silexphp/Pimple](https://github.com/silexphp/Pimple) , 他还有php扩展版 .

创建一个新文件 , 用于容纳“DI容器”的配置信息 : 

```php
<?php

use Symfony\Component\DependencyInjection;
use Symfony\Component\DependencyInjection\Reference;

$c = new DependencyInjection\ContainerBuilder();
$c->register('context', 'Symfony\Component\Routing\RequestContext');
$c->register('matcher', 'Symfony\Component\Routing\Matcher\UrlMatcher')

...
```

这个文件的目的 , 就是要配置对象和它们的依赖 , 这里的配置过程中并没有任何东西被实例化 , 纯粹是对需要操作的对象的静态描述，以及如何创建它们 . 对象 , 将在从容器中访问它们时 , 或者在容器需要它们来创建其他对象时 , 被创建出来 . 

现在 , Framework文件又变成了之前的简洁版 : 

```php
<?php

namespace Headplan;

use Symfony\Component\HttpKernel\HttpKernel;

class Framework extends HttpKernel {}
```



