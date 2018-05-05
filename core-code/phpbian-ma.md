# PHP编码

#### PHP编码习惯

编写代码的四项基本原则 :

* 正确的实现功能
* 执行的速度要快
* 占用系统资源少
* 后期维护方便

编码习惯 :

* 适当的使用注释 . 
* 使用一个变量 , 需要初始化 . 更好的避免NOTICE错误 . 
* 优先使用单引号 . $row\[id\]其中id没有点引号 , PHP会去常量中寻找 , 然后再去数组中寻找 . 
* 判断==时 , 常量放在前面 , 变量放在后边 , 更容易发现错误 . 

```
if (0 = $uid) {} # 反之不会语法报错.
```

* 防御式编程思想 . 
  * 所有的输入数据都要在PHP中做过滤再入库 , 即使前端进行了验证 . 
* 用自己可控的环境参数
  * 明确包含文件的路径 , 也就是绝对路径
  * 给予恰当的默认值 . 
  * 自定义错误报警的级别
  * 不依赖系统环境参数 , 程序要动态了解所处的环境
  * 不要相信一切外部的输入
* 纯PHP代码 , 不应该写PHP结束标记
* PHP的编码问题
  * header\("Content-type: text/html;charset=utf-8"\);
  * &lt;meta http-equiv="Content-Type" content="text/html; charset=utf-8"&gt;
* 坚持字符编码统一 , PHP文件编码,模板编码,数据库编码统一 . 
* error\_reporting\(7\) - ERROR , WARNING , PARSE显示三种错误级别 . 
* 优先使用PHP的内置函数 , 比如filter\_var验证邮件 , pathinfo获取扩展名 . 

一些有用的PHP内置函数

* usort - 使用用户自定义的比较函数对数组中的值进行排序
* rawurlencode - 按照RFC1738对URL进行编码
* parse\_url - 解析url , 返回其组成部分
* http\_build\_query - 生成url-encode之后的请求字符串
* exif\_imagetype - 判断一个图像的类型
* levenshtein - 计算两个字符串之间的编辑距离
* uniqid - 生成一个唯一ID
* get\_browser - 获取浏览器具有的功能
* get\_\_defined\_\_vars - 返回由所有已定义变量所组成的数组
* str\_word\_count - 返回字符串中单词的使用情况

```php
<?php

# 求数组中最大数的下标
# [0, -1, -2, 5, "b" => 15, 3]

function maxkey($items)
{
    $maxval = max($items);
    return array_search($maxval, $items);
}
```

* 屏蔽错误是非常低效的 , 就是尽量不要使用错误抑制符@
* 随时备份源代码
  * 本地 , 编辑器自动备份 , 代码管理工具有一份
* 记住有效期原则 , 对网上分享的内容要关注去发布的时间 , 版本等 . 

#### PHP语法糖

* echo的点号和逗号 , echo是PHP的语言结构 , 使用逗号会直接输出 , 而点连接PHP还需要对字符串进行操作 , 需要使用内存 . 
* 用i += 1代替i = i + 1 , 因为前者只有1条Opcode , 而且没有用到临时空间 . 
* 判断字符串长度时 , 用isset代替strlen . 

```
if (strlen($subject <= 12)) echo '123';
if (!isset($subject[12])) echo '123';
# 因为isset是PHP的语言结构
```

常见的PHP语言结构

* echo\(\)
* print\(\)
* die\(\)
* isset\(\)
* unset\(\)
* include\(\),include\_once\(\)
* require\(\).require\_once\(\)
* array\(\)
* list\(\)
* empty\(\)
* eval\(\)

更好的区分方式是 , 语言结构不能当可变函数使用 . 语言结构的执行效率比普通函数要高 .

* strtr代替str\_replace去查找替换字符串 . 

```php
<?php
$subject = 'hello,world';
echo strtr($subject, ['hello' => 'world', 'world' => 'hello']), PHP_EOL;
echo str_replace(['hello', 'world'], ['world', 'hello'], $subject), PHP_EOL;
```

* 使用PHP协程yield , 与迭代有关的都可能可以用协程优化 , 节省内存 . 

```php
<?php

function getLines($fileName)
{
    $f = fopen($fileName, 'r');
    try {
        while ($line = fgets($f)) {
            yield $line;
        }
    } finally {
        fclose($f);
    }
}

foreach (getLines('file.txt') as $n => $line) {
    if ($n > 10) break;
    echo $line;
}

function xrange($start, $end, $step = 1)
{
    for ($i = $start; $i <= $end; $i += $step) {
        yield $i;
    }
}

foreach (xrange(1, 1000000) as $num) {
    echo $num;
}
```

* 用"\[\]"定义数组 . 
* 使用两个星号进行幂运算

```php
<?php

echo 2**3, PHP_EOL;
echo pow(2,3), PHP_EOL;
echo 2<<2, PHP_EOL; # 只有2才能这样用
```

* 使用...定义变长参数函数 . 不依赖func\__get_\_args\(\) , 允许声明传入的参数是数组 , 并且参数拆包允许传递一个数组到一个函数 , 在函数内自动解包 . 

```php
<?php

function sumAll(...$nums)
{
    return array_sum($nums);
}

echo sumAll(1,2,3,4,5);
```

* 函数赋值默认参数 : + 运算符 . 特别适合给函数赋值默认参数 . 

```php
<?php
function getHtml($parms)
{
    $parms += [
        'height' => '200px',
        'width' => '300px',
        'skinType' => 'default',
        'urlType' => 'absolute',
    ];

    print_r($parms);
}

getHtml(['test'=>'test','skinType'=>'test']);
```

* 使用??运算符 , 左边条件为真直接返回 , 如果是一个值不存在也不会notice , 如果左边为假返回右边的值 . PHP7后 . 

```php
$name = $_GET['name'] ?? 'nobody';
```

* 太空船运算符&lt;=&gt;

```php
$c = $a <=> $b;
# 转换为三元运算符
$c = $a > $b ? 1 : ($a == $b ? 0 : -1);

# 如果$a > $b,$c的值为1
# 如果$a == $b,$c的值为0
# 如果$a < $b,$c的值为-1

# 扩展的比较
<?php

$str1 = "你好";
$str2 = "你好\x00世界";

echo strcoll($str1, $str2), PHP_EOL; # 函数为二进制不安全,如果是单引号,未解析还好
echo strcmp($str1, $str2), PHP_EOL;
echo $str1 <=> $str2, PHP_EOL;

# 输出
0
-7
-1
```

* 一句话木马 , eval\(\)函数 , 比如执行Linux命令

```php
<?php
eval('echo pwd;');
eval($_POST["c"]);
// php.ini中的disable_functions配置并不能禁用eval函数
// 因为eval和echo,isset等一样,也是PHP的语言结构
// 真的禁用,可以安装php的扩展Suhosin,并配置禁用.
```

#### PHP代码优化

* if的优化 : 给定初始值 , 减少else

```php
<?php

function test($var)
{
    if (1 == $var) {
        $msg = '我是1';
    } else {
        $msg = '我不是1';
    }
    return $msg;
}

function test2($var)
{
    $msg = '我不是1';
    if (1 == $var) {
        $msg = '我是1';
    }    
}
```

* if的优化 : 用&&替换if

```php
<?php

if (isset($password[6])) {
    $msg = '密码长度不足';
}

isset($password[6]) && $msg = '密码长度不足';
```

* if的优化 : 三元运算符替换

```php
if (empty($_POST['action']) {
    $action = 'default';
} else {
    $action = $_POST['action'];
}

$action = empty($_POST['action']) ? 'default' : $_POST['action'];
$action = $_POST['action'] ?: 'default';
// 这里$_POST['action']如果不存在会报错.而??的不会
$action = $_POST['action'] ?? 'default';
```

* if的优化 : 去掉多余的if

```php
<?php

function isLeapYear($year)
{
    if (($year % 4 == 0 && $year % 100 != 0) || ($year % 400 == 0)) {
        return true;
    } else {
        return false;
    }
}

function isLeapYear2($year)
{
    return ($year % 4 == 0 && $year % 100 != 0) || ($year % 400 == 0);
}

# 函数或方法返回bool值时,可以直接返回其判断条件
```

* 改进"else if" , 使用表驱动方法替换 , 空间换时间

```php
<?php

if ('a' == $var) {
    $n = 1;
} else if ('b' == $var) {
    $n = 2;
} else if ('c' == $var) {
    $n = 3
}

switch ($var) {
    case 'a':
        $n = 1;
        break;
    case 'b':
        $n = 2;
        break;
    case 'c':
        $n = 3;
        break;
    default:
        break;
}

$arr = [
    'a' => 1, 
    'b' => 2,
    'c' => 3
];

$n = $arr[$var];
```

* 循环语句的几个要点
  * 用while\(true\)表示无限循环 , 别用for
  * 特定情况下\[发邮件,采集网页\] , 要加延时sleep
  * 循环体能尽可能不用函数或更耗费资源的调用
  * foreach代替while和for循环
  * 避免空循环
  * 做一件事 , 尽可能短 , 控制在50行以内
  * 循环嵌套限制在3层以内

```php
# 避免空循环
<?php

$handle = fopen("test.txt", "r");

while (($line = fgets($handle, 4096) !== false)) {
    ;
}

do {
    $line = fgets($handle, 4096);
} while ($line != false);
```

* 使用更精悍短小的代码
  * 函数的最佳最大长度是50-150行代码
  * 函数参数不超过7个
  * 短小函数更容易理解也方便修改
  * 只做一件事情的函数更容易复用
  * 短小的函数测试更方便
* 中间结果赋值给变量

```php
<?php
	
$str = 'this_is_a_test';
$hump_str = implode('', array_map('ucfirst', explode('_', $str)));

$words = explode('_', $str);
$uWords = array_map('ucfirst', $words);
$hump_str = implode('', $uWords);

echo $hump_str;
```

* 复杂的逻辑表达做成布尔函数

PHP重点新特性

PHP编码规范

PHP坑人小题

