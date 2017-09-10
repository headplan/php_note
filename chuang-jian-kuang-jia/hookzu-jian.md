# Hook组件

可扩展意味着开发者应该可以轻易打入\(hook\)框架的生命周期来调整请求被处理的方式 . 由于PHP中没有类似Python的WSGI或Ruby的Rack等标准 , 这里使用了Mediator\(中介模式\) , 来允许任何各类的行为被附着到我们的框架之中 , Symfony的EventDispatcher组件实现了这种模式的一个轻量化版本 : 

```
composer require symfony/event-dispatcher
```

dispatcher\(派遣器\)是事件派遣系统的中心对象\(central object\) , 它通知那些“针对某个事件”的监听器 . 换一种表达方式 : 你的代码派遣一个事件到dispatcher , dispatcher通知所有已经注册到该事件的监听 , 然后每个监听对这个事件去做它们希望的任何事 . 

举个例子 : 框架派遣一个事件 , 在响应实例被返回之前 : 



