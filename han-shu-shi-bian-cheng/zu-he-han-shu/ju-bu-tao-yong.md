# 局部套用

Currying通常用作前面的"部分应用"程序的同义词 . 尽管这两个概念都允许我们绑定某个函数的一些参数 , 但核心思想有点不同 .

"Currying" 背后的想法是变换一个函数 , 将多个参数转换为一个参数序列 . 因为这可能有点难掌握 , 让我们尝试'curry'一下substr函数 , 结果称为 curryied 函数 :

```php
<?php
function substr_curryied(string $s)
{
    return function(int $start) use($s) {
        return function(int $length) use($s, $start) {
            return substr($s, $start, $length);
        };
    };
}

$f = substr_curryied('Lorem ipsum dolor sit amet.');
$g = $f(0);
echo $g(5);
// Lorem
```

正如您所看到的 , 每个调用都返回一个新函数 , 它采用下一个参数 . 这说明了与部分应用程序的主要区别 . 当调用部分应用函数时, 将获得结果 . 但是, 当您调用 curryied 函数时, 您将得到一个新函数, 直到传递最后一个参数 . 此外, 您只能按顺序从左侧\(最顶层\)开始绑定参数 .

如果调用链看起来过于冗长 , 您可以从 PHP 7 开始大大简化它 . 这是因为实现了RFC中的统一变量语法

> 参见 [https://wiki.php.net/rfc/uniform\_variable\_syntax](https://wiki.php.net/rfc/uniform_variable_syntax) 以获取详细信息

```php
<?php
echo substr_curryied('Lorem ipsum dolor sit amet.')(0)(5);
// Lorem
```

"Currying" 的优点在这样的情况下可能不太明显 . 但是, 一旦你开始使用高阶函数, 比如 "map" 或 "reduce" 函数, 这个想法就会变得非常强大 . 

您可能还记得 "functional-php" 库中的 "pluck" 函数 . 其目的是从对象集合中检索给定的属性 . 如果 "pluck" 函数是作为已经 curryied 的函数实现 , 则可以使用多种方式 : 

```php
<?php
function pluck(string $property)
{
    return function($o) use($property) {
        if (is_object($o) && isset($o->{$propertyName})) {
            return $o->{$property};
        } elseif ((is_array($o) || $o instanceof ArrayAccess) && isset($o[$property])) {
            return $o[$property];
        }
        
        return false;
    };
}
```



