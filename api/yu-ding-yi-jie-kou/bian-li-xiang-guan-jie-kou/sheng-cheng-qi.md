# 生成器

> [http://php.net/manual/zh/language.generators.overview.php](http://php.net/manual/zh/language.generators.overview.php)
>
> [http://www.laruence.com/2015/05/28/3038.html](http://www.laruence.com/2015/05/28/3038.html)
>
> https://laravel-china.org/articles/1430/single-php-generator-complete-knowledge-generator-implementation-process

生成器提供了一种更容易的方法来实现简单的对象迭代 , 相比较定义类实现 Iterator 接口的方式 , 性能开销和复杂性大大降低 .

生成器允许你在 foreach 代码块中写代码来迭代一组数据而不需要在内存中创建一个数组 , 那会使你的内存达到上限 , 或者会占据可观的处理时间 . 相反 , 你可以写一个生成器函数 , 就像一个普通的自定义函数一样 , 和普通函数只返回一次不同的是 , 生成器可以根据需要 yield 多次 , 以便生成需要迭代的值 .

一个简单的例子就是使用生成器来重新实现range\(\)函数 . 标准的range\(\)函数需要在内存中生成一个数组包含每一个在它范围内的值 , 然后返回该数组 , 结果就是会产生多个很大的数组 . 比如 , 调用**range\(0, 1000000\)**将导致内存占用超过 100 MB .

做为一种替代方法 , 我们可以实现一个_xrange\(\)_生成器 , 只需要足够的内存来创建**Iterator**对象并在内部跟踪生成器的当前状态 , 这样只需要不到1K字节的内存 .

```php
<?php

/**
 * @param $start
 * @param $limit
 * @param int $step
 * @return \Generator
 */
function xrange($start, $limit, $step = 1)
{
    // 判断是递增还是递减
    if ($start < $limit) {
        // 递增的step必须是正的
        if ($step <= 0) {
            throw new LogicException("Step必须是正的");
        }
        for ($i = $start; $i <= $limit; $i += $step) {
            yield $i;
        }
    } else {
        // 递减的step必须是负的
        if ($step >= 0) {
            throw new LogicException("Step必须是负的");
        }
        for ($i = $start; $i >= $limit; $i += $step) {
            yield $i;
        }
    }
}

foreach ($obj = xrange(9, 1, -2) as $number) {
    echo "$number \n";
}
var_dump($obj);
var_dump(!$obj instanceof Iterator ?: '迭代器');

foreach ($arr = range(1, 9, 2) as $number) {
    echo "$number \n";
}
var_dump($arr);
```

\(迭代\)生成器也是一个函数,不同的是这个函数的返回值是依次返回,而不是只返回一个单独的值.或者,换句话说,生成器使你能更方便的实现了迭代器接口 . 

当第一次调用生成器\(generator\)函数时 , 将返回内部生成器类的对象 . 此对象实现迭代器接口\( Iterator interface\)的方式与只向前迭代程序对象的方法非常相似 , 并提供了一些方法 , 可以调用它来操纵生成程序的状态 , 包括向其发送值和返回值 . 

