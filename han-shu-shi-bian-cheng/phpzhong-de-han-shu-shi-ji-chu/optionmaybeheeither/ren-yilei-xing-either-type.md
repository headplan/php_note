# 任一类型Either Type

这里的Either类型是我们Maybe类型的泛化 . 而不是有值或没值 , 或者一左一右两个值 . 由于它也是一个联合类型 , 所以在任何时候只能设置这两个可能的值之一 .

当只有很少的错误来源或者错误本身并不重要时 , Maybe类型的效果更好 . 使用 "任一" 类型, 我们可以通过"左"值提供任何我们想要的错误信息 . "右" 值用于成功 , 因为很明显它的意思是right . 

下面是一个简单的"Either"类型的实现 . 由于代码本身是相当枯燥乏味的 , 所以这里只有基类 , 可以去 Packt 网站上看看那其两个子类 : 

```php
<?php
abstract class Either
{
    protected $value;
    public function __construct($value)
    {
        $this->value = $value;
    }

    public static function right($value): Right
    {
        return new Right($value);
    }

    public static function left($value): Left
    {
        return new Left($value);
    }
    
    abstract public function isRight(): bool;
    abstract public function isLeft(): bool;
    abstract public function getRight();
    abstract public function getLeft();
    abstract public function getOrElse($default);
    abstract public function orElse(Either $e): Either;
    abstract public function map(callable $f): Either;
    abstract public function flatMap(callable $f): Either;
    abstract public function filter(callable $f, $error): Either;
}
```

该实现提供与我们对于Maybe类相同的API , 假设正确的值是有效的 . 你应该可以使用Either类而不是Maybe类 , 而不用改变你的逻辑 .  唯一的区别是检查我们处于哪种情况的方法 , 并将方法更改为新的getRight或getLeft方法 . 

也可以给我们的新类型写一个提升函数 : 

```php
<?php
function liftEither(callable $f, $error = "An error occured")
{
    return function() use ($f)
    {
        if(array_reduce(func_get_args(), function(bool $status, Either $e) {
            return $e->isLeft() ? false : $status;
        }, true)) {
            $args = array_map(function(Either $e) {
                // it is safe to do so because the fold above checked
                // that all arguments are of type Some
                return $e->getRight(null);
            }, func_get_args());
            
            return Either::right(call_user_func_array($f, $args));
        }
        
        return Either::left($error);
    };
}
```



