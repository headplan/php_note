# Hook组件

可扩展意味着开发者应该可以轻易打入\(hook\)框架的生命周期来调整请求被处理的方式 . 由于PHP中没有类似Python的WSGI或Ruby的Rack等标准 , 这里使用了Mediator\(中介模式\) , 来允许任何各类的行为被附着到我们的框架之中 , Symfony的EventDispatcher组件实现了这种模式的一个轻量化版本 :

```
composer require symfony/event-dispatcher
```

dispatcher\(派遣器\)是事件派遣系统的中心对象\(central object\) , 它通知那些“针对某个事件”的监听器 . 换一种表达方式 : 你的代码派遣一个事件到dispatcher , dispatcher通知所有已经注册到该事件的监听 , 然后每个监听对这个事件去做它们希望的任何事 .

举个例子 : 框架派遣一个事件 , 在响应实例被返回之前 :

```
# 在Framework文件中引入派遣器
use Symfony\Component\EventDispatcher\EventDispatcher;

# 初始化
public function __construct(
    EventDispatcher $dispatcher,

# 在响应前派遣事件,这里约定名字为'response',因为后面的监听器或者订阅器要和其同名.
$this->dispatcher->dispatch('response', new ResponseEvent($request, $response));
```

然后创建这个事件ResponseEvent : 

```php
# Headplan/Events/ResponseEvent.php
<?php

namespace Headplan\Events;

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\EventDispatcher\Event;

class ResponseEvent extends Event
{
    private $request;
    private $response;

    public function __construct(Request $request, Response $response)
    {
        $this->request = $request;
        $this->response = $response;
    }

    public function getRequest()
    {
        return $this->request;
    }

    public function getResponse()
    {
        return $this->response;
    }
}
```



