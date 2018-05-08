# 位运算符

#### 常用总结

* 判断int型变量a是奇数还是偶数 : a&1 = 0 偶数  a&1 = 1 奇数
* 乘法运算转化成位运算 : a \* \(2^n\) 等价于 a &lt;&lt; n
* 除法运算转化成位运算 : a / \(2^n\) 等价于 a &gt;&gt; n
* 不用中间变量交换两个整数x^= y; y^= x; x^= y;
* 二进制位掩码 , 提供了一种用一个选项表示多项的可能 . 

```php
<?php

$a = 2;
$b = 3;

echo $a&1, PHP_EOL;
echo $b&1, PHP_EOL;

echo $a << 3, PHP_EOL;
echo $b << 3, PHP_EOL;

$a ^= $b;
$b ^= $a;
$a ^= $b;

echo $a, PHP_EOL;
echo $b, PHP_EOL;

// 常量对应了数字掩码,比如
// 1	E_ERROR (integer)
// 2	E_WARNING (integer)
// 4	E_PARSE (integer)
// 这些值都是2的n次方
// 这些数字转换为二进制后,只有一位是1,其余都是0
// 1      1
// 2     10
// 4    100
// 8   1000
// 16 10000
// php判断二进制掩码1的位置,知道对应的错误级别,比如101,就是E_ERROR和E_PARSE
error_reporting(E_ERROR | E_WARNING | E_PARSE | E_NOTICE); // 打开这4种错误提示
 error_reporting(E_ALL ^ E_NOTICE); // 排除E_NOTICE错误提示

// 结果
0 偶数
1 奇数
16 2*2^3
24 3*2^3
3 交换两个数
2
```



