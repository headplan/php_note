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
* 一个\_\_invoke方法对象
* 用于匿名函数或闭包的变量

看一些例子 :

```php
# 这是第一种
$callback = 'strtoupper';
echo $callback('Hello World !');

# 和类相关的,可以像下面这样操作
# 先写一个类
class A
{
    public function hi($name)
    {
    return "Hi $name !\n";
    }

    static function hello($name) 
    {
    return "Hello $name !\n";
    }

    public function __invoke($name)
    {
    return $this->hi($name) . self::hello($name);
    }
}

# 这是我们前面提到的第二种的静态方法的方式
$callback = ['A', 'hello'];
$callback('World');
# 也可以像下面这样写,不过只针对PHP7以上的版本有效
$callback = 'A::hello';
$callback('World');
# 前面说的是静态方法,调用对象上的方法,也就是类实例上的方法也很简单,new一下即可
$a = new A();
$callback = [$a, 'hi']; # 注意这种形式是不能用的'$a->hi'
echo $callback('小明');
# 还有第三种,就是如果类中有__invoke()方法时,可以直接调用对象
$callback = $a;
$callback('World');
# 还有第四种,就是我们前面介绍了很多的闭包
$callback = function (string $s)
{
    return "Hello $s !\n";
};
echo $callback('World');

# 最后PHP还提供了两个函数用在回调函数上
# call_user_func_array()
# call_user_func()
# 这里的用法和上面是类似的,不再冗述,举一个例子
$foo = new foo;
# 第一个参数和前面的一样类的实例和其中的方法,第二个是这个方法的参数
call_user_func_array(array($foo, "bar"), array("three", "four"));
```

最后需要注意的是 , 如果使用了callback强制类型 , 就是这种 :

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

**接下来 , 任何函数名字符串\(包括自己声明的函数\) , 就都是可执行的了 , 这一点需要注意一下 . 如果你引入了你不太熟悉的库 , 你写的一些字符串可能和里面的名字重名了 , 但你认为这只是字符串 . **

