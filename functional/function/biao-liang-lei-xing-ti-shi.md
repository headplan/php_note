# 标量类型提示

#### PHP7标量类型提示

在PHP5中 , 我们已经可以使用类型提示声明classes , callables , arrays . PHP 5.0第一次引入了type hints特性 , 允许我们指定函数的参数是一个特定class或interface类型 . 之后 , 在PHP 5.1中 , 我们可以指定参数是一个array type , 在PHP 5.4中 , 我们可以指定参数是一个“可被调用\(callable\)”的类型\(例如:function或Closure\) .

在PHP7中引入了标量类型提示的概念 . 意思是你现在可以声明string , int , float , bool , 他们都可以用于参数和返回类型 . 同时还引入了两个个概念 :

* Coercive Type - 非严格模式

  * 这是PHP7针对scalar type hints采取的默认方式 , 即尽可能尝试把参数转换成scalar type hints要求的类型 . 当然 , 方便总是有代价的 , 因为类型转换有时会导致精度丢失

    > 在这里，要特别说一下bool类型，它和我们在C++中的处理逻辑类似，一切表达“空值”的概念，例如：0, 0.0, null, "0", ""（空字符串）, \[\]（空数组），$uninitializedVar（未经初始化的变量），都会被认为是false，除去这些情况之外的，则会被认为是true。
    >
    > 但是 , 在非严格模式中 , bool只允许使用4个标量之间的转换 .

    ```php
    <?php
    function add(float $a, int $b): float {
        return $a + $b;
    }
    echo add(3.5, 1);
    // 4.5
    echo add(3, 1);
    // 4
    echo add("3.5", 1);
    // 4.5
    echo add(3.5, 1.2); // 1.2 gets casted to 1
    // 4.5
    echo add("1 week", 1); // "1 week" gets casted to 1.0
    // PHP Notice: A non well formed numeric value encountered
    // 2
    echo add("some string", 1);
    // Uncaught TypeError Argument 1 passed to add() must be of the type float, string given

    function test_bool(bool $a): string {
        return $a ? 'true' : 'false';
    }
    echo test_bool(true);
    // true
    echo test_bool(false);
    // false
    echo test_bool("");
    // false
    echo test_bool("some string");
    // true
    echo test_bool(0);
    // false
    echo test_bool(1);
    // true
    echo test_bool([]);
    // Uncaught TypeError: Argument 1 passed to test_bool() must be of the type Boolean
    ```

* Strict Type - 严格模式

  * 如果你不希望PHP7执行上面的类型转换 , 要求类型严格匹配 , 你可以手动启用PHP7的“严格模式” . 在这个模式下 , 任何不严格匹配的类型都会导致抛出\TypeError异常 . 启用strict type很简单 , 在PHP代码中的第一行 , 写上declare\(strict\_types=1\);
    > “PHP起始标记和declare\(strict\_types=1\);之间 , 不能有任何内容 , namespace必须紧跟在declare语句后面”
  * strict\_types指令只影响指定使用的文件 , 不会影响被它包含\(通过include等方式\)进来的其他文件 . 该指令在运行时编译 , 不能修改 . 它的运作方式 , 是在opcode中设置一个标志位 , 让函数调用和返回类型检查符合类型约束 .

    > 在严格模式中 , 前面的转换都不成立了 , 只有一个地方是允许的 , add\(3, 1\) , 因为.0不会带来数据偏差或丢失的风向 , 反之则不行 , 如果要求int类型 , 输入的是float类型 , 即使是.0结尾也会报错 .

    ```php
    <?php
    declare(strict_types=1);
    function add(float $a, int $b): float {
        return $a + $b;
    }
    echo add(3.5, 1);
    // 4.5
    echo add(3, 1);
    // 4
    echo add("3.5", 1);
    // Uncaught TypeError: Argument 1 passed to add() must be of the
    type float, string given
    echo add(3.5, 1.2); // 1.2 gets casted to 1
    // Uncaught TypeError: Argument 2 passed to add() must be of the type integer, float given
    echo add("1 week", 1); // "1 week" gets casted to 1.0
    // Uncaught TypeError: Argument 1 passed to add() must be of the type float, string given
    echo add("some string", 1);
    // Uncaught TypeError: Argument 1 passed to add() must be of the type float, string given
    function test_bool(bool $a): string {
        return $a ? 'true' : 'false';
    }
    echo test_bool(true);
    // true
    echo test_bool(false);
    // false
    echo test_bool("");
    // Uncaught TypeError: Argument 1 passed to test_bool() must be of the type boolean, string given
    echo test_bool(0);
    // Uncaught TypeError: Argument 1 passed to test_bool() must be of the type boolean, integer given
    echo test_bool([]);
    // Uncaught TypeError: Argument 1 passed to test_bool() must be of the type boolean, array given
    ```

##### Return Type Hints

在PHP7 , 我们除了可以type hint函数参数之外 , 还可以type hint函数的返回值 , 像下面这样 :

```php
<?php

function divisible(int $dividend, int $divider): int {
    return $dividend / $divider;
}

divisible(6, 3);
divisible(6, 4);
```

Return type hints对类型的处理 , 和参数使用的规则是相同的 . 默认采用coersive type , 当开启strict type之后 , 不满足约定的类型将会导致\TypeError异常 .

