# Filter函数

PHP 中的过滤器或 array\_filter 函数是一种高阶函数 , 它基于布尔判断只保留集合的某些元素 . 返回值是一个集合 , 它将只包含判断函数返回 true 的元素 . 一个简单的例子是 : 

```php
<?php
function odd(int $a): bool
{
    return $a % 2 === 1;
}

$filtered = array_filter([1, 2, 3, 4, 5, 6], 'odd');

/* $filtered contains [1, 3, 5] */
```

我们首先创建一个函数 , 它采用集合的一个值并返回一个布尔型 . 这个函数将是我们的判断 . 在我们的例子中 , 我们检查一个整数是否是奇数 . 与array\_map方法一样 , 判断可以是任何可调用的函数或闭包 , 集合必须是数组 . 但是 , 请注意 , 参数顺序是颠倒的 , 集合先来 . 

回调是可选参数 , 如果你不给它任何内容 , 那么PHP将所有可以转换为false的值过滤掉 , 例如 , 空字符串和数组 : 

```php
<?php
$filtered = array_filter(["one", "two", "", "three", ""]);
/* $filtered contains ["one", "two", "three"] */
$filtered = array_filter([0, 1, null, 2, [], 3, 0.0]);
/* $filtered contains [1, 2, 3] */ 
```



