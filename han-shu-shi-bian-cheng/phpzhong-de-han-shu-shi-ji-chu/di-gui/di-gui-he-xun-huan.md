# 递归和循环

某些功能性语言 \(如 "Haskell"\) 没有任何循环结构。这意味着遍历数据结构的唯一方法是使用递归。虽然在功能世界中不鼓励使用 for 循环, 因为当您可以修改循环索引时出现的所有问题, 例如, 使用 foreach 循环并不存在真正的危险。

为了完整起见, 这里有一些方法可以用递归调用来替换循环, 如果您想尝试它, 或者需要理解在没有循环构造的情况下用另一种语言编写的代码。

替换 while 循环 : 

```php
<?php
function while_iterative()
{
    $result = 1;
    while($result < 50) {
        $result = $result * 2;
    }
    return $result;
}

function while_recursive($result = 1, $continue = true)
{
    if($continue === false) {
        return $result;
    }
    return while_recursive($result * 2, $result < 50);
}
```

替换for循环 : 

```php
<?php
function for_iterative()
{
    $result = 5;
    for($i = 1; $i < 10; ++$i) {
        $result = $result * $i;
    }
    return $result;
}

function for_recursive($result = 5, $i = 1)
{
    if($i >= 10) {
        return $result;
    }
    return for_recursive($result * $i, $i + 1);
}
```

正如您所看到的, 诀窍是使用函数参数将循环的当前状态传递给下一个递归。在 while 循环的情况下, 传递条件的结果, 当您模拟 for 循环时, 将传递循环计数器。显然, 当前的计算状态也必须始终被传递。

通常, 递归本身是在帮助器函数中完成的, 以避免将签名与用于执行循环的可选参数混淆。为了保持全局命名空间的整洁, 此帮助器在原始函数中声明。下面是一个示例 : 

```php
<?php
function for_with_helper()
{
    $helper = function($result = 5, $i = 1) use(&$helper)
    {
        if($i >= 10) {
            return $result;
        }
        return $helper($result * $i, $i + 1);
    };
    
    return $helper();
}
```

请注意, 您需要如何通过引用和 "use" 关键字传递包含该函数的变量。这是由于我们已经讨论过的事实。传递给闭包的变量在声明时绑定, 但当声明该函数时, 赋值还没有发生, 变量为空。但是, 如果我们通过引用传递变量, 它将在赋值完成后进行更新, 我们将能够将它用作匿名函数中的回调。

