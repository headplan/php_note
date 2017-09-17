# 匿名函数与闭包

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



