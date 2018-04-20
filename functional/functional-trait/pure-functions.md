# Pure functions

#### 纯函数

假设有一个函数签名函数`getCurrentTvProgram(Channel $channel)` , 这个函数没有指示任何的纯度 , 也不知道这个函数背后可能隐藏的复杂性 .

你可能会得到实际在给定频道上播放的程序 . 你不知道的是 , 如果你登录到系统中 , 该函数是否检查过 . 也许为了分析 , 有某种数据库更新了数据 . 可能该函数还将返回一个异常 , 因为日志文件处于只读状态 . 你不能确切地知道 , 所有上述的问题或副作用 . 那么对于与这些隐藏依赖项相关的所有复杂性 , 你将面临三选项 :

* 深入到文档或代码中 , 了解所发生的一切
* 让依赖关系更加的明显
* 什么也不做 , 求佛 ...

最后一项纯属凑选项 , 第一个选择可能是最好的 , 但是你的同事其他所有人都要和你一样深入文档 , 看一遍 . 答案显而易见 ...

#### 关于封装

封装是为了隐藏实现细节 . 纯度是为了使隐藏的依赖性已知 . 两者都是有用的, 更好的做法是让他们没有任何形式的冲突 . 如果你足够细心 , 那么你就可以实现这两个目标 , 哈哈哈 . 他们喜欢干净 , 简单的解决方案 .

用简单的术语解释 :

* 封装是关于隐藏内部实现
* 避免副作用的原因是让外部输入已知
* 避免副作用的结果是使外部变化已知

#### 发现副作用原因

再来看看`getCurrentTvProgram(Channel $channel)`函数 , 下面的实现不是Pure的 , 问题出在哪呢? 根据现在我们知道的Pure函数的知识 : 当使用相同的参数调用时 , 它们总是返回相同的结果 .

```php
<?php
function getCurrentTvProgram(Channel $channel): string
{
    // 假设这里的getProgramAt()是一个Pure方法.
    return $channel->getProgramAt(time());
}
```

找到了?对 , 就是time\(\)函数 , 让不同时间调用返回的结果不同了 , 接下来改进一下 :

```php
<?php
functiongetTvProgram(Channel $channel, int $when): string
{
    return $channel->getProgramAt($when);
}
```

OK , 现在我们的函数已经Pure了 , 而且现在我们可以按名称去更改一天中的任何时间 , 还可以不用改变当前的时间去测试函数 .

下面的例子也是一个隐藏输入带来的副作用 , 看看他们是为啥不纯的 :

```php
<?php
$counter = 0;
function increment()
{
    global $counter;
    return ++$counter;
}

function increment2()
{
    static $counter = 0;
    return ++$counter;
}

function get_administrators(EntityManager $em)
{
    // Let's assume $em is a Doctrine EntityManager allowing
    // to perform DB queries
    return $em->createQueryBuilder()
    ->select('u')
    ->from('User', 'u')
    ->where('u.admin = 1')
    ->getQuery()->getArrayResult();
}

function get_roles(User $u)
{
    return array_merge($u->getRoles(), $u->getGroup()->getRoles());
}
```

* 第一个例子 , global让其使用全局范围中的一些变量 , PHP的范围规则也告诉我们 , 所有使用global的函数 , 都可能不纯洁 .
* 第二个示例中的静态关键字是一个很好的指示器 , 我们可以尝试在函数调用之间存储状态 . 在本例中 , 它是在每次运行时递增的计数器 . 这个函数显然是不纯的 . 但是 , 与全局变量相反 , 静态关键字的使用可能只是在调用之间缓存数据的一种方式 , 因此必须在得出结论之前检查使用它的原因 . 
* 第三个例子也是不纯的 , 原因是数据库的访问 . 那你会问 , 如果只允许Pure函数 , 那应该如何从数据库获取数据 . 这个问题后面会详细讨论 . 
* 关于第四函数 , 你是不能仅仅通过看它就知道它是否纯净 . 你将需要查看调用的方法的代码 . 大多数情况下会遇到这样的问题 , 一个函数调用其他函数和方法 , 你也将不得不去阅读 , 以确定纯度 . 

#### 发现副作用结果

通常情况下 , 输出的副作用比输入的副作用容易发现一些 . 任何时候 , 如果更改的值在外部会有明显的效果, 或者调用另一个函数, 那么就会产生副作用 , 我们再来看看上面前两个递增的例子 : 

```php
<?php
$counter = 0;
function increment()
{
    global $counter;
    return ++$counter;
}
function increment2()
{
    static $counter = 0;
    return ++$counter;
}
```

第一个明确对全局变量的副作用 . 但是第二个呢 ? 变量本身不能从外部访问 , 那么我们可以考虑这个函数没有副作用吗 ? 答案是否定的 , 因为这个变化意味着对函数的后续调用将返回另一个值 , 这也可以作为副作用的 . 

再看一些其他的例子 : 

```php
<?php
function set_administrator(EntityManager $em, User $u)
{
    $em->createQueryBuilder()
    ->update('models\User', 'u')
    ->set('u.admin', 1)
    ->where('u.id = ?1')
    ->setParameter(1, $u->id)
    ->getQuery()->execute();
}
function log_message($message)
{
    echo $message."\n";
}
function updatePlayers(Player $winner, Player $loser, int $score)
{
    $winner->updateScore($score);
    $loser->updateScore(-$score);
}
```

* 第一个函数显然具有副作用 , 因为我们更新了数据库中的值 . 
* 第二种方法在屏幕上打印一些东西 . 通常 , 这被认为是一种副作用 , 因为该函数对其范围之外的某物有影响 . 
* 最后一个函数可能会产生副作用 . 这是一个基于方法名称的专业的猜测 . 但我们不能给肯定的答案 , 直到我们看到的方法的代码来验证它 . 要发现原因 , 你往往需要挖更深一点 , 而不是只是一个功能, 以确定它是否会导致副作用 . 

#### 关于对象方法

在纯函数式语言中 , 只要您需要更改对象、数组或任何类型的集合中的值 , 您实际上就会返回一个具有新值的副本 . 这意味着任何方法 , 如前面的提到的`updateScore`方法 , 不会修改对象的内部属性 , 但会以新的分数返回新实例 . 那不是所有的都是不纯的么 ? 这里可以从另一个角度去思考 , 我们可以做到的是在更改后确定该实例的值不是相同 . 举个例子 : 

```php
<?php
class Test
{
    private $value;
    public function __construct($v)
    {
        $this->set($v);
    }

    public function set($v) {
        $this->value = $v;
    }
}

function compare($a, $b)
{
    echo ($a == $b ? 'identical' : 'different')."\n";
}

$a = new Test(2);
$b = new Test(2);
compare($a, $b);
// identical
$b->set(10);
compare($a, $b);
// different
$c = clone $a;
$c->set(5);
compare($a, $c);
```

在两个对象之间进行简单的相等比较时, PHP 会考虑内部值, 而不是实例本身来进行比较 . 需要注意的是 , 当您使用严格的比较 \(例如, 使用 === 运算符\) 时, PHP 将验证两个变量是否都保持同一实例 , 并在所有三案例中返回 "不同" 字符串 . 

但是 , 这与引用透明度的概念不兼容 , 我们将在本章后面讨论 . 

#### 结束语

由于翻译或者一些未知的概念 , 这部分内容可能会让你迷惑 , 可以读一读最后的扩展内容 , 这里给出检查函数是否纯净的最佳操作过程的内容 : 

* 放弃使用global关键字
* 检查您是否使用了不是函数本身的参数的任何值
* 确认你所调用的所有函数都是纯函数
* 对外部存储的任何访问都是不纯的 \(例如,数据库和文件\)
* 特别注意返回值依赖于外部的函数
* 状态 \(time时间, random随机\)

---

> https://zh.wikipedia.org/wiki/纯函数

在程序设计中，若一个函数符合以下要求，则它可能被认为是纯函数：

* 此函数在相同的输入值时，需产生相同的输出。函数的输出和输入值以外的其他隐藏信息或状态无关，也和由I/O设备产生的外部输出无关。
* 该函数不能有语义上可观察的函数副作用，诸如“触发事件”，使输出设备输出，或更改输出值以外物件的内容等。

纯函数的输出可以不用和所有的输入值有关，甚至可以和所有的输入值都无关。但纯函数的输出不能和输入值以外的任何资讯有关。纯函数可以传回多个输出值，但上述的原则需针对所有输出值都要成立。若引数是传引用调用，若有对参数物件的更改，就会影响函数以外物件的内容，因此就不是纯函数。

---

Pure Function，直译一下就是「纯函数」。

1.在给定同样的参数的前提下 , Pure Function 都会返回同样的结果 . 即Pure Function 的返回值 , 只能由其函数入参所决定 , 而不能有其他的干扰因素 . 

2.Pure Function 在计算返回值的时候不会产生 Side Effect

所谓 Side Effect 就是我们在平时经常做的一些工作 , 例如 : I/O 操作 , 修改函数入参或函数外部的变量，抛出异常等 . 即 , 会产生 Side Effect 的 Function 不是真正的 Pure Function . 

**每个 Pure Function 的运行结果都是可重现的 . 这样的好处就是 Pure Function 十分容易进行测试 , 因为这完全是一个透明的盒子 . **



