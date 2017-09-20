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







代码可读性

无限列表或流

代码优化

记忆化
