# HttpKernel组件之控制器解析

正如题目所写 , 传统意义上的控制器要出现了 , 虽然现在一切的一切依然是面向过程的 . 前面我们约定了\_controller即控制器 , 它可以是任何有效的php回调 , 这样我们就可以把前面计算闰年的例子转换为一个控制器类 : 

```php
class LeapYearController
{
    public function indexAction($request)
    {
        if (is_leap_year($request->attributes->get('year'))) {
            return new Response('Yep, this is a leap year!');
        }
 
        return new Response('Nope, this is not a leap year.');
    }
}
```

更新相应的路由定义 : 

```php
$routes->add('leap_year', new Routing\Route('/is_leap_year/{year}', array(
    'year' => null,
    '_controller' => array(new LeapYearController(), 'indexAction'),
)));
```



