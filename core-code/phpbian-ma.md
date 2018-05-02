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

* 使用PHP协程yield , 与迭代有关的都可以用协程优化 . 
* 用"\[\]"定义数组 . 



PHP代码优化

PHP重点新特性

PHP编码规范

PHP坑人小题

