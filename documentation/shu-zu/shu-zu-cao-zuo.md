# 数组操作

#### 数组转换为字符串\(序列化、持久化\)

**为什么要序列化 ? **

* Api接口通信
* 数据缓存
* 数组数据持久化\(例:保存到数据库中\)

**序列化的方法 ? **

* 方法一 : 函数 serialize\(\) 可以实现
* 方法二 : 函数 json\_encode \(\)可以实现
* 方法三 : 函数 var\_export\($items, true\);
* 方法四 : xml、其他自定义文件格式…
* 方法五 : mcpack、protobuffer等二进制序列化方式

```php
<?php

$arr = [1,2,3,4];

echo serialize($arr), PHP_EOL;
echo json_encode($arr), PHP_EOL;
echo var_export($arr, true), PHP_EOL;
```

#### 如何合并两个数组 ?

通常我们会用到+运算符或者array\_merge函数 .

问 : 数组合并中 +运算符 与 array\_merge 的区别是 ?

**+运算符 对比 array\_merge**

**array\_merge**

* 一个数组中的值附加在前一个数组的后面
* 字符串键名 , 则该键名后面的值将覆盖前一个值
* 数字键名 , 后面的值将不会覆盖原来的值 , 而是附加到后面 . 因为一下面一条
* **数字键名将会被重新编号 . **
* 也可以是一个参数 , 并且该数组是数字索引的 , 则键名会以连续方式重新索引

```php
<?php

$arr = [1,2];

$merge = array_merge($arr, [0=>1]);

print_r($merge);

$arr = ['a'=>1,'b'=>2];

$merge = array_merge($arr, ['b'=>0]);

print_r($merge);

// 结果
Array
(
    [0] => 1
    [1] => 2
    [2] => 1
)
Array
(
    [a] => 1
    [b] => 0
)
```

**+运算符**

* 把右边的数组元素附加到左边的数组后面 , 
* 两个数组中都有的键名\(索引和数字\) , 则只用左边数组中的 , 右边的被忽略 . 

```php
<?php

$arr = [1,2,'a'=>'a','b'=>'b'];

$arr += [0=>2,1=>1,'a'=>'b','b'=>'a',6,7,8];

print_r($arr);

// 结果
Array
(
    [0] => 1
    [1] => 2
    [a] => a
    [b] => b
    [2] => 6
    [3] => 7
    [4] => 8
)
```

**总结**

* array\_merge : 覆盖 , 相同索引键覆盖 , 相同数字键追加 . 
* +运算符 : 补充 , 相同数字键索引建都忽略 . 

#### 数组运算符

![](/assets/shuzuyunsuanfu.png)

#### 数组元素查找

* 判断key是否存在 : array\_key\_exists\(\) 和 isset\(\)
* 判断值是否存在 : in\_array\(\) 和 array\_search\(\)
* 依据键返回值 :  $items\[$key\];
* 依据值返回键 : array\_keys \($items,$value\);
* 依据给定值进行模糊查找 : preg\_grep\(\);

```

```

#### 数组元素过滤和移除

#### 数组相关其他函数

#### 数组函数基本分类



