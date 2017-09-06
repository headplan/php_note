# HttpKernel组件之控制器解析

正如题目所写 , 传统意义上的控制器要出现了 , 虽然现在一切的一切依然是面向过程的 . 前面我们约定了\_controller即控制器 , 它可以是任何有效的php回调 , 这样我们就可以把前面计算闰年的例子转换为一个控制器类 :

```php
class LeapYearController
{
    public function indexAction($request)
    {
        if (is_leap_year($request->attributes->get('year'))) {
            return new Response('是闰年!'.'现在时间是:'.date('Y-m-d H:i:s', time()));
        }

        return new Response('不是闰年!'.'现在时间是:'.date('Y-m-d H:i:s', time()));
    }
}
```

更新相应的路由定义 :

```php
$routes->add('leap_year', new Routing\Route('/leap-year/{year}', array(
    'year' => null,
    '_controller' => array(new LeapYearController(), 'indexAction'),
)));
```

这种方式很容易理解 , 但是存在一个问题 , 即全部路由的所有控制器在每次请求时 , 都要被实例化 . 懒加载是更好的解决方案 , 接下来我们要引入一个新的组件 : 

```
composer require symfony/http-kernel
```



