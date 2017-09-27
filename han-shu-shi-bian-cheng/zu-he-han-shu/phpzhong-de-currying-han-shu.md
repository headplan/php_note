# PHP中的Currying函数

我希望你现在已经相信了 "Currying" 的力量 . 如果没有 , 我希望下面的例子会说服你 . 同时 , 你可能已经发现了 , 在现有的函数周围编写一个新的实用程序函数来创建一个新的 curryied 版本是非常麻烦的 , 没错 .

在“Haskell”这样的语言中 , 默认情况下所有的函数都是“curryied” . 可悲的是 , PHP并不是这样 , 但是实现这个过程却是很简单的 , 那就让我们编写一个帮助函数吧 .

由于在 PHP 中有可选参数的可能性, 我们将首先创建一个函数 curry\_n, 它采用你想要的参数个数。这样, 你也可以决定是否要 ' curry ' 所有的参数, 或者只是其中的一些。它也可以用于具有可变参数数目的函数 :

```php
<?php
function curry_n(int $count, callable $function): callable
{
    $accumulator = function(array $arguments) use($count, $function, &$accumulator) {
        return function() use($count, $function, $arguments, $accumulator) {
            $arguments = array_merge($arguments, func_get_args());
                if($count <= count($arguments)) {
                    return call_user_func_array($function, $arguments);
                }

            return $accumulator($arguments);
        };
    };

    return $accumulator([]);
}
```

其思想是使用内部帮助器函数, 将已经传递的值作为参数, 然后创建一个闭包 . 当调用时, 闭包将根据实际值的数目决定, 我们是否可以调用原始函数, 或者是否需要使用我们的帮助器创建一个新函数 .

请注意, 如果给出的参数计数高于实际计数, 则所有无关参数都将传递给原始函数, 但可能会被忽略 . 此外, 给出较小的计数将导致最后一步, 而不仅仅是一个参数可以正确完成 . 我们现在可以创建我们的第二个函数, 它将使用反射变量来确定参数的个数 :

```php
<?php
function curry(callable $function, bool $required = true): callable
{
    if(is_string($function) && strpos($function, '::', 1) !== false) {
        $reflection = new \ReflectionMethod($f);
    } elseif (is_array($function) && count($function) == 2) {
        $reflection = new \ReflectionMethod($function[0], $function[1]);
    } elseif (is_object($function) && method_exists($function, '__invoke')) {
        $reflection = new \ReflectionMethod($function, '__invoke');
    } else {
        $reflection = new \ReflectionFunction($function);
    }

    $count = $required 
    ? $reflection->getNumberOfRequiredParameters() 
    : $reflection->getNumberOfParameters();

    return curry_n($count, $function);
}
```

正如您所看到的, 没有简单的方法来确定函数所期望的参数个数 . 我们还必须添加一个参数, 以确定我们是否应该考虑所有参数, 包括那些具有默认值, 或者只有强制性的 . 

您可能已经注意到, 我们不创建严格执行一个参数的函数 ; 相反, 我们使用 "func\_get\_args" 函数来获取所有传递的参数 . 这样可以更自然地使用 "currying" 功能, 并且与 "函数式" 语言所做的工作是等同的 . 我们对 "currying" 的定义现在更多的是函数的行, 它返回一个新函数, 直到它接收到它的所有参数 . 

本书其余部分的示例将假定这个“curry”函数是可用并可以直接使用的 . 还可以去functional-php类库中找找 , 或许已经添加了这个功能 . 

