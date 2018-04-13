# 生成器

> [http://php.net/manual/zh/language.generators.overview.php](http://php.net/manual/zh/language.generators.overview.php)
>
> [http://www.laruence.com/2015/05/28/3038.html](http://www.laruence.com/2015/05/28/3038.html)
>
> [https://laravel-china.org/articles/1430/single-php-generator-complete-knowledge-generator-implementation-process](https://laravel-china.org/articles/1430/single-php-generator-complete-knowledge-generator-implementation-process)

#### 迭代和迭代器Iterator

迭代是指反复执行一个过程 , 每执行一次叫做一次迭代 . 前面已经介绍了遍历迭代接口等\(Iterator\) .

#### 生成器Generator

虽然迭代器仅需继承接口即可实现 , 但依旧很麻烦 , 我们毕竟需要定义一个类并实现该接口所有方法 , 这十分繁琐 . 在一些情景下我们需要更简洁的办法 .

**生成器提供了一种更容易的方法来实现简单的对象迭代 , 相比较定义类实现 Iterator 接口的方式 , 性能开销和复杂性大大降低 . **

**官方文档**

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

实际上`生成器`生成的正是一个`迭代器对象实例` . 该迭代器对象继承了 Iterator 接口 , 同时也包含了`生成器对象`自有的接口 .

---

#### 生成器语法

一个生成器函数看起来像一个普通的函数 , 不同的是普通函数返回一个值 , 而一个生成器可以yield生成许多它所需要的值 .

当一个生成器被调用的时候 , 它返回一个可以被遍历的对象 . 当你遍历这个对象的时候\(例如通过一个foreach循环\) , PHP 将会在每次需要值的时候调用生成器函数 , 并在产生一个值之后保存生成器的状态 , 这样它就可以在需要产生下一个值的时候恢复调用状态 .

一旦不再需要产生更多的值 , 生成器函数可以简单退出 , 而调用生成器的代码还可以继续执行 , 就像一个数组已经被遍历完了 .

> **Note**:
>
> 一个生成器不可以返回值 : 这样做会产生一个编译错误 . 然而**return**空是一个有效的语法并且它将会终止生成器继续执行 .

#### yield

需要注意的关键是`yield` , 这是生成器的关键 . 我们通过上面例子 , 可以看得出 , `yield`会将当前一个值传递给 foreach , 换句话说 , foreach 每一次迭代过程都会从 yield 处取一个值 , 直到整个遍历过程不再存在 yield 为止的时候 , 遍历结束 .

我们也可以发现 , yield 和 return 都会返回值 , 但区别在于一个 return 是返回既定结果 , 一次返回完毕就不再返回新的结果 , 而 yield 是不断产出直到无法产出为止 .

实际上存在 yield 的函数返回值返回的是一个`Generator`对象\(**这个对象不能手动通过 new 实例化\) , **该对象实现了`Iterator`接口 .

```php
# 核心yield关键字

/**
 * @return \Generator
 */
function gen_one_to_three() {
    for ($i = 1; $i <= 3; $i++) {
        // 注意变量$i的值在不同的yield之间是保持传递的
        yield $i;
    }
}

foreach (gen_one_to_three() as $value) {
    echo "$value\n";
}
```

yield 和 return 的区别 , 前者是暂停当前过程的执行并返回值 , 而后者是中断当前过程并返回值 . 暂停当前过程 , 意味着将处理权转交由上一级继续进行 , 直至上一级再次调用被暂停的过程 , 该过程则会从上一次暂停的位置继续执行 .

**yield语法**

如果在一个表达式上下文\(例如在一个赋值表达式的右侧\)中使用yield , 必须使用圆括号把yield申明包围起来 . 例如这样是有效的 :

```
$data = (yield $value);
```

> 注意 : php7中没有括号限制 .

**指定键名来生成值**

PHP的数组支持关联键值对数组 , 生成器也一样支持 . 所以除了生成简单的值 , 你也可以在生成值的时候指定键名 .

如下所示 , 生成一个键值对与定义一个关联数组十分相似 :

```php
# 指定键名
/*
 * 下面每一行是用分号分割的字段组合，第一个字段将被用作键名。
 */
$input = <<<'EOF'
1;PHP;Likes dollar signs
2;Python;Likes whitespace
3;Ruby;Likes blocks
EOF;

function input_parser($input) {
    foreach (explode("\n", $input) as $line) {
        $fields = explode(';', $line);
        $id = array_shift($fields);

        yield $id => $fields;
    }
}

foreach (input_parser($input) as $id => $fields) {
    echo "$id:\n";
    echo "    $fields[0]\n";
    echo "    $fields[1]\n";
}
```

**生成null值**

Yield可以在没有参数传入的情况下被调用来生成一个`NULL`值并配对一个自动的键名 .

```php
function gen_three_nulls() {
    foreach (xrange(1, 3) as $i) {
        yield;
    }
}

var_dump(iterator_to_array(gen_three_nulls()));
```

**使用引用来生成值**

生成函数可以像使用值一样来使用引用生成。这个和[returning references from functions](http://php.net/manual/zh/functions.returning-values.php)\(从函数返回一个引用\)一样 , 通过在函数名前面加一个引用符号 . 

```php
function &gen_reference() {
    $value = 3;

    while ($value > 0) {
        yield $value;
    }
}

/* 
 * 我们可以在循环中修改$number的值，而生成器是使用的引用值来生成，所以gen_reference()内部的$value值也会跟着变化。
 */
foreach (gen_reference() as &$number) {
    echo (--$number).'... ';
}
```

**PHP7新增语法**

**生成器委派**

在PHP7中 , 生成器允许通过使用`yield from`关键字委派另一个生成器generator, 遍历对象Traversable object, 或数组来生成值 . 外部生成器将从内部生成器、对象或数组中 "生成" 所有值 , 然后在外部生成器中继续执行 , 直到不再有效 . 如果使用`yield from`的生成器, 则`yield from`表达式也将返回由内部生成器返回的任何值 . 例子很明了 : 

```php
function count_to_ten() {
    yield 1;
    yield 2;
    yield from [3, 4];
    yield from new ArrayIterator([5, 6]);
    yield from seven_eight();
    yield 9;
    yield 10;
}

function seven_eight() {
    yield 7;
    yield from eight();
}

function eight() {
    yield 8;
}

foreach (count_to_ten() as $num) {
    echo "$num ";
}
```

#### 生成器类



