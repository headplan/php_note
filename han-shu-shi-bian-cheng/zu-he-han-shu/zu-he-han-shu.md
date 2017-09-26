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



