# 异常

错误管理是编写软件时遇到的最棘手的问题之一。通常很难决定哪段代码应该处理错误。在低级函数中执行此操作, 您可能无法访问这些设施, 以显示错误消息或足够的上下文来决定最佳的行动路线。把它做得更高一些, 这可能会破坏你的数据或使应用程序处于不可恢复的状态。

在 OOP 代码中, 管理错误的通常方法是使用异常。您在库或实用程序代码中抛出一个异常, 当您准备好按需要管理它时就会捕获它。

无论是异常抛出或捕捉可以被认为是副作用还是不是副作用, 甚至在学术界都是争论的问题。有各种各样的观点。我不想用修辞的观点来烦你, 所以让我们坚持一些几乎每个人都同意的观点 : 

* 由任何外部源引发的异常 \(数据库访问、文件系统错误、不可用的外部资源、无效的用户输入等\) 本身都是不纯的, 因为访问这些源已是一个侧面原因。
* 由于逻辑错误 \(索引外的绑定、无效的类型或数据等\) 而引发的异常通常被认为是纯的, 因为它可以被认为是函数的有效返回值。但是, 必须将例外情况清楚地记录在案, 作为一个可能的结果。
* 捕获异常会中断引用透明度, 从而使任何函数与 catch 块不纯。

前两条应该是相当容易理解的, 但第三句话呢？让我们用一小段代码开始演示:

```php
<?php
function throw_exception()
{
    throw new Exception('Message');
}

function some_function($x)
{
    $y = throw_exception();
    try {
        $z = $x + $y;
    } catch(Exception $e) {
        $z = 42;
    }
    
    return $z;
}
echo some_function(42);
// PHP Warning: Uncaught Exception: Message
```

很容易看到, 我们对 some\_function 函数的调用将导致捕获异常, 因为对 throw\_exception 函数的调用超出了try ... catch块 . 现在, 如果我们应用引用透明度的原则, 我们应该能够用它的值来替换 $y 参数。让我们尝试一下:

```php
<?php
try {
    $z = $x + throw_exception();
} catch(Exception $e) {
    $z = 42;
}
```

现在 $z 参数的值是什么, 我们的函数将返回什么？与以前相反, 我们现在将有一个返回值 42, 显然改变了调用我们的函数的结果。通过简单地尝试应用等式推理, 我们刚刚证明 , 捕捉异常可以破坏参照透明度 . 

如果你不能“抓住”他们，那么“异常”有什么好处？ 不是很多 . 这就是为什么我们不会在后面的内容中使用它们的原因 . 但是，您可以将其视为副作用，然后应用我们将在第6章“Real-Life Monads”中看到的技术来进行管理。 例如，Haskell允许抛出异常，只要它们被使用IO Monad捕获 . 

另一个问题是认知负担。一旦你使用它们, 你就不能确定他们何时会被抓住;它们甚至可能直接显示给最终用户。这就破坏了对一条代码本身进行推理的能力, 因为您现在必须考虑将会发生什么更高的事情。

此问题通常是您听到的建议, 例如只对错误使用异常, 而不是流控制。这样, 您至少可以确定您的异常将用于显示某种错误, 而不是想知道您将应用程序放在哪个状态。

#### PHP7的异常

即使我们主要是在一个负面的讨论异常 , 让我借此机会介绍在新的 PHP 版本中所做的关于这个主题的改进 . 

以前, 某些类型的错误会产生致命错误或错误, 这将停止脚本的执行并显示错误消息。您可以使用 set\_error\_handler 异常来定义非致命错误的自定义处理程序, 并最终继续执行。

PHP 7.0 引入了一个 Throwable 接口, 它是异常的新父级。Throwable 类还有一个名为 Error 类的新子级, 您可以使用它来捕获以前无法管理的大多数错误。仍然有一些错误, 如分析错误, 你可以明显不能捕捉, 因为它意味着你的整个 PHP 文件是不知何故无效。

让我们用一段代码来演示它, 它试图调用一个对象上不存在的方法:

```php
<?php
class A {}
$a = new A();
$a->invalid_method();
// PHP Warning: Uncaught Error: Call to undefined method
A::invalid_method()
```

如果您使用的是 PHP 5.6 或更低的代码, 则该消息将沿着以下内容进行说明 : 

> Fatal error: Call to undefined method A::invalid\_method\(\)

但是, 使用 PHP 7.0, 消息将是 \(重点是我的\):

> Fatal error: Uncaught Error: Call to undefined method A::invalid\_method\(\)

不同的是, PHP 通知你, 这是一个捕获的错误。这意味着您现在可以使用通常的 `try...catch` 您可以直接捕获错误类, 或者如果希望更广泛并捕获任何可能的异常, 则可以使用 Throwable 接口。但是, 我不鼓励这样做, 因为您将丢失有关正确的错误信息:

```php
<?php 
class B {}
$a = new B();
try {
    $a->invalid_method();
} catch(Error $e) {
    echo "An error occured : ".$e->getMessage();
}
// An error occured : Call to undefined method B::invalid_method()
```

同样有趣的是, TypeError 参数是错误类的子级, 当函数被调用错误类型的参数或返回类型错误时引发:

```php
<?php
function add(int $a, int $b): int
{
    return $a + $b;
}
try {
    add(10, 'foo');
} catch(TypeError $e) {
    echo "An error occured : ".$e->getMessage();
}
// An error occured : Argument 2 passed to add() must be of the type integer, string given
```

对于那些想知道为什么在新的错误类旁边创建新接口的人, 主要原因有两个:

* 要清楚地将异常接口与以前的内部引擎错误分开
* 为了避免破坏捕获异常接口的现有代码, 让开发人员选择是否也要开始捕获错误



