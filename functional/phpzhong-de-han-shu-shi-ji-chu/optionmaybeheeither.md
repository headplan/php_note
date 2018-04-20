# Option/Maybe和Either

如前所述, 我们的解决方案是使用包含所需值的返回类型或发生错误时的其他内容。这些种类的数据结构称为联合类型。联合可以包含不同类型的值, 但一次只能有一个。

我们从本章中将会看到 , 从最简单的两种联合类型开始 .  和往常一样，命名在计算机科学中是一件困难的事情，人们提出了不同的名字来指定大部分相同的结构：

* Haskell称它为Maybe类型，Idris也是这样
* Scala称它为Option类型，OCaml，Rust和ML也是这样
* 自版本8以来，Java具有可选类型，Swift以及下一个C ++规范也是如此

就我个人而言, 我更喜欢这个教派, 因为我认为这是另外一种选择。因此, 本书的其余部分将使用此方法, 除非某个特定库有一个称为 "选项\(Option\)" 的类型。

“Maybe”类型是特殊的 , 它可以持有特定类型的值或相当于任何值 , 或者如果您喜欢的是null值 . 在Haskell中 , 这两个可能的值称为Just和Nothing . 在Scala中 , 它是Some和None , 因为没有用于指定值为null的类型 .

PHP 只实现了一个 "Maybe" 或 "Option" 类型的库, 并且本章后面介绍的一些库也带有这样的类型。但是, 为了正确地理解他们的工作方式和力量, 我们将实施我们自己的。

让我们先重申我们的目标:

* 强制执行错误管理, 以便没有任何错误可以冒泡到最终用户
* 避免样板或复杂的代码结构
* 在我们的功能签名中发布
* 避免将错误误以为正确结果的风险

如果您使用我们将在稍后创建的类型来输入提示您的函数返回值，那么您正在关注我们的第三个目标。 存在两个不同的可能性，即Just和Nothing值，确保您无法将有效结果误认为错误。 为了确保我们不会在某处出现错误的值，我们必须确保我们无法从我们的新类型获取值，而不指定默认值（如果它是Nothing值）。 而且，关于我们的第二个目标，我们将看到我们能写出一些好的东西：

```php
<?php
abstract class Maybe
{
    public static function just($value): Just
    {
        return new Just($value);
    }

    public static function nothing(): Nothing
    {
        return Nothing::get();
    }

    abstract public function isJust(): bool;
    abstract public function isNothing(): bool;
    abstract public function getOrElse($default);
}
```

我们的类有两个静态帮助器方法来创建代表我们两个可能状态的即将到来的子类的两个实例 . 出于性能原因, Nothing值将作为单一实例实现 , 因为它永远不会持有任何值 , 这样做是安全的 .

我们类中最重要的部分是一个抽象的getOrElse函数 , 它将强制任何人想要获取一个值 , 也可以传递一个默认值 , 如果没有 , 返回值 . 这样 , 我们可以强制执行一个有效的值即使在出现错误的情况下也会返回 . 显然 , 您可以将值null作为默认值 , 因为PHP没有机制执行其他操作 , 这有点像搬起石头砸自己的脚 :

```php
<?php
final class Just extends Maybe
{
    private $value;
    public function __construct($value)
    {
        $this->value = $value;
    }

    public function isJust(): bool
    {
        return true;
    }

    public function isNothing(): bool
    {
        return false;
    }

    public function getOrElse($default)
    {
        return $this->value;
    }
}
```

> 搬起石头砸自己的脚的典故 , 可以查看文章
>
> [http://www-users.cs.york.ac.uk/susan/joke/foot.htm](http://www-users.cs.york.ac.uk/susan/joke/foot.htm)

我们的“Just”类很简单 , 一个构造函数和一个getter :

```php
<?php
final class Nothing extends Maybe
{
    private static $instance = null;
    public static function get()
    {
        if(is_null(self::$instance)) {
            self::$instance = new static();
        }
        return self::$instance;
    }

    public function isJust(): bool
    {
        return false;
    }

    public function isNothing(): bool
    {
        return true;
    }

    public function getOrElse($default)
    {
        return $default;
    }
}
```

如果您不考虑单个元素 , Nothing类更简单 , 因为getOrElse函数总是返回默认值 . 对于那些想知道的人来说 , 保持建设者的公开是一个刻意的选择 . 如果有人想直接创建一个Nothing实例 , 它绝对没有任何后果 , 为什么要费心？

我们来测试我们的新Maybe类型 :

```php
<?php
$hello = Maybe::just("Hello World !");
$nothing = Maybe::nothing();
echo $hello->getOrElse("Nothing to see...");
// Hello World !
var_dump($hello->isJust());
// bool(true)
var_dump($hello->isNothing());
// bool(false)
echo $nothing->getOrElse("Nothing to see...");
// Nothing to see...
var_dump($nothing->isJust());
// bool(false)
var_dump($nothing->isNothing());
// bool(true)
```

一切似乎都很好 . 可以改进对样板的需求 . 此时 , 每次要实例化一个新的Maybe类型时 , 都需要检查您所拥有的值 , 并在“Some”和“Nothing”值之间进行选择 .

此外 , 可能会发生这种情况 , 您需要在进一步传递该值之前应用一些函数 , 而不知道什么是最佳的默认值 . 因为在创建一个新的Maybe类型之前获取一些临时默认值是一件麻烦的事情 , 所以我们也尝试修复这个方面 :

```php
<?php
abstract class Maybe
{
    // [...]
    public static function fromValue($value, $nullValue = null)
    {
        return $value === $nullValue ?
        self::nothing() :
        self::just($value);
    }

    abstract public function map(callable $f): Maybe;
}

final class Just extends Maybe
{
    // [...]
    public function map(callable $f): Maybe
    {
        return new self($f($this->value));
    }
}

final class Nothing extends Maybe
{
    // [...]
    public function map(callable $f): Maybe
    {
        return $this;
    }
}
```

为了对实用程序方法有一些一致的命名 , 我们使用与使用集合的功能相同的名称 . 在某种程度上 , 您可以将Maybe类型视为列表中的一个或没有值 . 我们根据相同的假设添加一些其他的实用方法 :

```php
<?php

abstract class Maybe
{
    // [...]
    abstract public function orElse(Maybe $m): Maybe;
    abstract public function flatMap(callable $f): Maybe;
    abstract public function filter(callable $f): Maybe;
}

final class Just extends Maybe
{
    // [...]
    public function orElse(Maybe $m): Maybe
    {
        return $this;
    }

    public function flatMap(callable $f): Maybe
    {
        return $f($this->value);
    }

    public function filter(callable $f): Maybe
    {
        return $f($this->value) ? $this : Maybe::nothing();
    }
}

final class Nothing extends Maybe
{
    // [...]

    public function orElse(Maybe $m): Maybe
    {
        return $m;
    }

    public function flatMap(callable $f): Maybe
    {
        return $this;
    }

    public function filter(callable $f): Maybe
    {
        return $this;
    }
}
```

我们在实施中增加了三新方法 :

* orElse 方法返回当前值 \(如果有\) 或给定值 \(如果没有\) . 这使我们可以轻松地从多个可能的来源获取数据 .
* flatMap 方法对我们的值进行回调 , 但不将其包装在Maybe类中 . 回调的责任是返回Maybe类本身 .
* filter 方法将给定的谓词\(判断\)应用于该值 . 如果谓词\(判断\)返回真值 , 我们保留该值 , 否则返回值Nothing . 

现在, 我们已经实现了一个可以工作的Maybe类型 , 让我们看看如何使用它来轻松的管理并摆脱错误和空的 . 假设我们希望在应用程序的右上角显示有关连接用户的信息 . 如果没有可能的类型 , 您可以执行如下操作 :

```php
<?php
#先获取用户对象
$user = getCurrentUser();
#判断并显示用户名
$name = $user == null ? 'Guest' : $user->name;
echo sprintf("Welcome %s", $name);
// Welcome John
```

在这里, 我们只使用的用户名 , 所以我们可以限制自己到一个空检查 . 如果我们需要来自用户的更多信息 , 通常的方法是使用有时称为 Null 对象模式的模式 . 在我们的例子中 , 我们的 Null 对象将是一个 AnonymousUser 方法的实例 :

```php
<?php
$user = getCurrentUser();
if($user == null) {
    $user = new AnonymousUser();
}
echo sprintf("Welcome %s", $user->name);
// Welcome John
```

现在我们尝试用我们的Maybe类型来做同样的事情 : 

```php
<?php
$user = Maybe::fromValue(getCurrentUser());
$name = $user->map(function(User $u) {
 return $u->name;
})->getOrElse('Guest');
echo sprintf("Welcome %s", $name);
// Welcome John
echo sprintf("Welcome %s", $user->getOrElse(new AnonymousUser())->name);
// Welcome John
```

第一个版本可能不太好, 因为我们必须创建一个新函数来提取名称。但是让我们记住, 在需要提取最终值之前, 您可以对对象进行任何数量的处理。此外, 我们在以后提供的大多数功能库都是以更简单的方式从对象获取值的帮助器方法。

您还可以很容易地调用一个方法链, 直到其中一个返回一个值。假设您希望显示一个仪表板, 但可以根据每个组和每个级别重新定义这些面板。让我们比较一下我们的两种方法的票价。

首先 , 空值检查方法 :

```php
<?php
$dashboard = getUserDashboard();
if($dashboard == null) {
    $dashboard = getGroupDashboard();
}
if($dashboard == null) {
    $dashboard = getDashboard();
}
```

现在 , 使用Maybe类型 :

```php
<?php
/* 我们假设仪表板方法现在返回Maybe实例 */
$dashboard = getUserDashboard()
 ->orElse(getGroupDashboard())
 ->orElse(getDashboard());
```

我觉得更容易识别谁的可读性更强 ! 

最后, 让我们演示一个小例子, 说明如何在一个可能的实例上链接到多个调用, 而不必检查当前是否有值。所选的例子可能有点傻, 但它显示了什么是可能的 : 

```php
<?php
$num = Maybe::fromValue(42);
$val = $num->map(function($n) { return $n * 2; })
    ->filter(function($n) { return $n < 80; })
    ->map(function($n) { return $n + 10; })
    ->orElse(Maybe::fromValue(99))
    ->map(function($n) { return $n / 3; })
    ->getOrElse(0);
echo $val;
// 33
```

我们的Maybe类型的力量是 , 我们从来没有必要考虑实例是否包含一个值 . 我们只需要应用函数 , 直到最后 , 使用getOrElse方法提取最终值 .   




