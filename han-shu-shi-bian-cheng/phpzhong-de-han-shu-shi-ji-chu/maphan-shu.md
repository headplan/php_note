# Map函数

PHP中的map或array\_map方法是将一个给定的回调应用于集合的所有元素的高阶函数 . 返回值是相同顺序的集合 .

一个简单的例子是 :

```php
<?php
function square(int $x): int
{
    return $x * $x;
}

$squared = array_map('square', [1, 2, 3, 4]);
// $squared contains [1, 4, 9, 16]
```

我们创建一个函数来计算给定整数的平方 , 然后使用 array\_map 函数计算给定数组的所有平方值 . array\_map 函数的第一个参数是任何形式的可调用的 , 第二个参数必须是实数数组 . 不能传递迭代器或遍历的实例 .

您也可以传递多个数组 . 您的回调将从每个数组中获取一个值 :

```php
<?php
$numbers = [1, 2, 3, 4];
$english = ['one', 'two', 'three', 'four'];
$french = ['un', 'deux', 'trois', 'quatre'];
function translate(int $n, string $e, string $f): string
{
    return "$n is $e, or $f in French.";
}
print_r(array_map('translate', $numbers, $english, $french));
```

上面的代码将显示 : 

```php
Array
(
    [0] => 1 is one, or un in French.
    [1] => 2 is two, or deux in French.
    [2] => 3 is three, or trois in French.
    [3] => 4 is four, or quatre in French.
)
```

最长的数组将确定结果的长度 . 较短的数组将用 null 值展开 , 以便它们都具有匹配的长度 . 

如果将 null 作为函数传递, PHP 将合并数组 : 

```php
<?php
print_r(array_map(null, [1, 2], ['one', 'two'], ['un', 'deux']));

# 结果是
Array
(
    [0] => Array
        (
            [0] => 1
            [1] => one
            [2] => un
        )
    [1] => Array
        (
            [0] => 2
            [1] => two
            [2] => deux
        )
)
```

如果只传递一个数组 , 那么这些键将被保留 , 但是如果您传递多个数组 , 则它们将丢失 : 

```php
<?php
function add(int $a, int $b = 10): int
{
    return $a + $b;
}
print_r(array_map('add', ['one' => 1, 'two' => 2]));
print_r(array_map('add', [1, 2], [20, 30]));

# 结果是
Array
    (
    [one] => 11
    [two] => 12
)
Array
(
    [0] => 21
    [1] => 32
)
```

最后要注意的是 , 很难轻易地访问每个项目的键key . 但是, 您可调用的可以是一个闭包 , 因此您可以使用任何可从您的上下文访问的变量 , 使用此方法, 您可以映射数组的键并使用闭包来检索像这样的值 : 

```php
<?php
$data = ['one' => 1, 'two' => 2];
print_r(array_map(function ($key) use ($data)
{
    return $data[$key];
}, array_keys($data)));
```



