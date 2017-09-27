# 部分应用

您可能需要设置函数的一些参数 , 但将其中的一部分保留为以后未分配 . 例如 , 我们可能希望创建一个函数来返回一个博客帖子的摘录

设置此类值的专用术语是绑定参数或绑定一个参数 . 进程本身称为部分应用程序 , 新函数被设置为部分应用 .

这样做的简单方法是将函数换为一个新的 :

```php
<?php

function excerpt(string $s)
{
    return substr($s, 0, 5);
}

echo excerpt('Lorem ipsum dolor si amet.');
// Lorem
```

与组合一样 , 总是创建新的函数会很快让一切变得繁琐 . 但是 , "functional-php" 库又一次颠覆了我们 . 您可以决定从左、右或函数签名中的任何特定位置绑定参数 , 分别使用 partial\_left、partial\_right 或 partial\_any 函数 . 

为什么有三个函数 ? 主要出于性能方面的原因 , 因为左和右版本的执行速度会更快 , 因为参数将被一次性替换 , 而最后一个将使用在每次调用新函数时计算的占位符 . 

在上一个示例中, 使用该函数定义占位符 , "..." 是 unicode 字符 . 如果您没有一种简单的方法在计算机上键入它 , 则还可以从函数命名空间\(别名\)中使用占位符函数 . 


