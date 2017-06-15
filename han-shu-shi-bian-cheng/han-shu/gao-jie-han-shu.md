# 高阶函数

PHP函数可以将函数作为参数 , 也可以将函数作为返回值 . 一个函数可以调用高阶函数 . 没什么惊喜 .

#### 什么是callback?

callback是一个类型提示 , 可以用来强制函数的参数是回调函数 , 从PHP7开始callback也可以作为返回值的类型提示 .

```php
<?php
function test_callable(callable $callback) : callable
{
    $callback();
    return function() {
        // [...]
    };
}
```

但是 , 使用类型提示不能强制执行的是调用方应该拥有的参数的数目和类 , 不过保证有你可以调用的东西已经很好了 .

callable的形式 :

* 一个普通函数名的字符串
* 用于类方法或静态函数的数组



