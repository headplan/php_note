# Functors

在直接讲Monad之前 , 让我们从头开始 . 为了理解Monad是什么 , 我们需要引入一些相关的概念 . 首先 ,第一个就是Functors .

说的复杂一点 , 在函数式编程中 , 用Functors来描述函数对象 , 但和我们平常理解的函数对象的概念是完全不同的 . 在PHP中 , 具有\_\_invoke方法的对象 , 就是一个这样的函数对象 .

然而 , 在函数式编程中 , Functors是从类别理论的数学领域中获取和改编的概念 . 其细节对这里就不深究了 , 只要了解一个Functors就是一个模式 , 让我们可以将函数映射到上下文中 , 并且包含的一个或多个值 . 另外 , 为了使定义尽可能完整，Functors必须遵守一些规则或者说约定 , 这些我们将在后面进行描述和验证 .

我们已经在集合中多次使用了Map , 事实上我们已经接触了Functors . 但是 , 如果没记错的话 , 我们也自定义了一个我们的方法函数 , 以相同的方式将函数应用于Maybe中包含的值 . 其原因是 , "Functors" 可以被看作是一种将函数应用于所包含的值的容器 .

在某种意义上 , 实现以下接口的任何类都可以称为Functor :

```php
<?php
interface Functor
{
    public function map(callable $f): Functor;
}
```

然而 , 这样解释可能有点笼统 . 比如 ,  一个简单的PHP数组也是一个Functor\(因为array\_map函数存在\) , 就像任何实现 "Traversable\(遍历\)" 接口的东西一样 , 只要你使用 "functional-php" 库及其 "map" 函数 , 也是一个道理 .

为什么这么简单的想法如此小题大做 ? 因为, 尽管这个概念本身很简单, 但它允许我们对发生在不同的情景中的事情进行推理 , 并可能帮助理解和重构代码 .

此外, "map" 函数可以比盲目应用给定的 "回调" 类型更重要, 就像使用 "array" 的情况一样 . 如果您还记得我们的 "Maybe" 类型的实现, 在一个值 "无" 的情况下, "map" 函数简单地保持返回 "无" 值, 以便能够更简单地管理 null 值 .

我们也可以想象在我们的Functor中有更复杂的数据结构 , 比如树 , 其中赋予Map函数的Functor被应用于所有节点 .

Functors允许我们共享一个通用接口 , 我们的映射方法或函数 , 以便在隐藏实现的复杂性的同时对各种数据类型执行类似的操作 . 与函数式编程一样 , 认知负担减少 , 因为您不具有相同操作的多个名称 . 例如"apply", "perform", "walk" 等函数和方法名称经常被看作是描述相同的事情 .

> 百度百科
>
> [https://baike.baidu.com/item/仿函数](https://baike.baidu.com/item/仿函数)
>
> 仿函数\(functor\)，就是使一个类的使用看上去像一个函数。其实现就是类中实现一个operator\(\)，这个类就有了类似函数的行为，就是一个仿函数类了。
>
> 在我们写代码时有时会发现有些功能实现的代码，会不断的在不同的成员函数中用到，但是又不好将这些代码独立出来成为一个类的一个成员函数。但是又很想复用这些代码。写一个公共的函数，可以，这是一个解决方法，不过函数用到的一些变量，就可能成为公共的全局变量，再说为了复用这么一片代码，就要单立出一个函数，也不是很好维护。这时就可以用仿函数了，写一个简单类，除了那些维护一个类的成员函数外，就只是实现一个operator\(\)，在类实例化时，就将要用的，非参数的元素传入类中。这样就免去了对一些公共变量的全局化的维护了。又可以使那些代码独立出来，以便下次复用。而且这些仿函数，还可以用关联，聚合，依赖的类之间的关系，与用到他们的类组合在一起，这样有利于资源的管理（这点可能是它相对于函数最显著的优点了）。如果在配合上模板技术和policy编程思想，那就更是威力无穷了，大家可以慢慢的体会。
>
> > Operator是C++和pascal的关键字，它和运算符一起使用，表示一个运算符函数，理解时应将operator=整体上视为一个函数名。

#### 恒等函数

通过上面的概念 , 我们得到两个functor的规律 , 在介绍规律之前 , 先来看看本小节的主题 , 身份函数 , 或者叫id函数 , 它是一个非常简单的函数 , 只需返回其参数 :

```php
<?php
function id($value)
{
    return $value;
}
```

为什么会需要一个这样的函数 , 而且它是真实存在的 .

例如 , 当你做一个数字折叠 , 说要求和 , 你会使用初始值0 . 在函数折叠时 , "id" 函数将具有相同的作用 . 实际上 , "compose" 函数是使用 "functional-php" 库中的 "id" 函数实现的 .

另一个用途可能是来自另一个库的某个函数, 它执行您感兴趣的操作, 但也会对结果数据调用回调 . 如果该回调是必需的 , 但您不想对数据执行任何其他工作, 只需将它们原封不动地传递给id函数即可 .

让我们使用我们的新函数来声明我们的函数 f 的一个属性, 只考虑一个参数 :

```
compose(id, f) == compose(f, id)
```

这基本上是说, 如果你首先应用参数 id, 然后 f, 你会得到完全相同的结果, 当第一次应用 f, 然后 id。在这一点上, 这应该是显而易见的给你。如果没有, 我鼓励你重温最后一章, 直到你清楚地明白为什么会是这样。

#### Functor定律

现在我们有了我们的身份函数 , 让我们回到我们的定律上 , 它们很重要 , 原因有两个 :

* 他们给我们一个约束 , 以保证我们的Functor的有效性
* 它们允许我们执行并保证我们的重构是正确的

不用多说 , 他们是 :

1. map\(id\) == id
2. compose\(map\(f\), map\(g\)\) == map\(compose\(f, g\)\)

第一定律指出, 在所包含的值上映射 id 函数与在函子本身上直接调用 id 函数是完全相同的 . 当这项定律成立时 , 这保证了我们的 map 函数只将给定的函数应用到数据中, 而不执行任何其他类型的处理 .

第二定律说, 第一个映射 f 函数, 然后映射 g 函数 与 首先将 f 和 g 组合到一起 , 然后映射产生的结果是相同的 . 知道这一点, 我们可以执行各种优化 . 例如, 我们可以将它们组合在一起, 只执行一个循环, 而不是将三不同方法的数据循环三次 .

```php
<?php
$data = [1, 2, 3, 4];
var_dump(array_map('id', $data) === id($data));
// bool(true)

function add2($a)
{
    return $a + 2;
}

function times10($a)
{
    return $a * 10;
}

function composed($a)
{
    return add2(times10($a));
}

var_dump(
    array_map('add2', array_map('times10', $data))
    === array_map('composed', $data)
);
// bool(true)
```

该组合是手动执行的 , 没有进行 "currying" , 以免让事情变得更复杂 .

正如我们所看到的 , 这两个定律都保留了 array\_map 方法 , 这是一个好兆头 , 这意味着没有隐藏的进行一些数据处理 , 而且我们可以避免在我们的数组上循环两次或更多次 , 只有一次就足够了 .

让我们尝试一下我们之前定义的Maybe类型 :

```php
<?php
$just = Maybe::just(10);
$nothing = Maybe::nothing();
var_dump($just->map('id') == id($just));
// bool(true)
var_dump($nothing->map('id') === id($nothing));
// bool(true)
```

这里 , 我们不得不把$just部分转换为非严格的模式\(即两个等号\) , 否则返回的结果为false . 因为PHP比较实例对象 , 而不是它们的value值 . Maybe类型将结果值包装在一个新的对象中 , 而PHP只在非严格相等比较的情况下才执行内部值比较 , 上面定义的 add2、times10 和组合函数也是这样 :

```php
<?php
var_dump($just->map('times10')->map('add2') == $just->map('composed'));
// bool(true)
var_dump($nothing->map('times10')->map('add2') === $nothing->map('composed'));
// bool(true)
```

#### 恒等Functor

正如前面在关于身份函数的部分中讨论的 , 身份Functor也是存在的 . 它作为一个非常简单的Functor，除了持有它 , 对 "value" 没有任何作用 :

```php
<?php

class IdentityFunctor implements Functor
{
    private $value;

    public function __construct($value)
    {
        $this->value = $value;
    }

    public function map(callable $f): Functor
    {
        return new static($f($this->value));
    }

    public function get()
    {
        return $this->value;
    }
}
```

与恒等函数一样 , 对这样的Functor的使用并不是立竿见影的 . 然而 , 当你有一些函数具有Functor作为参数 , 但是你不想修改其实际值时 , 这个想法和上面是一样的 , 可以使用它 . 

