# 函数

如何正确的创建和使用函数

* 声明函数和方法
* 标量类型提示
* 匿名函数
* 闭包
* 使用对象作为函数
* 高阶函数
* 可调用类型提示

#### 声明函数和方法

在PHP中通常使用`function`声明函数 :

```php
<?php

function my_function($parameter, $second_parameter)
{
    // [...]
}

?>
```

在类内声明的函数成为方法 . 它不同于传统函数 , 因为它可以访问对象属性 , 具有可见性修饰符 , 并且可以声明为静态 . 为了保持代码的纯净 , 我们会将属性设置为私有 :

```php
<?php

class SomeClass
{
   private $some_property;

   // a public function
   public function some_function()
   {
       // [...]
   }

   // a protected static function
   static protected function other_function()
   {
       // [...]
   }
}

?>
```

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

#### 匿名函数与闭包

之所以将这两个概念放在一起 , 因为容易混淆 , 我们来看看PHP文档中给出的解释 .

在PHP文档中 , 匿名函数也叫闭包函数 . 允许指定一个没有名称的函数 . 最常用的就是回调函数的参数值 . 一个匿名函数可以看做是闭包类的一个实例 . 将匿名函数放在普通函数中 , 也可以将匿名函数返回 , 这就构成了一个简单的闭包 .

我们先来看看匿名函数 :

```
<?php
$add = function(float $a, float $b): float {
    return $a + $b;
};
// 注意:花括号后面会有一个分号表示结束
```

上面这段代码段声明了一个匿名函数 , 并将其分配给一个变量 , 以便以后可以重用它 , 作为参数到另一个函数或直接调用 .

```
$add(5, 10);
$sum = array_reduce([1, 2, 3, 4, 5], $add, 0);
```

当然 , 也可以直接声明一个匿名函数作为参数

```
<?php
$uppercase = array_map(function(string $s): string {
    return strtoupper($s);
}, ['hello', 'world']);
```

当然 , 我们还可以返回一个函数 , 因为可以返回任何类型的值

```
<?php
function return_new_function()
{
    return function($a, $b, $c) { /* [...] */};
}
```

在Yii框架中 , 我们还遇到过这样的使用

```
'test' => function(){
    return 'test'
},
```

在配置文件的数组中 .

我们再来回顾一下官方的解释 :

> **匿名函数\(Anonymous functions\)**
>
> 也叫闭包函数\(closures\) , 允许临时创建一个没有指定名称的函数 . 最经常用作回调函数\(callback\)参数的值 . 当然 , 也有其它应用的情况 .

注意 : 在使用匿名函数的时候 , 匿名函数当做变量 , 须提前声明 , js中也是这样的  . 当然 , 使用function声明的函数式不会报错的 .

```
// 报错
echo $callback(); 
$callback=function(){ 
 return "aa"; 
};
```

关于匿名函数 , 鸟哥的一片文章 , 可以读读

> [http://www.laruence.com/2010/06/20/1602.html](http://www.laruence.com/2010/06/20/1602.html)

我们再来看看**闭包 .**

如果你知道JS的闭包 , 你可以在闭包中方便的使用外面的变量 , 但是PHP中是不可以的 . 在PHP中 , 你要使用一个关键字use将现有的变量导入到匿名函数中 :

```
<?php
$some_variable = 'value';
$my_closure = function() use($some_variable)
{
    // [...]
};
```

而且 , PHP闭包使用的是前期绑定的方法 . 这意味着更改变量之后 , 不会从闭包内部看到更改 .

```
<?php
$s = 'orange';
$my_closure = function() use($s) { echo $s; };
$my_closure(); // 输出 'orange'
$s = 'banana';
$my_closure(); // 还是输出 'orange'
```

记住这些都是前期的 , 后面都不会变的就可以了 , 或者可以理解为use关键字所引用的也只不过是变量的一个副本而已 . 但是有一个方式是可以改变的 , 那就是通过引用传递变量 . 以便对变量进行更改 .

```
$s = 'orange';
$my_closure = function() use(&$s) { echo $s; };
$my_closure(); // 输出 'orange'
$s = 'banana';
$my_closure(); // 输出 'banana'
```

但是我们这个专题是为了说明函数式编程 , 目的也是为了保持数据结构或状态的不变 , 保持函数的纯粹 . 这里只是简单的引入一个概念罢了 .

**消除歧义** : 当将对象传递给闭包时 , 在匿名函数外部对对象中的属性所做的任何修改都将在闭包内访问 . PHP在传递对象到闭包时不会复制对象 . 例子 :

```php
<?php
class a
{
    public $a='hello';
}

$a = new a;
$b = function() use($a) {
    return $a->a;
};
$a->a = 'world';
echo $b();
```

对于PHP中闭包的语法 , 前面已经基本理清了 , 我们再来看几个闭包实现的例子吧 . 首先 , 我们再清晰一下概念 :

> 什么是闭包?
>
> 闭包是可以包含自由（未绑定到特定对象）变量的代码块；这些变量不是在这个代码块内或者任何全局上下文中定义的，而是在定义代码块的环境中定义（局部变量）。“闭包” 一词来源于以下两者的结合：要执行的代码块（由于自由变量被包含在代码块中，这些自由变量以及它们引用的对象没有被释放）和为自由变量提供绑定的计算环境（作用域）。
>
> 在编程领域我们可以通俗的说：子函数可以使用父函数中的局部变量，这种行为就叫做闭包。

一些帮助消化的例子 :

```php
//例一
//在函数里定义一个匿名函数，并且调用它
function printStr() {
    $func = function( $str ) {
        echo $str;
    };
    $func( 'some string' );
}

printStr();

//例二
//在函数中把匿名函数返回，并且调用它
function getPrintStrFunc() {
    $func = function( $str ) {
        echo $str;
    };
    return $func;
}

$printStrFunc = getPrintStrFunc();
$printStrFunc( 'some string' );

//例三
//把匿名函数当做参数传递，并且调用它
function callFunc( $func ) {
    $func( 'some string' );
}

$printStrFunc = function( $str ) {
    echo $str;
};
callFunc( $printStrFunc );

//也可以直接将匿名函数进行传递。如果你了解js，这种写法可能会很熟悉
callFunc( function( $str ) {
    echo $str;
} );

//例四
function getMoney() {
    $rmb = 1;
    $dollar = 6;
    $func = function() use ( $rmb ) {
        echo $rmb;
        echo $dollar;
    };
    $func();
}

getMoney();

//输出：
//1
//报错，找不到dorllar变量

// 例五
function getMoney() {
    $rmb = 1;
    $func = function() use ( $rmb ) {
        echo $rmb;
        //把$rmb的值加1
        $rmb++;
    };
    $func();
    echo $rmb;
}

getMoney();

//输出：
//1
//1

//例六
function getMoney() {
    $rmb = 1;
    $func = function() use ( &$rmb ) {
        echo $rmb;
        //把$rmb的值加1
        $rmb++;
    };
    $func();
    echo $rmb;
}

getMoney();

//输出：
//1
//2
```

**总结一下**

前面的例子 , 主要都是在说使用use关键字 , 传递变量到闭包中 . 需要注意的是 , 这些变量都必须在函数或类的头部声明 , 并且PHP 7.1 起 , 不能传入[superglobals](http://php.net/manual/zh/language.variables.predefined.php) , $this或者和参数重名的变量 .

**类中的闭包**

一个基本的购物车类 , 闭包和作用于的应用案例 :

```php
<?php
// 一个基本的购物车,包括一些已经添加的商品和每种商品的数量
// 其中有一个方法用来计算购物车中所有商品的总价格
class Cart
{
    # 基本价格
    const PRICE_BUTTER = 1.00;
    const PRICE_MILK   = 3.00;
    const PRICE_EGGS   = 6.95;
    # 商品
    protected $products = [];

    /**
     * 添加商品
     * $product  商品
     * $quantity 个数
     */
    public function add($product, $quantity)
    {
        $this->products[$product] = $quantity;
    }

    # 获取商品个数
    public function getQuantity($product)
    {
        return isset($this->products[$product]) ? $this->products[$product] : FALSE;
    }

    # 获取总价格
    public function getTotal($tax)
    {
        $total = 0.00;
        $callback = function ($quantity, $product) use ($tax, &$total)
        {
            $pricePerItem = constant(__CLASS__ . "::PRICE_" .strtoupper($product));
            $total += ($pricePerItem * $quantity) * ($tax + 1.0);
        };

        array_walk($this->products, $callback);
        return round($total, 2);
    }
}

$my_cart = new Cart;
$my_cart->add('butter', 1);
$my_cart->add('milk', 3);
$my_cart->add('eggs', 6);
# 其中有5%的销售税
print $my_cart->getTotal(0.05) . "\n";
```

**自动绑定$this**

在类中声明的任何匿名函数 , 会自动绑定$this . 也就是说 :

```php
<?php
class ClosureInsideClass
{
    public function testing()
    {
        return function() {
            var_dump($this);
        };
    }
}
$object = new ClosureInsideClass();
$test = $object->testing();
$test();

# 意思是,可以直接这样用
<?php
class ClosureInsideClass
{
    public $a = '123';
    public function testing()
    {
        return function() {
            var_dump($this->a);
        };
    }
}
$object = new ClosureInsideClass();
$test = $object->testing();
$test();
```

以上是PHP5.4的新特性 , 自动绑定$this , 如果不希望使用当前类的自动绑定 , 则可以改用静态匿名函数 .

```php
<?php
class ClosureInsideClass
{
    public function testing()
    {
        return (static function() {
            // no access to $this here, the following line
            // will result in an error.
            var_dump($this);
        });
    }
}
$object = new ClosureInsideClass();
$test = $object->testing();
$test();

此时会输出
Notice: Undefined variable: this in %s on line %d
NULL
```

同理 , 声明静态的匿名函数 , 试图将对象绑定到静态匿名函数 , 也会报错 , 而且是警告错误

```php
<?php

$func = static function() {
    // function body
};
$func = $func->bindTo(new StdClass);
$func();

Warning: Cannot bind an instance to a static closure in %s on line %d
```

#### **\_\_invoke和闭包类**

**使用对象作为函数**

有时 , 可能想把你的helper函数分成几个小部分 , 并且私有化的隐藏起来 , 这是就可以利用任何对象的`__invoke`魔术方法 , 把一个实例当成一个你隐藏的函数来用 , 不过用这样隐藏的方式来解释`__invoke`魔术方式还是有些牵强 , 先来看个例子 :

```php
<?php
class ObjectAsFunction
{
    private function helper(int $a, int $b): int
    {
        return $a + $b;
    }

    public function __invoke(int $a, int $b): int
    {
        return $this->helper($a, $b);
    }
}
$instance = new ObjectAsFunction();
echo $instance(5, 10);
```

`__invoke`魔术方式可以使用传递给实例的任何参数调用 , 如果需要 , 可以用构造函数初始化你需要的参数和方法 , 给`__invoke`使用.

> 注意 : 我们之所以能调用变量 , 是因为这个变量的值是一个闭包 , 而且闭包对象实现了\_\_invoke\(\)魔术方法 , 只要变量名后有\(\) , PHP就会查找并调用\_\_invoke方法 .

**Closure类\(闭包类\)**

用于代表匿名函数的类 . 匿名函数\(在PHP5.3中被引入\)会产生这个类型的对象 . 在过去 , 这个类被认为是一个实现细节 , 但现在可以依赖它做一些事情 . 自PHP5.4起 , 这个类带有一些方法 , 允许在匿名函数创建后对其进行更多的控制 . 除了此处列出的方法，还有一个 \_\_invoke 方法 . 这是为了与其他实现了\_\_invoke\(\)魔术方法的对象保持一致性 , 但调用匿名函数的过程与它无关 .

* Closure::\_\_construct - 用于禁止实例化的构造函数 , 这个方法仅用于禁止实例化一个Closure类的对象
* Closure::bind - 复制一个闭包 , 绑定指定的$this对象和类作用域 , 这个方法是Closure::bindTo\(\)的静态版本 
  * `public static Closure Closure::bind ( Closure $closure , object $newthis [, mixed $newscope = 'static' ] )`
    * closure - 需要绑定的匿名函数
    * newthis - 需要绑定到匿名函数的对象 , 或者NULL创建未绑定的闭包
    * newscope - 想要绑定给闭包的类作用域 , 或者'static'表示不改变 . 如果传入一个对象 , 则使用这个对象的类型名 . 类作用域用来决定在闭包中$this对象的私有 , 保护方法的可见性
    * 返回一个新的Closure对象或者在失败时返回FALSE
* Closure::bindTo - 复制当前闭包对象 , 绑定指定的$this对象和类作用域
  * `public Closure Closure::bindTo ( object $newthis [, mixed $newscope = 'static' ] )`
    * newthis - 绑定给匿名函数的一个对象 , 或者NULL来取消绑定
    * newscope - 关联到匿名函数的类作用域 , 或者 'static' 保持当前状态 . 如果是一个对象 , 则使用这个对象的类型为心得类作用域 , 这会决定绑定的对象的保护,私有成员方法的可见性
    * 返回一个新创建的Closure对象或者在失败时返回FALSE

这里的介绍还有些模糊 , 下面这篇Laravel相关的文章可以帮助更好的理解 : http://laravelacademy.org/post/4341.html



