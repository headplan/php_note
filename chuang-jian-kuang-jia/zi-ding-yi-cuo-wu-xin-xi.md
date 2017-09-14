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



