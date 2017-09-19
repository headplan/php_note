# 不变性

#### Immutability

我们说一个变量应该是不可变的 , 如果它被赋值后 , 你就不能改变它的内容 . 在函数的纯度之后 , 函数式编程第二个最重要的事情 .

在一些学术语言中 , 你会听到 : 你不能声明变量 , 一切都必须是一个函数 . 因为所有这些函数都是纯的 , 这意味着其有天然的不变性 . 其中一些语言提供了类似于变量声明的语法糖 , 以避免总是声明函数的潜在问题 .

大多数函数式语言只允许您声明为同一目的服务的不可变变量或构造 . 这意味着您有一种存储值的方法 , 但在初始赋值之后 , 不可能更改数值 . 还有一些语言可以让你为每个变量选择你想要的 . 例如 , 在 Scala 中, 您有 var 关键字来声明传统可变变量和 val 关键字来声明不可变变量 .

然而大多数语言 , 如 PHP 的情况一样 , 没有变量的不变性概念 .

#### 为什么不变性很重要？

首先 , 它有助于减少认知负担 . 要记住算法中涉及的所有变量已经相当困难了 . 如果没有不变性 , 您还需要记住所有的值更改 . 将一个值与特定的标签\(即变量名\)关联起来要容易得多 . 如果你能确定这个值不会改变 , 那么对正在发生的事情进行推理就容易多了 .

另外 , 如果有一些全局的状态你无法摆脱 , 只要它是不可变的 , 就可以记录这些变量的值 , 因为无论在程序执行过程中发生什么 , 所写的总是程序的当前状态 , 这意味着不必启动调试器或输出变量来确保值没有改变 .

假设将一个对象传递给一个函数 . 但不知道函数是否是纯的 , 这意味着可以更改对象属性 . 其在你的头脑中引入了烦恼 , 并分散了你的思维 . 事实上 , 你必须问自己 , 是否内部状态发生了变化 , 从而降低了对代码进行推理的能力 . 如果你的对象是不可变的 , 你可以得到100%的保证 , 它完全和以前一样 , 加速你对正在发生的事情的理解 .

您还有与线程安全和并行化相关的优势 . 如果您的所有状态都是不可变的 , 则更容易确保您的程序能够同时在多个内核或计算机上运行 . 大多数并发问题都是由于某个线程修改了一个值而没有与其他线程进行正确同步而发生的 . 这会导致它们之间的不一致 , 而且通常是计算中的错误 . 如果您的变量是不可变的 , 只要所有线程都被发送相同的数据 , 这种情况就不太可能发生 , 当然 , 这并不真正有用 , 因为 PHP 主要用于非线程方案 .

#### 数据共享

不变性的另一个好处是, 当它由语言本身强制执行时, 编译器可以执行称为数据共享的优化 . 由于 PHP 还不支持这一点, 这里只做简短介绍 .

数据共享是共享包含相同数据的多个变量的公用内存位置的过程 . 这允许更小的内存足迹 , 并 "复制" 数据从一个变量到另一个几乎没有任何成本 .

举个例子 , 想象一下以下的代码 :

```php
<?php
//let's assume we have some big array of data
$array= ['one', 'two', 'three', '...'];
$filtered = array_filter($array, function($i) { /* [...] */ });
$beginning = array_slice($array, 0, 10);
$final = array_map(function($i) { /* [...] */ }, $array);
```

在 PHP 中 , 每个新变量都是数据的一个新拷贝 . 这意味着我们有一个内存和时间成本 , 如果我们的阵列太大 , 可能会成为问题 .

函数式编程可以使用巧妙的技术 , 只将数据存储在内存中 , 然后用另一种方式表示每个变量所包含数据的哪一部分来描述 . 虽然仍然需要一些计算 , 但整体来看 , 还是灰节省很多的内存和时间 .

当然 , 这种优化也可在非不变性的语言中实施 . 但是 , 它通常是不完善的 , 因为必须跟踪每个变量的写访问 , 以确保数据的一致性 . 而且编译器隐含的复杂性被认为超过了这种方法的好处 .

然而 , 在PHP中 , 时间和内存消耗不是很大 , 还可以保证避免使用不变特性  . 因为PHP有一个非常好的垃圾收集器 , 这意味着当一个对象不再被使用时 , 内存的清理非常有效 . 此外 , 我们经常使用相对较小的数据结构 , 这意味着创建几乎相同的数据是相当快的 .

#### 使用常量

可以使用常量和类常量来获得某种不变性 , 但它们只适用于标量值 . 目前没有办法将它们用于对象或更复杂的数据结构 . 既然这是到现在我们唯一的选项 , 就来看看吧 .

可以声明包含任何标量值的全局可用常量 . 从 php 5.6 开始 , 当使用 const 关键字时 , 您还可以在常量中存储一个标量值数组 , 并且 , 自 php 7 以后 , 还支持了define\(\)函数 .

常量名称必须以字母或下划线开头 , 而不是数字 . 通常 , 常量是在全大写 , 所以他们可以很容易地发现 . 它也不鼓励以下划线开头 , 因为它可能与 PHP 核心定义的一些常量冲突 .

```php
<?php
define('FOO', 'something');
const BAR=42;
// PHP 5.6版本才支持
const BAZ = ['one', 'two', 'three'];
// PHP 7之后define也支持了
define('BAZ7', ['one', 'two', 'three']);
// 这样做可以,但是不鼓励这样,因为可能造成冲突
define('__FOO__', 'possible clash');
```

可以使用函数的结果来填充常量 . 但是 , 这仅在使用define\(\)时才有可能 . 如果使用 const 关键字 , 则必须直接使用标量值 :

```php
<?php
define('UPPERCASE', strtoupper('Hello World !'));
```

如果您尝试访问不存在的常量 , PHP将假定您实际上是在尝试将该值用作字符串 :

```php
<?php
echo UPPERCASE;
//display 'HELLO WORLD !'
echo I_DONT_EXISTS;
//PHPNotice: Use of undefined constant
I_DONT_EXISTS
//- assumed'I_DONT_EXISTS'
//display 'I_DONT_EXISTS'anyway
```

这可能存在很大的误导作用 , 因为所假设的字符串将被评估为true , 但如果希望常量保持一个false值 , 那么可能会破坏您的代码 .

如果要避免此陷阱 , 可以使用defined\(\)或constant\(\)函数 . 但是 , 这会增加很多冗长的内容 :

```php
<?php
echo constant('UPPERCASE');
// display 'HELLO WORLD !'
echo defined('UPPERCASE') ? 'true' : 'false';
// display 'true'
echo constant('I_DONT_EXISTS');
// PHP Warning: constant(): Couldn't find constant I_DONT_EXISTS
// display nothings as 'constant' returns 'null' in this case
echo defined('I_DONT_EXISTS') ? 'true' : 'false';
// display 'false'
```

PHP 还允许在类中声明常量 :

```php
<?php
class A
{
    const FOO='some value';
    public static function bar()
    {
        echo self::FOO;
    }
}
echo A::FOO;
// display 'some value'
echo constant('A::FOO');
// display 'some value'
echo defined('A::FOO') ? 'true' : 'false';
// display 'true'
A::bar();
// display 'some value'
```

不过 , 上面的例子中只能直接使用标量值 , 无法使用函数的返回值 , like 用define\(\)函数那样 .

```php
<?php
class A
{
    const FOO=uppercase('Hello World !');
}
// This will generate an error when parsing the file :
// PHP Fatal error: Constant expression contains invalid operations
```

但是 , 从 PHP 5.6 开始 , 就可以使用 const 关键字对任何标量表达式或以前声明的常量 :

```php
<?php
const FOO=6;

class B
{
    const BAR=FOO*7;
    const BAZ="The answer is ": self::BAR;
}
```

在常量和变量的不变性之外还有一个基本的区别 , 通常为作用域规则不通用 . 可以在代码中任意位置使用常量 , 只要它被声明 :

```php
<?php
const FOO='foo';
$bar='bar';

function test()
{
    // here FOO is accessible
    echo FOO;
    // however, if you want to access $bar, you have to use
    // the 'global' keyword.
    global $bar;
    echo $bar;
}
```

在PHP 7.1版本中 , 引入了类常量可见性修饰符 :

```php
<?php
class A
{
    public const FOO='public const';
    protected const BAR='protected const';
    private const BAZ='private const';
}
// public constants are accessible as always
echo A::FOO;
// this will however generate an error
echo A::BAZ;
// PHP Fatal error: Uncaught Error: Cannot access private const
A::BAR
```

#### 一个正在进行的RFC

我们从上面的最后一句警告中看到 , 尽管常量是不可变的 , 但它们是全局的 , 这使它们成为应用程序的状态 . 所以 , 任何使用常量的函数都是不纯的 , 所以你应该谨慎使用它们 .

正如我们刚刚看到的 , 常量对于不变性来说充其量是一条木腿 . 他们很好地存储简单的信息 , 如我们希望每页显示的项目数 . 但是 , 一旦你想拥有一些复杂的数据结构 , 就会被卡主了 .

幸运的是, PHP 核心团队的成员很清楚, 不变性是重要的 , 目前在 RFC 中正在进行一些工作, 将其包含在语言级别里 .

> [https://wiki.php.net/rfc/immutability](https://wiki.php.net/rfc/immutability)

对于那些不熟悉PHP新特性的进度的人 , RFC让核心团队成员向PHP添加新内容的一个建议 . 这个命题首先通过一个草稿阶段 , 在这里写下并完成一些例子的实现 . 之后 , 有一个讨论阶段 , 其他人可以给出建议和建议 . 最后 , 进行表决以决定是否将该功能包含在下一个 PHP 版本中 .

目前看来 , 不可变的类和属性 RFC 仍处于草稿阶段 . 没有真正的论据 , 无论是为或反对它 , 只有时间能证明它是否被接受 .

#### 值对象

> [https://en.wikipedia.org/wiki/Value\_object](https://en.wikipedia.org/wiki/Value_object)

在计算机科学中 , 值对象是一个小对象 , 它表示一个简单的实体 , 其等式不基于标识 . 也就是说 , 即当两个值对象具有相同的值时 , 它们是相等的 , 不一定是同一个对象 .

值对象应该是不可变的 : 对于创建了相等的两个值对象的隐式协定 , 这是必需的 , 应保持相等 . 对于值对象是不可变的也很有用 , 因为客户端代码不能将值对象置于无效状态 , 也不能在实例化后引入 bug 行为 .

由于没有在 PHP 中获得真正的不可变性的意思 , 它通常是通过拥有私有属性 , 而不是类上的 setter 来实现的 . 因此 , 当开发人员要修改某个值时 , 它会强制创建一个新对象 . 该类还可以提供实用方法来简化新对象的创建 . 让我们看一个简短的例子 :

```php
<?php
class Message
{
    private $message;
    private $status;

    public function __construct(string $message, string $status)
    {
        $this->status = $status;
        $this->message = $message;
    }

    public function getMessage()
    {
        return $this->message;
    }

    public function getStatus()
    {
        return $this->status;
    }

    public function equals($m)
    {
        return $m->status === $this->status &&

        $m->message === $this->message;
    }

    public function withStatus($status): Message
    {
        $new = clone $this;
        $new->status = $status;
        return $new;
    }
}
```

此类模式可用于创建从数据使用者的角度看不可变的数据实体 . 但是 , 您必须特别注意保证类上的所有方法不破坏不变性 , 否则你的努力将会毫无意义 . 此外, 如果你不是以数组的形式使用它们 , 则可以 :

* 使用它们作为类型提示, 而不是简单的数组
* 避免由于一个错误的带来的任何错误
* 强制执行某些项目的存在或格式
* 为不同的上下文提供格式化值的方法

值对象的常见用途是存储和操作与货币相关的数据 . 你可以看看 [http://money.rtfd.org](http://money.rtfd.org) , 这是一个很好的例子, 告诉你如何有效地使用它们 .

值对象的另一个重要的用法是PSR-7 : "HTTP 消息接口" . 此标准引入并形式化了一种框架和应用程序以可互操作的方式管理 HTTP 请求和响应的方法 . 所有主要框架都有核心支持或插件可用 . 阅读他们的完整理论 , 其说明l 为什么您应该对 PHP 生态系统中如此重要的部分使用不变性 : [http://www.php-fig.org/psr/psr-7/meta/\#why-value-objects](http://www.php-fig.org/psr/psr-7/meta/#why-value-objects)

实质上 , 将 HTTP 消息建模为值对象可以确保消息状态的完整性 , 并防止对双向依赖关系的需要 , 这种依赖性通常会失去同步或导致调试或性能问题 .

在PHP中 , 值对象是获得某种类型的不变性的一种很好的方式 . 虽然它没有给你带来更多的好处 , 特别是那些与绩效相关的 , 但是大部分的认知负担都被移除了 . 要了解更多 , 可以查看这个网站 , [http://www.phpvalueobjects.info/](http://www.phpvalueobjects.info/) , 但是网站好像已经无法打开 , 关注了Twitter , 其中可以发现一些很好的内容 .

#### 不可变集合的库



