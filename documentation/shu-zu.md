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



