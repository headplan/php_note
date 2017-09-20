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





非严格或懒惰评估

性能

代码可读性

无限列表或流

代码优化

记忆化

