# 函数

如何正确的创建和使用函数

* 声明函数和方法
* 标量类型提示
* 匿名函数
* 闭包
* 使用对象作为函数
* 高阶函数
* 可调用类型提示

#### 

#### 

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
    * 创建并返回一个[匿名函数](http://php.net/manual/zh/functions.anonymous.php)， 它与当前对象的函数体相同、绑定了同样变量，但可以绑定不同的对象，也可以绑定新的类作用域。

      “绑定的对象”决定了函数体中的_$this_的取值，“类作用域”代表一个类型、决定在这个匿名函数中能够调用哪些 私有 和 保护 的方法。 也就是说，此时 $this 可以调用的方法，与`newscope`类的成员函数是相同的。

      静态闭包不能有绑定的对象（`newthis`参数的值应该设为`NULL`）不过仍然可以用 bindTo 方法来改变它们的类作用域。

这里的介绍还有些模糊 , 下面这篇Laravel相关的文章可以帮助更好的理解 : [http://laravelacademy.org/post/4341.html](http://laravelacademy.org/post/4341.html)

> 现代 PHP 新特性系列（五） —— 闭包和匿名函数
>
> 闭包是指在创建时封装周围状态的函数，即使闭包所在的环境的不存在了，闭包中封装的状态依然存在。
>
> 匿名函数其实就是没有名称的函数，匿名函数可以赋值给变量，还能像其他任何PHP函数对象那样传递。不过匿名函数仍然是函数，因此可以调用，还可以传入参数，适合作为函数或方法的回调。
>
> > 注：理论上讲闭包和匿名函数是不同的概念，不过PHP将其视作相同的概念（匿名函数在PHP中也叫作闭包函数），所以下面提到闭包时指的也是匿名函数；反之亦然 . **前面我们也提到过 . **
>
> **创建闭包**
>
> 闭包和普通的PHP函数很像：常用的句法相同，也接受参数，而且能返回值。不过闭包没有函数名。
>
> > 注：我们之所以能调用$greet变量，是因为这个变量的值是一个闭包，而且闭包对象实现了\_\_invoke\(\)魔术方法，只要变量名后有\(\)，PHP就会查找并调用\_\_invoke方法。**之前我们也提到过 . **
>
> ### **从父作用域继承变量** {#ipt_kb_toc_4341_2}
>
> 在PHP中必须手动调用闭包对象的bindTo方法或使用use关键字把父作用域的变量及状态附加到PHP闭包中。而实际应用中，又以使用use关键字实现居多。
>
> 里面use关键字的用法不在冗述 , 重点看bindTo方法 :
>
> 通过该方法，我们可以把闭包的内部状态绑定到其他对象上。这里`bindTo`方法的第二个参数显得尤为重要，其作用是指定绑定闭包的那个对象所属的PHP类，这样，闭包就可以在其他地方访问邦定闭包的对象中受保护和私有的成员变量。
>
> PHP框架经常使用`bindTo`方法把路由URL映射到匿名回调函数上，框架会把匿名回调函数绑定到应用对象上，这样在匿名函数中就可以使用`$this`关键字引用重要的应用对象：
>
> ```php
> <?php
> class App
> {
>     protected $routes = [];
>     protected $responseStatus = '200 OK';
>     protected $responseContentType = 'text/html';
>     protected $responseBody = '测试路由';
>     
>     public function addRoute($routePath, $routeCallback)
>     {
>         $this->routes[$routePath] = $routeCallback->bindTo($this, __CLASS__);
>     }
>     
>     public function dispatch($currentPath)
>     {
>         foreach ($this->routes as $routePath => $callback) {
>             if( $routePath === $currentPath) {
>                 $callback();
>             }
>         }
>         header('HTTP/1.1 ' . $this->responseStatus);
>         header('Content-Type: ' . $this->responseContentType);
>         header('Content-Length: ' . mb_strlen($this->responseBody));
>         echo $this->responseBody;
>     }
> }
>
> $app = new App();
> $app->addRoute('user/nonfu', function(){
>     $this->responseContentType = 'application/json;charset=utf8';
>     $this->responseBody = '{"name":"测试路由"}';
> });
> $app->dispatch('user/nonfu');
> ```
>
> 这里我们需要重点关注`addRoute`方法，这个方法的参数分别是一个路由路径和一个路由回调，`dispatch`方法的参数是当前HTTP请求的路径，它会调用匹配的路由回调。第9行是重点所在，我们将路由回调绑定到了当前的App实例上。这么做能够在回调函数中处理App实例的状态 . **看看我们之前的一个简单个购物车类 , 更好理解 . **

#### 



