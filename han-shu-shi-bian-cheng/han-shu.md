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



