# Reduce函数

折叠是指使用组合函数将集合缩减为返回值的过程 . 根据语言的不同 , 此操作可以有多个名称 , 如折叠、减少、累积、聚合或压缩 . 与其他与数组相关的函数一样 , PHP 版本是 array\_reduce 函数 .

您可能熟悉 array\_sum 函数 , 它计算数组中所有值的总和 . 事实上 , 这是一个折叠 , 可以很容易地使用 array\_reduce 函数编写 :

```php
<?php
function sum(int $carry, int $i): int
{
    return $carry + $i;
}
$summed = array_reduce([1, 2, 3, 4], 'sum', 0);
/* $summed contains 10 */
```

像 array\_filter 方法一样 , 集合先来 , 然后传递一个回调 , 最后是一个可选的初始值 . 在我们的例子中 , 我们被迫通过初始值 0 , 因为默认 null 是 int 类型的函数签名的无效类型 .

回调函数有两个参数 . 第一个是基于所有以前的项目 , 有时称为进位或累加器的当前减少的价值。第二个是当前正在处理的数组元素。在第一次迭代中 , 进位等于初始值 .

您不一定需要使用元素本身来生成值 . 例如 , 您可以使用折叠实现对 in\_array 的天然替换 :

```php
<?php
function in_array2(string $needle, array $haystack): bool
{
    $search = function(bool $contains, string $item) use ($needle):bool
    {
    return $needle == $item ? true : $contains;
    };

    return array_reduce($haystack, $search, false);
}

var_dump(in_array2('two', ['one', 'two', 'three']));
// bool(true)
```

reduce操作从初始值false开始 , 因为我们假设数组不包含我们的针 . 这也使我们能够很好地管理我们有一个空数组的情况 . 

每一个项目 , 如果该项目是我们正在寻找的, 我们返回 true , 这将是新的价值传递周围 . 如果它不匹配 , 我们只返回累加器的当前值 , 如果我们早发现了该项 , 或者如果没有 , 则为 false . 

我们的实现可能会比API提供的函数要慢一点 , 因为无论发生什么 , 我们必须在返回结果之前遍历整个数组 , 而不是在我们遇到搜索到的项时就能够退出该函数 . 

但是 , 我们可以在性能相似的情况下实现求最大值的函数max\(\)的替代 , 因为任何实现都必须遍历所有值 : 

```php
<?php
function max2(array $data): int
{
    return array_reduce($data, function(int $max, int $i) : int
    {
        return $i > $max ? $i : $max;
    }, 0);
}
echo max2([5, 10, 23, 1, 0]);
// 23
```

虽然使用数字而不是布尔值 , 但这个想法和以前一样 . 我们从最初的0开始 , 我们目前的最大值 . 如果我们遇到一个更大的价值 , 我们返回它 , 使它得到通过周围 . 否则 , 我们将继续返回当前的累加器 , 它已经包含迄今遇到的最大值 . 

