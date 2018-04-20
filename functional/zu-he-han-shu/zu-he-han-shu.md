# 组合函数

在函数式编程中, 功能组合的概念是从数学中借用的。如果你有两个函数, f 和 g, 你可以通过组合来创建第三个函数 . 数学中常用的符号是\(f g\) \(x\) , 等价于将它们一个接一个地调用 f \(g \(x\)\) .

使用包装函数, 可以很容易地用 PHP 编写任意两个给定函数 . 假设您希望在所有大写字母和仅安全 HTMLcharacters 中显示标题 :

```php
<?php
function safe_title(string $s)
{
    $safe = htmlspecialchars($s);
    return strtoupper($safe);
}
```

也可以完全避免临时变量 :

```php
<?php
function safe_title2(string $s)
{
    return strtoupper(htmlspecialchars($s));
}
```

当您只想编写一些函数时 , 这很有效 . 但是 , 创建大量这些包装函数会变得非常麻烦 . 如果你可以简单地使用 `$safe _title = strtoupper htmlspecialchars` 一行代码岂妙哉 , 可悲的是 , 这个操作符不存在于php中 , 但是我们前面介绍的functional-php库包含一个compose函数 , 它完全是这样的 :

```php
<?php

require_once __DIR__.'/vendor/autoload.php';

use function Functional\compose;

$safe_title2 = compose('htmlspecialchars', 'strtoupper');
```

增益可能似乎并不重要 , 但让我们比较一下在更多的上下文中使用这样的方法 : 

```php
<?php
$titles = ['Firefly', 'Buffy the Vampire Slayer', 'Stargate Atlantis', 'Tom & Jerry', 'Dawson\'s Creek'];

$titles2 = array_map(function(string $s) {
    return strtoupper(htmlspecialchars($s));
}, $titles);

$titles3 = array_map(compose('htmlspecialchars', 'strtoupper'), $titles);
```

就我个人而言 , 第二种方法容易阅读和理解 . 而且它会变得更好 , 因为您可以将两个以上的函数传递给 "组合" 函数 : 

```php
<?php
$titles4 = array_map(compose('htmlspecialchars', 'strtoupper', 'trim'), $titles);
```

可能引发歧义的一件事是 "函数" 的应用顺序 . 数学符号 f ∘ g 首先执行 g 然后结果被传递到 f . 但是 , "functional-php" 库中的 "compose" 函数按照它们在`compose('first', 'second', 'third')`参数中传递的顺序应用这些函数 . 

根据您的个人喜好 , 这可能更容易理解 , 但在使用其他库时要小心 , 因为应用程序的顺序可能会反转 , 所以一定要仔细阅读文档 . 

