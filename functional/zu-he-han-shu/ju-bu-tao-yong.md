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

我们可以轻松地从任何类型的对象或数组中获取值 : 

```php
<?php
$user = ['name' => 'Gilles', 'country' => 'Switzerland', 'member' => true];
pluck('name')($user);
```

我们可以从对象集合中提取一个属性, 如 "functional-php" 库中的版本一样 : 

```php
<?php
$users = [
    ['name' => 'Gilles', 'country' => 'Switzerland', 'member' => true],
    ['name' => 'Léon', 'country' => 'Canada', 'member' => false],
    ['name' => 'Olive', 'country' => 'England', 'member' => true],
];
pluck('country')($users);
```

当我们的实现在没有发现任何内容时返回 false , 我们可以使用它来筛选包含特定值的数组 : 

```php
<?php
array_filter($users, pluck('member')); 
```

我们可以结合多个用例来获取所有成员的名称 : 

```php
<?php
pluck('name', array_filter($users, pluck('member')));
```

如果没有"currying" , 我们需要在一个更传统的pluck函数上编写一个包装器 , 或者创建三个专门的功能 . 

让我们更进一步, 结合多个 curryied 函数 . 首先 , 我们将需要在 array\_map 和 preg\_replace 函数周围创建一个包装函数 :

```php
<?php
function map(callable $callback)
{
    return function(array $array) use($callback) {
        return array_map($callback, $array);
    };
}

function replace($regex)
{
    return function(string $replacement) use($regex) {
        return function(string $subject) use($regex, $replacement)
        {
            return preg_replace($regex, $replacement, $subject);
        };
    };
}
```

现在, 我们可以使用这些函数来创建多个新功能, 例如, 一个函数, 它用下划线或所有带有星号的元音替换字符串中的所有空格:

```php
<?php
function map(callable $callback)
{
    return function(array $array) use($callback) {
        return array_map($callback, $array);
    };
}

function replace($regex)
{
    return function(string $replacement) use($regex) {
        return function(string $subject) use($regex, $replacement)
        {
            return preg_replace($regex, $replacement, $subject);
        };
    };
}
```



