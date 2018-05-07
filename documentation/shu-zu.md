# 数组

#### 数组的定义

```
$items = array();
$items = [];
$items = ['abc']; # 直接初始化
# 使用对象定义数组,前提是对象实现了PHP自带的ArrayAccess接口,查看预定义接口部分.
$obj = new Obj;
$obj[] = 'a';
$obj[] = 'b';
$obj[] = 'c';
print_r($obj);
```

#### 数组的key和value的限制条件

意思是什么类型的值可以当做数组的key , 数组的value值又可以存什么类型 .

* key 可以是 integer 或者 string
* value 可以是任意类型

key 会有如下的强制转换 :

* 包含有**合法整型值**的字符串会被转换为整型
* 浮点数和布尔值也会被转换为整型
* **键名 null 实际会被储存为 ""**
* 数组和对象不能被用为键名
* 相同键名 , 之前会被覆盖

```php
<?php

$arr1 = [
    1 => 'a',
    '1' => 'b',
    1.5 => 'c',
    true => 'd',
];

var_dump($arr1);

$arr2 = [
    'foo' => 'bar',
    'bar' => 'foo',
    100 => -100,
    -100 => 100,
];

var_dump($arr2);

array(1) {
  [1]=>
  string(1) "d"
}
array(4) {
  ["foo"]=>
  string(3) "bar"
  ["bar"]=>
  string(3) "foo"
  [100]=>
  int(-100)
  [-100]=>
  int(100)
}
```

#### 数组的访问

获取数组元素的值有哪些方法 ?

* 方括号 , 数组单元可以通过 array\[key\] 语法来访问
* 花括号也可以 , 例如 $array\[42\] 和 $array{42} 效果相同
* 方括号可以包括“表达式” , 例如 : $arr\[somefunc\($bar\)\];
* 自 PHP 5.4 起可以用数组间接引用函数或方法调用的结果

看一下数组的间接引用 :

```php
<?php

function getArr()
{
    return [1,2,3];
}

echo getArr()[1];
```

#### 数组元素的删除

unset\(\) 函数允许删除数组中的某个键 .

```
<?php

$arr = [1,2,3,4,5,6];

foreach ($arr as $key => $value) {
    unset($arr[$key]);
}

$arr[] = 6;
print_r($arr);

Array
(
    [6] => 6
)
```

这里的索引是6 , 数组unset后 , 不会重建索引 .

#### php数组类型与其他类型的转换

在PHP中 , 存在8种变量类型 , 分为三类 : 

* 标量类型 :  boolean、integer、float\(double\)、string
* 复合类型 : **array**、object
* 特殊类型 : resource、NULL

两个问题 : 

如何将一个int、float、string、boolean 类型转换为数组?

var\_dump\(\(array\)false\)和var\_dump\(\(array\)null\)的输出结果是?

* **int，float，string，boolean 和 resource 类型 \(array\)$scalarValue 等同 array\($scalarValue\)**
* **object 转换为 array , 结果为一个数组 , 其单元为该对象的属性 , 键名将为成员变量名 . **
* **将 NULL 转换为 array 会得到一个空的数组 . **

```php
<?php

var_export(array(false));
var_export((array)false);
var_export(array(null));
var_export((array)null);
// 结果
array (
  0 => false,
)array (
  0 => false,
)array (
  0 => NULL,
)array (
)
```

```php
<?php

class User
{
	public $name = 'jony';
	public $age = 22;
	protected $phone = '138xxxxxxx';
	private $sex = 'man';
}
$obj = (array) new User;
var_export($obj);
```



