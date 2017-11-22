# Closure类

> 更多关于匿名函数和函数式编程的内容查看前面的笔记

Closure类用于代表匿名函数的类 . 匿名函数（在 PHP 5.3 中被引入）会产生这个类型的对象。在过去，这个类被认为是一个实现细节，但现在可以依赖它做一些事情。自 PHP 5.4 起，这个类带有一些方法，允许在匿名函数创建后对其进行更多的控制。

```php
Closure {
    # 用于禁止实例化的构造函数
    __construct ( void )
    public function __invoke(...$_) { }
    # 复制一个闭包,绑定指定的$this对象和类作用域
    public static Closure bind ( Closure $closure , object $newthis [, mixed $newscope = 'static' ] )
    # 复制当前闭包对象,绑定指定的$this对象和类作用域
    public Closure bindTo ( object $newthis [, mixed $newscope = 'static' ] )
    # 暂时将闭包绑定到newthis,并使用任何给定的参数调用它(PHP7.0)
    function call ($newThis, ...$parameters) {}
    # 新的fromCallable方法提供一个高效和简洁的方式来创建闭包
    public static function fromCallable (callable $callable) {}
}
```

除了此处列出的方法，还有一个\_invoke方法。这是为了与其他实现了\_\_invoke\(\)魔术方法的对象保持一致性，但调用匿名函数的过程与它无关。

#### **Closure::bind和Closure::bindTo**

**Closure::bind**

复制一个闭包，绑定指定的$this对象和类作用域。Closure::bind是Closure::bindTo的静态版本 :

```php
public static Closure bind (Closure $closure , object $newthis [, mixed $newscope = 'static' ])
```

* closure - 表示需要绑定的闭包对象\(匿名函数\)。
* newthis - 表示需要绑定到闭包对象\(匿名函数\)的对象，或者NULL创建未绑定的闭包。
* newscope - 表示想要绑定给闭包的类作用域，可以传入类名或类的实例，默认值是 'static'， 表示不改变。类作用域用来决定在闭包中 $this 对象的 私有、保护方法 的可见性。

该方法成功时返回一个新的 Closure 对象，失败时返回FALSE。

**Closure::bindTo**

复制当前闭包对象，绑定指定的$this对象和类作用域。

```php
public Closure Closure::bindTo ( object $newthis [, mixed $newscope = 'static' ] )
```

* newthis - 绑定给匿名函数的一个对象，或者`NULL`来取消绑定。
* newscope - 关联到匿名函数的类作用域，或者 'static' 保持当前状态。如果是一个对象，则使用这个对象的类型为心得类作用域。 这会决定绑定的对象的 保护、私有成员 方法的可见性。

该方法成功时返回一个新的 Closure 对象，失败时返回FALSE。

> 创建并返回一个匿名函数 , 它与当前对象的函数体相同、绑定了同样变量，但可以绑定不同的对象，也可以绑定新的类作用域。
>
> “绑定的对象”决定了函数体中的_$this_的取值，“类作用域”代表一个类型、决定在这个匿名函数中能够调用哪些 私有 和 保护 的方法。 也就是说，此时 $this 可以调用的方法，与`newscope`类的成员函数是相同的。
>
> 静态闭包不能有绑定的对象（`newthis`参数的值应该设为`NULL`）不过仍然可以改变它们的类作用域。

**Closure::call \(PHP7.0\)**

有着更好的性能，将一个闭包函数动态绑定到一个新的对象实例并调用执行该函数。

```php
public mixed Closure::call ( object $newthis [, mixed $... ] )
```

* newthis - 要将闭包绑定到调用期间的对象。
* ... - 零个或多个参数, 将作为闭包的参数提供。

返回闭包的值 .

**Closure::fromCallable \(7.1\)**

将回调函数转为闭包 .

```php
public static Closure Closure::fromCallable ( callable $callable )
```

使用当前作用域创建并返回一个新的匿名函数。此方法检查函数\(回调函数\)在当前作用域中是否可调用 , 如果不是 , 则抛出 TypeError .

* callable - 要转换的回调函数

返回新创建的闭包, 如果在当前作用域中不可调用, 则会引发 TypeError。

可以当做Observer的闭包模板 . 查看代码示例 . 

**代码示例**

这里关于闭包的示例查看本地代码部分 .

