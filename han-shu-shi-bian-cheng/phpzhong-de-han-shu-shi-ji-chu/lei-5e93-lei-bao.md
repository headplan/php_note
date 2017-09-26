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

同样重要的是要注意，大多数函数接受数组和任何实现Traversable接口的函数 , 如迭代器 .

**常用函数**

这些函数可以在各种情况下帮助到你 , 不只是函数式中 :

* “true”和“false”函数检查集合中的所有元素是严格为True还是严格为False . 
* "truthy" 和 "falsy"函数与上面的相同 , 但比较并不严格 . 
* "const\_function"函数返回一个将始终返回给定值的新函数 . 这可以用来模拟不可变的数据 . 

**扩展PHP的函数**

PHP函数只能在真正的数组上工作 . 以下函数将它们的行为扩展到任何可以使用 foreach 循环进行迭代的操作 . 在所有函数中 , 参数的顺序也保持不变 :

* "contains"方法检查该值是否包含在给定集合中 . 第三个参数控制是否严格 . 
* "sort"方法对一个集合进行排序 , 但返回一个新数组 , 而不是按引用排序 . 您可以决定是否保留key . 
* "map"方法将array\_map方法的行为扩展到所有集合上 . 
* "sum\(总和\)"、"maximum\(最大值\)" 和 "minimum\(最小值\)" 方法执行与 PHP 对应的相同的工作 , 但是可以用在任何类型的集合中 . 除了这些 , 图书馆还包含"乘积\(product\)"、"比率\(ratio\)"、"差异\(difference\)"和"平均值\(average\)" . 
* 当不传递函数时 , "zip" 方法执行与 array\_map 方法相同的工作 . 但是 , 您也可以通过回调来确定如何合并不同的项 . 
* "reduce\_left"和"reduce\_right"方法从左或右折叠集合 . 

**断言的应用**

在处理集合时 , 通常需要检查某些、全部或没有元素是否验证某个条件并相应地进行操作 . 为了做到这一点 , 您可以使用以下函数 :

* 如果集合的所有元素都对断言有效, 则 "every" 函数都返回值 true
* 如果至少有一个元素对断言有效, 则 "some" 函数返回值 true
* 如果没有任何元素对断言有效, 则 "none" 函数返回值 true

上面提到的这些函数不会修改集合 . 它们只是检查元素是否匹配特定条件 . 如果需要过滤某些元素 , 可以使用以下函数 :

* "select"或"filter"函数仅返回对断言有效的元素 . 
* "reject"函数只返回对断言无效的元素 . 
* "first"或"head"函数返回对断言有效的第一个元素 . 
* "last"函数返回对断言有效的最后一个元素 . 
* "drop\_first"函数从集合开始删除元素 , 直到给定的回调为true . 一旦回调函数返回false , 即停止删除元素 . 
* "drop\_last"函数与上一个函数相同 , 但是从最后开始 . 

所有这些函数都返回一个新数组 , 保留原始集合不变 .

**调用函数**

当您想在回调中调用函数时 , 立即声明一个匿名函数是很麻烦的 . 这些helper将为你提供更简单的语法 :

* "invoke" helper调用集合中所有对象的方法 , 并返回一个具有其结果的新集合 . 
* "invoke\_first"和"invoke\_last" helper分别调用集合的第一个和最后一个对象的方法 . 
* 如果对象是有效的 , "invoke\_if" helper的第一个参数上调用给定的方法 , 还可以传递方法参数和默认值 . 
* "invoker" helper返回一个新的回调来调用 , 它使用给定的参数将给定的方法用于其参数 . 

您可能还需要调用一个函数 , 直到获得一个值或达到某种阈限为止 . 类库中的 :

* "retry"类调用一个函数 , 直到它停止返回异常或达到尝试的次数 . 
* "poll"类调用一个函数 , 直到它返回 truthy 值或到达给定限时 . 

**操作数据**

前面的函数是关于用helper调用函数的 , 下面的函数是关于数据的获取和操作 , 所以不必每次都使用匿名函数 :

* "pluck"函数从给定集合中的所有对象中获取一个属性 , 并返回一个带有值的新集合 .
* "pick"函数根据给定的键从数组中选择一个元素 . 如果元素不存在 , 则可以提供默认值 .
* "first\_index\_of"和"last\_index\_of"函数返回与给定值匹配的元素的第一个和最后一个索引 .
* "indexes\_of"函数返回与给定值匹配的所有索引 .
* "flatten"函数将嵌套集合的深度减少到单个平面集合 .

有时 , 你应该还希望将一个集合分成多个部分 , 或者给定一个断言或某个分组值之后 :

* "partition"方法接受谓词列表 - 根据第一个有效的谓词 , 将集合的每个项目放在给定的组中 . 
* "group"方法基于每个元素的回调所返回的每个不同的值创建多个组 . 

**Github**

functional-php库提供了许多不同的帮助和实用程序功能 . 看看github上的doc可以了解更多 .

#### php-option类库

我们之前创建了我们自己的Maybe类型的版本 . 这个类库提出了更加完整的实现 . 这里使用的是Scala所使用的命名option . 源码位于GitHub :

> [https://github.com/schmittjoh/php](https://github.com/schmittjoh/php) -option

最简单的安装方法还是使用Composer命令安装 :

```
composer require phpoption/phpoption
```

一个有趣的补充是LazyOption方法 , 它采用回调而不是一个值 . 只有当需要值时才会执行回调 . 当您使用orElse方法给出替代方案\(如果先前的值为无效值\)时 , 这一点尤为有用 . 在这种情况下 , 通过使用LazyOption方法 , 一旦一个值有效 , 您就可以避免进行不必要的计算 .

还有各种helper , 只有在值有效的时候才可以调用方法 , 并且提供了多个实例化的可能性 . 该库还提供了一个 API , 更类似于一个集合 .

#### Laravel collections

正如前面已经提到的 , Laravel 提供了一个很好的库来管理集合 . 它声明一个称为Collection的类 , 它由其 ORM、Eloquent的内部使用 , 还有大多数依赖于集合的部分 .

在内部 , 使用了一个简单的数组 , 但它以一种促进数据不变性和操作数据的功能方法的方式进行包装 . 为了实现这一目标 , 大部分的方法开发者建议开发的 .

如果您已经使用了 Laravel, 您可能已经熟悉了此支持类提供的可能性。如果您使用的是任何其他框架, 您仍然可以从中受益, 从它得到提取的部分 :

> [https://github.com/tightenco/collect](https://github.com/tightenco/collect)

官方文档 : 

> https://laravel.com/docs/5.5/collections

如果想了解更多关于Laravel的collections类库 , 可以看看下面的视频配套教材 , 因为Laravel的作者也说他在其中学了不少 : 

> https://adamwathan.me/refactoring-to-collections





