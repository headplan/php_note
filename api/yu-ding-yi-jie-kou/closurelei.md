# Closure类

> 更多关于匿名函数和函数式编程的内容查看前面的笔记

Closure类用于代表匿名函数的类 . 匿名函数（在 PHP 5.3 中被引入）会产生这个类型的对象。在过去，这个类被认为是一个实现细节，但现在可以依赖它做一些事情。自 PHP 5.4 起，这个类带有一些方法，允许在匿名函数创建后对其进行更多的控制。

```php
Closure {
    # 用于禁止实例化的构造函数
    __construct ( void )
    # 复制一个闭包，绑定指定的$this对象和类作用域
    public static Closure bind ( Closure $closure , object $newthis [, mixed $newscope = 'static' ] )
    # 复制当前闭包对象，绑定指定的$this对象和类作用域
    public Closure bindTo ( object $newthis [, mixed $newscope = 'static' ] )
}
```

除了此处列出的方法，还有一个\_invoke方法。这是为了与其他实现了\_\_invoke\(\)魔术方法的对象保持一致性，但调用匿名函数的过程与它无关。

**代码示例**

这里的几个匿名函数的示例查看本地代码部分 . 

