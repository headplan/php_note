# Applicative functors

我们来看一下我们的 identity functor 的一个实例 , 拿一些整数和一个add函数的curryied版本 : 

```php
<?php
$add = curry(function(int $a, int $b) { return $a + $b; });
$id = new IdentityFunctor(5);
```

现在 , 当我们试图将 $add 参数映射到我们的 Functor 上时会发生什么 ? 请考虑以下代码 : 

```php
<?php
$hum = $id->map($add);
echo get_class($hum->get());
// Closure
```



