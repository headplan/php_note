# Closure类

Closure类用于代表匿名函数的类 . 匿名函数（在 PHP 5.3 中被引入）会产生这个类型的对象。在过去，这个类被认为是一个实现细节，但现在可以依赖它做一些事情。自 PHP 5.4 起，这个类带有一些方法，允许在匿名函数创建后对其进行更多的控制。

```php
Closure {
    /* 方法 */
    __construct ( void )
    public static Closure bind ( Closure $closure , object $newthis [, mixed $newscope = 'static' ] )
    public Closure bindTo ( object $newthis [, mixed $newscope = 'static' ] )
}
```

除了此处列出的方法，还有一个_\_\_invoke_方法。这是为了与其他实现了\_\_invoke\(\)魔术方法的对象保持一致性，但调用匿名函数的过程与它无关。

  




