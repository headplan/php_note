# 自定义错误信息

通常派遣会一个新的事件 , 然后监听它 , 正确使用的话意味着监听需要调用一个常规控制器 , 就是这么方便 .

之前引入了`HttpKernelInterface` 接口, 现在继承`HttpKernel`类 , 它是对`HttpKernelInterface`接口的一个“通用的、可扩展的、灵活的”实现 .

```
class Framework extends HttpKernel
```

HttpKernel很像我们编写的Framework类 , 它在“处理请求”过程中的一些策略关键点上派发事件 , 它使用控制器解析器来选择控制器以发送请求 , 它照顾到了极端情况下的场景 , 在出现问题时可以提供很好的反馈信息 . 现在 , 清空我们之前编写的Framework类 , 然后继承HttpKernel类 .

```php
<?php

namespace Headplan;

use Symfony\Component\HttpKernel\HttpKernel;

class Framework extends HttpKernel
{
}
```

修改前端控制器 , 引入HttpKernel初始化需要的依赖 . 其中 , `RouterListener`实现的是和我们的框架中相同的逻辑 : 匹配进入的请求 , 再以路由参数来装载请求的属性 .

```
$dispatcher->addSubscriber(new RouterListener($matcher, $requestStack));
```

现在我们的代码非常简洁 , 异常的健壮 . 例如 , 引入内置的`ExceptionListener`可以让你的错误管理\(error management\)可配置 .

```php
$errorHandler = function (Symfony\Component\Debug\Exception\FlattenException $exception) {
    $msg = 'Something went wrong! ('.$exception->getMessage().')';

    return new Response($msg, $exception->getStatusCode());
};
$dispatcher->addSubscriber(new HttpKernel\EventListener\ExceptionListener($errorHandler));
```

`ExceptionListener`给你的是一个`FlattenException`实例 , 而不是抛出`Exception`实例 , 为的是简化异常的操作和显示 . 它接受任何有效的控制器来作为“异常处理器”\(exception handler\) , 因此你可以创建一个ErrorController类来替换掉Closure :

```php
$listener = new HttpKernel\EventListener\ExceptionListener(
    'ErrorController::exceptionAction'
);
$dispatcher->addSubscriber($listener);
```

创建ErrorController类 , `namespace Headplan\Controllers;`一个整洁 , 可定制的错误管理完成了 .

在添加两个HttpKernel的“事件派遣” :

每次在发送响应给客户端之前 , 调用`Response::prepare()`方法 , 可以确保响应兼容HTTP协议 :

```php
$dispatcher->addSubscriber(new HttpKernel\EventListener\ResponseListener('UTF-8'));
```

对流响应\(streamed responses\)进行支持  , 订阅`StreamResponseListener`即可 :

```php
$dispatcher->addSubscriber(new HttpKernel\EventListener\StreamedResponseListener());
```

---

现在创建一个监听 , 允许控制器返回字符串而不是一个完整响应对象 :

```php
class StringResponseListener implements EventSubscriberInterface
{
    public function onView(GetResponseForControllerResultEvent $event)
    {
        $response = $event->getControllerResult();

        if (is_string($response)) {
            $event->setResponse(new Response($response));
        }
    }

    public static function getSubscribedEvents()
    {
        return array('kernel.view' => 'onView');
    }
}
```

`kernel.view`事件只在控制器的返回值并非一个Response对象是才触发 , 另外也因为设置事件的响应会中止事件宣传\(event propagation , 我们的监听器会拦截其他的view监听\) . 

现在 , 整个framework代码已是尽可能的简洁 , 它主要是由“现有的类库\(existing libraries\)”组合而成 . 扩展性则是通过注册事件监听/事件订阅来完成 . 

