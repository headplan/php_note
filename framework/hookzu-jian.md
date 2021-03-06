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

最后一步是在前端控制器\(即入口文件\)中创建dispatcher , 然后注册监听或订阅到`response`事件 :

```php
$dispatcher = new EventDispatcher();
# 监听接口有三个参数,监听名,监听者(回调),优先级
$dispatcher->addListener();
# 所以这里在app目录下建立监听者的类回调
# 约定Listener为后缀的文件名和类名
# 约定回调方法名为onEvent,参数即为该事件ResponseEvent.方法中是在此事件发生时的操作
public function onEvent(ResponseEvent $event)
{
    $response = $event->getResponse();

    if ($response->isRedirection()
        || ($response->headers->has('Content-Type') && false === strpos($response->headers->get('Content-Type'), 'html'))
        || 'html' !== $event->getRequest()->getRequestFormat()
    ) {
        return;
    }

    $response->setContent($response->getContent().' ||====YO~YO~YO~');
}

# 添加订阅更方便一些,参数是EventSubscriberInterface $subscriber
# 接口中的getSubscribedEvents方法返回一个数组,格式为:
# array('eventName' => 'methodName')
# array('eventName' => array('methodName', $priority))
# array('eventName' => array(array('methodName1', $priority), array('methodName2')))
$dispatcher->addSubscriber(new App\Listeners\CodeListener);
```

这里重构 , 把前端控制器移到config中 , 创建listeners.php配置文件 , 之后引入 , 约定注册的监听类都继承Listeners类 .

```php
<?php

namespace App\Listeners;

use Symfony\Component\EventDispatcher\EventDispatcher;

# 创建dispatcher并注册一个监听到response事件
$dispatcher = new EventDispatcher();
$dispatcher->addSubscriber(new CodeListener(['response', -123]));
$dispatcher->addListener('response', [new ContentListener, 'onEvent']);
```

>  这里的-1表示CodeListener事件滞后触发\(默认为0\) , 所以就不会输出其中的字符串"YO~YO~YO~"



