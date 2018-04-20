# 命令行工具

PHP主要用于Web编程,不过也可以作为一个通用的脚本语言.如果要与Web应用共享代码,使用PHP建立可以在命令行执行的脚本就特别有用.例如一个论坛,可能希望每几分钟或者每几小时运行一个程序来扫描新帖子等操作,就可以使用PHP去编写这种扫描程序,节省时间,也避免维护开销.

PHP包含一个命令行接口版本.CLI中对一些配置指令设置了硬编码值.比如,max\_execution\_time指令设置为0,支持无限的程序运行时间等等.运行一个脚本直接传入脚本文件名即可

```
php scan-discussions.php
```

在UNIX上,还可以在脚本最前面使用hash-bang语法来自动运行PHP解释器,例如库放在/usr/local/bin中,脚本第一行可以写:

```
#!/usr/lcoal/bin/php
```

这样只需要在命令行上输入脚本名就可以运行脚本,当然文件还要有执行权限.

有一些类和函数可能同时用于Web和命令行,这是可以使用php\_sapi\_name\(\)判断是否返回cli,在脚本中建立不同的分支:

```
if ('cli' == php_aspi_name()) {
    print "命令行直接输出来了";
} else {
    print "不是命令行可以打进log里";
    error_log(mysql_error());
}
```

## 命令行选项

* `-d`参数 - 允许指定定制的INI记录,而不需要修改ini文件,例如打开输出缓冲:
  * `php -d output_buffering=1 scan-discussions.php`
* `-r`参数 - 直接运行PHP代码\(不需要&lt;?php和?&gt;\),例如直接输出当前时间:
  * `php -r 'print strftime("%c");'`
* `-h`参数 - 查看帮助,得到完整的CLI二进制选项列表
  * `php -h`

CLI二进制库将标准I/O流的句柄定义为常量STDIN,STDOUT,STDERR.可以直接使用这些常量,而无需使用fopen\(\)自行创建文件句柄:

```
// 读标注输入
$input = fgets(STDIN, 1024);
// 写至标准输出
fwrite(STDOUT, $jokebook);
// 写至标准错误输出
fwrite(STDERR, $error_code);
```



