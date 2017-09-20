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

