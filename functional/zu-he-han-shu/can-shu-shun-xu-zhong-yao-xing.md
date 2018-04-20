# 参数顺序重要性

前面已经介绍了 , array\_map和array\_filter函数的参数顺序是不同的 . 肯定的是 , 这让它们更难使用 , 因为你更容易出错 , 但这不是唯一的问题 . 为了说明为什么参数顺序很重要 , 我们来创建两者的curryied版本 :

```php
<?php
$map = curry_n(2, 'array_map');
$filter = curry_n(2, 'array_filter');
```

我们在这里使用 curry\_n 函数有两个不同的原因 :

* "array\_map"函数接受可变数量的数组 , 所以我们将值强制设置为2 , 来使其安全 ; 
* "array\_filter"函数有一个名为$flag的第三个参数 , 最好是optional值 ; 

还记得我们新 curryied 函数的顺序参数吗 ? $map参数将首先进行回调 , 而$filters参数将首先要求集合 . 让我们尝试创建一个新的有用的函数来了解这一点 :

```php
<?php
$trim = $map('trim');
$hash = $map('sha1');

$oddNumbers = $filter([1, 3, 5, 7]);
$vowels = $filter(['a', 'e', 'i', 'o', 'u']);
```

我们的映射示例是非常基本的 , 但有一些用途 , 而我们的过滤示例只是静态数据 . 我敢打赌 , 你可以找到一些方法来使用$trim和$hash参数 , 但是你有什么机会需要一个可以被过滤的奇数或元音的列表呢 ?

还有一个例子可以在一切开始之前考虑一下 , 还记得我们前面的 substr 函数的 curryied 例子吗 ?

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

我可以保证 , 如果我们可以先定义要创建的开始和长度 , 那将会更加有用 . 例如 , $take5fromStart函数 ; 而不是使用这个别扭的$substrOnLoremIpsum参数 , 我们在示例中简单地调用$f参数 .

这里重要的是 , 您要处理的数据 , 您的“主题”必须最后出现 , 因为它大大增加了您的curryied函数的重用 , 并允许您将其用作其他高阶函数的参数 .

在上一个示例中 , 我们假设要创建一个函数 , 它采用集合中所有元素的前两个字母 . 我们将尝试用一组两个函数来完成它 , 其中的参数是不同的顺序 .

函数的实现是作为一个练习 , 因为它不是真正的问题 .

在例子1中 , 主题是第一个参数 :

```php
<?php
$map = curry(function(array $array, callable $cb) {});
$take = curry(function(string $string, int $count) {});
$firstTwo = function(array $array) {
    return $map($array, function(string $s) {
        return $take($s, 2);
    });
}
```

参数顺序强制我们创建包装函数 . 事实上 , 由于我们不能使用 , 这是事实 . 这些函数并不是令人信服的 .

在例子2中 , 主题在末尾 : 

```php
<?php
$map = curry(function(callable $cb, array $array) {});
$take = curry(function(int $count, string $string) {});
$firstTwo = $map($take(2));
```

事实上，一个很好的选择也可以帮助很多"功能组合" , 我们将在下面的内容中看到 . 

作为关于这个主题的最后一个注释 , 并且是完全公平的 , 使用函数与反向参数的版本可以使用来自"functional-php"库的"partial\_right"函数来编写 , 并且您可以使用"partial\_any"函数用于具有奇怪顺序的更多参数的函数 . 但即使如此 , 解决方案并不像正确的顺序那样简单 : 

```php
<?php
use function Functional\partial_right;
$firstTwo = partial_right($map, partial_right($take, 2));
```



