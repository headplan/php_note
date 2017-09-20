# 引用透明性

一个表达式 , 如果你可以随时用它的输出来代替它, 而不改变你的程序的行为 , 那么它被认为是引用透明的 . 为了对您的代码库的所有表达式进行这样的操作 , 所有的函数都必须是纯的 , 所有的变量都必须是不可变的 .

从引用透明性中我们得到了什么 ? 它再次的帮助我们减少了认知负担 . 假设我们有以下功能和数据 :

```php
<?php
// The Player implementation is voluntarily simple for brevity.
// Obviously you would use immutable.php in a real project.
class Player
{
    public $hp;
    public $x;
    public $y;

    public function __construct(int $x, int $y, int $hp) {
        $this->x = $x;
        $this->y = $y;
        $this->hp = $hp;
    }
}

function isCloseEnough(Player $one, Player $two): boolean
{
    return abs($one->x - $two->x) < 2 && 
    abs($one->y - $two->y) < 2;
}

function loseHitpoint(Player $p): Player
{
    return new Player($p->x, $p->y, $p->hp - 1);
}

function hit(Player $p, Player $target): Player
{
    return isCloseEnough($p, $target) ?
    loseHitpoint($target) :
    $target;
}
```

现在让我们来模拟两人之间一种非常简单的争吵 :

```php
<?php
$john = new Player(8, 8, 10);
$ted = new Player(7, 9, 10);
$ted = hit($john, $ted);
```

上面定义的所有函数都是纯的 , 因为我们没有可变的数据结构 , 它们也被扩展引用透明性 . 现在 , 为了更好地理解我们的代码片断 , 我们可以使用一种叫做**等式推理**的技术 . 这个想法很简单 , 你可以简单地用等号代替对代码的解释 . 在某种程度上 , 它就像是手动评估代码一样 .

先从isCloseEnough函数开始 , 可以把hit函数转换为 :

```php
<?php
return abs($p->x - $target->x) < 2 && abs($p->y - $target->y) < 2
? loseHitpoint($target) 
: $target;
```

我们的数据是不可变的 , 所以可以直接的使用以下值 :

```php
<?php
return abs(8 - 7) < 2 && abs(8 - 8) < 2 
? loseHitpoint($target) 
: $target;
```

做一些简单的数学题 :

```php
<?php
return 1<2 && 0<2 
? loseHitpoint($target) 
: $target;
```

显而易见 , 条件为真 , 所以我们只能保持正确的分支 :

```php
<?php
return loseHitpoint($target);
```

继续使用函数调用替换 :

```php
<?php
return new Player($target->x, $target->y, $target->hp-1);
```

再次替换这些值

```php
<?php
return new Player(8, 7, 10-1);
```

最后 , 我们的初始函数调用变为 :

```php
<?php
$ted = new Player(8, 7, 9);
```

通过上面这种 , 将引用透明表达式替换为其结果的方式 , 我们能够减少一个相对冗长的代码片断 , 并将多个函数调用用于创建简单对象 . 这对于重构或理解代码的功能非常有用 . 如果在理解某些代码时遇到问题时 , 在知道某些代码是纯粹的时候 , 那么就可以在尝试了解代码时将其替换为结果 , 这可能会帮助你找到问题的核心 .

#### 非即时或延后计算\(惰性计算\)

引用透明度的最大好处是编译器或解析器的延后计算 . 例如, 您可以通过一个数学函数来定义一个无限列表 . 因为语言的懒惰性确保了只有在需要值时才计算列表的值 .

在术语表中 , 我们将非严格或非即时的语言定义为评懒惰性的语言 . 事实上 , 非严格性和惰性还是有细微的差别的 . 这里有一篇文章 , 更详细的描述这些术语和细微的差别 .

> [https://wiki.haskell.org/Lazy\_vs.\_non-strict](https://wiki.haskell.org/Lazy_vs._non-strict)

这里我们先暂时忽略这些差别 .

**性能**

通过使用惰性计算 , 可以确保根据需求去计算需要的值 . 让我们看一个简短而简单的例子来说明这个好处 :

```php
<?php
function wait(int $value): int
{
    // 让我们想象一下,这是一个需要一段时间来计算一个值的函数
    sleep(10);
    return $value;
}

function do_something(bool $a, int $b, int $c): int
{
    if($a) {
        return $b;
    } else {
        return $c;
    }
}

do_something(true, sleep(10), sleep(8));
```

由于PHP不对功能参数进行惰性计算 , 在调用 do\_something 时, 您首先必须等待两次10秒, 然后才开始执行该功能 . 如果 PHP 是一种非即时的语言 , 那么只有我们需要的值才会被计算出来 , 从而除以两个需要的时间 . 它变得更好 , 因为返回值甚至没有保存在一个新的变量中 , 所以可能根本不执行该函数 .

有一种情况 , PHP会执行一种惰性计算 : 即布尔运算符短路 . 当你有一系列布尔运算时 , 一旦PHP可以确定结果 , 它就会停止执行 .

```php
<?php
// “等待”将永远不会被呼叫,因为这些操作符是短路的.
$a= (false && sleep(10));
$b = (true || sleep(10));
$c = (false and sleep(10));
$d = (true or sleep(10));
```

我们可以重写我们以前的例子 , 以利用这一点 . 但是 , 在下面的示例中可以看到 , 它是牺牲可读性的 . 此外 , 我们的例子非常简单 , 而不是您在现实生活中的应用程序代码中遇到的事情 . 想象一下 , 对于具有多个可能分支的一些复杂函数也是一样的 ? 这在以下代码段中显示 :

```php
<?php
($a && sleep(10)) || sleep(8);
```

前面的代码还有两个更大的问题 :

* 如果由于某种原因 , 第一次对睡眠的调用返回一个错误值 , 则第二个调用也将被执行
* 方法的返回值将自动转换为布尔型

**代码可读性**

当你的变量和函数计算是懒惰的 , 你可以花更少的时间考虑哪个是最好的声明顺序 , 或者甚至是你正在计算的数据是否会被使用 . 相反, 您可以集中精力编写可读的代码 . 想象一个博客应用程序有很多帖子, 标签 , 类别 , 并存档年份等 . 您是否希望为每个页面编写自定义查询 , 或使用惰性计算 , 如下所示 :

```php
<?php
// 让我们想象一下,$blogs是一个懒惰计算的集合
// 包含您应用程序的所有博文,按日期排序
$posts = [ /* ... */ ];
// 主页展示的最后10个帖子
return $posts->reverse()->take(10);
// 标签“函数式PHP”的帖子
return $posts->filter(function($b) {
    return $b->tags->contains('functional-php');
})->all();
// 从2014年开始的“生活”类别的分类
return $posts->filter(function($b) {
    return $b->year == 2014;
})->filter(function($b) {
    return $b->category == 'life';
})->pluck('title')->first();
```

要清楚 , 如果我们加载所有帖子 , 这段代码可能会正常工作 , 但表现会相当糟糕 . 然而 , 如果我们惰性计算和ORM足够强大 , 数据库查询可能会延迟到最后一刻 . 那时候 , 我们将会精确地知道我们需要的数据 , SQL将会自动为这个精确的页面量身定制 , 让我们可以轻松地读取代码和出色的性能 .

据我所知 , 这个想法纯粹是假设的 . 我目前不知道任何ORM足够强大 , 即使是在最功能语言中 , 也达不到了这种懒惰水平 . 但是如果真是这样 , 那不是很好吗？

如果您想知道示例中使用的语法 , 看看我们之前讨论的Laravel's Collection的API , 应该会受到启发 .

**无限列表或流**

懒惰计算允许您创建无限列表 . 在Haskell中 , 要获取所有正整数的列表 , 您可以简单地做\[1 ..\] . 那么 , 如果你想要前十个数字 , 你可以拿10 \[1 ..\] . 虽然这个例子不是很令人兴奋 , 但更复杂的也更难理解 .

从PHP5.5起 , PHP支持生成器 . 你可以实现类似的东西 , 在无限列表上使用它们 . 例如 , 我们所有正整数的列表 , 如下 :

```php
<?php
function integers()
{
    $i=0;
    while(true) yield $i++;
}
```

然而 , 懒惰无限列表和我们的PHP生成器之间至少有一个显着的区别 . 例如 , 你可以执行您通常在 "Haskell" 的集合上执行的任何操作, 计算其长度并对其进行排序 . 而我们的生成器是一个迭代器 , 如果您尝试使用“iterator\_to\_array” , 那么您的PHP进程将很有可能挂起 , 直到内存不足 .

那我们应该如何计算一个无限列表的长度或排序 ? 其实很简单 , Haskell是只计算列表值 , 直到它已经足够执行其计算 . 假设我们在PHP中有条件`count($list)<10` , 即使你有一个无限列表 , Haskell一旦达到10就会停止计数项目 , 因为它会在当时比较一个答案 .

#### 代码优化

看看下面的代码 , 哪个更快 : 

```php
<?php
$array= [1, 2, 3, 4, 5, 6 /* ... */];
// version 1
for($i = 0; $i < count($array); ++$i) {
// do something with the array values
}
// version 2
$length = count($array);
for($i = 0; $i < $length; ++$i) {
// do something with the array values
}
```

显然 , 版本2应该要快得多 . 因为您只计算一次数组的长度 , 而在版本1中 , PHP在每次验证 for 循环的条件时都必须计算长度 . 这个例子很简单 , 但是有些情况下这种模式很难发现 . 如果您具有引用透明性 , 则这无关紧要 . 编译器可以自己执行此优化 . 任何借鉴的透明计算都可以在不改变程序结果的情况下移动 . 这是可能的 , 因为我们可以保证每个函数的执行不依赖于全局状态 . 因此 , 在不改变结果的情况下 , 移动计算以获得更好的性能是可能的 . 

另一个可能的改进是消除执行常见的子表达或CSE . 编译器不仅可以更自由地移动部分代码 , 还可以将一些共享一个公共计算的操作转换为使用中间值 . 想象下面的代码 : 

```php
<?php
$a = $foo * $bar + $u;
$b = $foo * $bar * $v;
```

如果计算`$foo * $bar`有很大的成本 , 编译器可以决定使用中间值来转换它 : 

```php
<?php
$tmp= $foo * $bar;
$a = $tmp + $u;
$b = $tmp * $v;
```

同样 , 这是一个很简单的例子 . 这种优化可以在整个代码库的范围内进行 . 

#### 记忆化



