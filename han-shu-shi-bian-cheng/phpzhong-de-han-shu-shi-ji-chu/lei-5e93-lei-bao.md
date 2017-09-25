# 类库/类包

现在 , 我们介绍了 PHP 中已经具备的各种函数的函数式技术基础知识 , 我们应该看看与其相关的各种类库 , 因为我们应该专注于业务代码 , 而不是编写helper和一些实用函数 , 就像我们用新的Maybe和Either类型一样 .

#### functional-php类库

“functional-php”库可能是与PHP功能编程相关的最旧的库之一 , 因为它的第一个版本可追溯到2011年6月 . 它一直跟随最新的PHP版本发展 , 甚至去年转换为Composer进行分发 .

> 如果你愿意仔细阅读 , 相信你会得到更多 .
>
> [https://github.com/lstrojny/functional-php](https://github.com/lstrojny/functional-php)

使用composer安装 , 很简单 :

```
composer require lstrojny/functional-php.
```

由于性能原因 , 库曾经在 PHP 和 C 扩展中被实现 . 但最近 PHP 核心的改进速度和维护两个代码的负担使得扩展过期了 . 

很多帮助函数都被实现了 , 我们现在没有足够的空间去详细讨论它们 . 如果你有兴趣 , 可以先看看文档 . 但是 , 后面的一些新的概念或新的内容 , 还是会经常用到这些类库 . 

此外 , 我们还没有讨论 "library-related" 函数所涵盖的一些概念 , 将在我们处理这些主题时提出 . 

**如何使用**

正如1章中已经讨论过的 , php 中的第一类公民函数 , 从 php 5.6 开始 , 你可以从一个命名空间中导入一个函数 . 这是使用库的最简单方法 . 您还可以导入整个命名空间 , 并在调用它们时对所有函数进行前缀标注 : 

```php
<?php
require_once __DIR__.'/vendor/autoload.php';

use function Functional\map;
map(range(0, 4), function($v) {
    return $v * 2;
});

use Functional as F;
F\map(range(0, 4), function($v) {
    return $v * 2;
});
```



