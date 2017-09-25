# 提升函数Lifting functions

我们看到了我们新的Maybe类型的力量 . 但事实是 , 您要么没有时间重写所有现有的函数来支持它 , 要么你根本不能因为把其当成在外部第三方功能 .

幸运的是 , 您可以提升一个函数来创建一个以Maybe类型为参数的新函数 , 将原始函数应用于其值 , 并返回修改的Maybe类型 .

为此, 我们将需要一个新的帮助函数。为了使事情或多或少的简单, 我们还会假设, 如果提升函数的任何参数都不计算值或视为Nothing , 我们将简单地返回 :

```php
<?php
function lift(callable $f)
{
    return function() use ($f)
    {
        if(array_reduce(func_get_args(), function(bool $status, Maybe $m) {
            return $m->isNothing() ? false : $status;
        }, true)) {
            $args = array_map(function(Maybe $m) {
                // 这样做是安全的，因为上面的折叠检查所有参数的类型为Some
                return $m->getOrElse(null);
            }, func_get_args());
        
            return Maybe::just(call_user_func_array($f, $args));
        }
        
        return Maybe::nothing();
    };
}
```

我们来试试吧 : 

```php
<?php
function add(int $a, int $b)
{
    return $a + $b;
}
$add2 = lift('add');
echo $add2(Maybe::just(1), Maybe::just(5))->getOrElse('nothing');
// 6
echo $add2(Maybe::just(1), Maybe::nothing())->getOrElse('nothing');
// nothing
```

您现在可以提起任何功能，以便它可以接受我们的新Maybe类型。 唯一需要考虑的是，如果您要依赖于函数的任何可选参数，它将无法正常工作。

我们可以使用反射或其他方法来确定函数是否具有可选值 , 或者通过一些默认的取消函数 , 但这只会使事情变得复杂并使我们的函数变慢 . 如果您需要使用带有可选参数和Maybe类型的函数 , 则可以对其进行重写或为其自定义包装 . 

作为结束语 , 提升的过程不是预留给Maybe类型的 . 您可以提升任何函数以接受任何类型的容器 . 一个更好的名字 , 可以可能会叫我们的帮主函数 liftMaybe , 或者我们可以添加它作为一个静态的方法 , 到我们Maybe类 , 让一切看起来更清楚 . 



