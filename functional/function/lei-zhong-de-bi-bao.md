# 类中的闭包

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



