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

Closure::bind是Closure::bindTo的静态版本 : 

```php
public static Closure bind (Closure $closure , object $newthis [, mixed $newscope = 'static' ])
```

* closure - 表示需要绑定的闭包对象\(匿名函数\)。
* newthis - 表示需要绑定到闭包对象\(匿名函数\)的对象，或者NULL创建未绑定的闭包。
* newscope - 表示想要绑定给闭包的类作用域，可以传入类名或类的实例，默认值是 'static'， 表示不改变。类作用域用来决定在闭包中 $this 对象的 私有、保护方法 的可见性。

该方法成功时返回一个新的 Closure 对象，失败时返回FALSE。

```

```

**代码示例**

这里关于闭包的示例查看本地代码部分 .

