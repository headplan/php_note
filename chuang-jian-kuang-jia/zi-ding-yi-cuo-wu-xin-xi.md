# 自定义错误信息

通常派遣会一个新的事件 , 然后监听它 , 正确使用的话意味着监听需要调用一个常规控制器 . 但如果这个错误控制器\(error controller\)也抛出异常怎么办 , 那将是一个无尽的循环 . 

之前引入了`HttpKernelInterface` 接口, 现在继承`HttpKernel`类 , 它是对`HttpKernelInterface`接口的一个“通用的、可扩展的、灵活的”实现 . 

```
class Framework extends HttpKernel
```

新的前端控制器 : 





