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

HttpKernel组件有很多有趣的功能 , 我们先来看看控制器解析器和参数解析器 . 控制器解析器决定了使用哪个控制器 , 参数解析器用于决定传给控制器的参数 .

控制器解析器实现的是下面的接口 :

```php
namespace Symfony\Component\HttpKernel\Controller;

// ...
interface ControllerResolverInterface
{
    function getController(Request $request);

    function getArguments(Request $request, $controller);
}
```

`getController()`方法依赖的命名约定和我们之前定义过的相同：

`_controller`这个request attribute\(请求属性\)必须包含与Request相关联的控制器 . 除了内置的PHP回调\(callbacks\),`getController()`也支持由“类名后面跟两个冒号和一个方法名”所组成的字符串来作为一个有效的回调 , 比如“class::method” :

```
'_controller' => 'LeapYearController::indexAction',
```

现在来引入HttpKernel组件的这两个解析器 , **查看相关commit **.

`getArguments()`在内部检查了控制器签名 , 通过使用reflection\(反射\)来决定哪个参数应该传给它\(控制器\) . 例如 , `indexAction()`方法需要Request对象作为参数 , 如果该对象被正确地进行了“类型提示” ,`getArguments()`方法知道何时正确地注入它 , 而且还能注入Request的任意属性 , 作为（控制器的）参数只需使用和对应属性相同的名字即可, 顺序是没有要求的 , 可以为任何一个参数定义默认值 , 用来匹配Request对象中的一个可选属性 :

```php
public function indexAction(Request $request, $year = null)
```

解析器负责对控制器回调\(注:symfony的控制器是个callable\)及其参数进行验证 . 如果有问题 , 它会抛出一个异常 , 解析问题所在并给出信息 .

默认的控制器解析器和参数解析器已经超级灵活 , 但也可以自定义 , 因为给出了interface接口 , 在Symfony中`getController()`被强化为支持把控制器作为服务 , 而`getArguments()`提供了一个扩展点\(extension point\) , 用于修改或强化对参数的解析 . 

> 注意 : 类型提示时别忘记引入命名空间

---



