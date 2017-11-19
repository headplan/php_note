# 扩展配置

**xdebug.idekey**

类型:string（字符串）, 默认值: \*complex\*

控制哪些 IDE 密钥 Xdebug 应传递给 DBGp 调试器处理程序。控制哪些 IDE 密钥 Xdebug 应传递给 DBGp 调试器处理程序。默认值基于环境设置。首先环境设置 DBGP\_IDEKEY 被查询, 然后用户和作为最后用户名。默认设置为找到的第一个环境变量。如果没有找到, 则设置默认为 ""。如果设置了此设置, 它总是覆盖环境变量。

**xdebug.halt\_level**

类型: integer（整型）, 默认值: 0 , Xdebug &gt;= 2.3

此设置允许您配置一个掩码 , 以确定是否将NOTICE或WARNING转换为错误 . 可以配置PHP生成自己的NOTICE或WARNING , 或者用trigger\_error\(\)函数生成 , 例如 , 将不带参数的strlen\(\)的警告转换为错误 :

```
ini_set('xdebug.halt_level', E_WARNING);
strlen();
echo "Hi!\n";
```

转换为错误后 , 显示不变 ,但脚本会停止执行 , 里面前面的例子中Hi将不会输出 .

该设置是一个位掩码 , 因此要将所有的通知和警告转换为所有应用程序的错误的话 , 应该设置 :

```
xdebug.halt_level=E_WARNING|E_NOTICE|E_USER_WARNING|E_USER_NOTICE
```

> 位掩码只支持上面提到的四级 .

**xdebug.scream**

类型: boolean（布尔型）, 默认值: 0, 在Xdebug 2.1版中引进

如果此项设置为1，Xdebug将禁用@操作符，那么php的错误提示信息将无法被隐藏。

**xdebug.force\_display\_errors**

类型: boolean（布尔型）, 默认值: 1 , Xdebug &gt;= 2.3

如果此设置设置为 1, 则无论 PHP display\_errors 的设置如何, 都将始终显示错误。意思就是是否开启强制显示错误信息 .

**xdebug.force\_error\_reporting**

类型: boolean（布尔型）, 默认值: 1 , Xdebug &gt;= 2.3

是否强制显示所有错误级别的信息。与error\_reporting逻辑上为OR .

**xdebug.max\_nesting\_level**

类型: integer（整型）, 默认值: 256

用来控制对无限递归的保护机制。 此项的值限制了在脚本中断之前，运行嵌套执行的函数的最大数目（即最大递归次数，超过这个数，php脚本将中断执行）.

在 Xdebug 2.3 之前, 默认值为100 .

**xdebug.max\_stack\_frames**

类型: integer（整型）, 默认值: -1 , Xdebug &gt;= 2.3

控制堆栈跟踪中显示的堆栈帧的数量, 在 PHP 错误堆栈跟踪期间的命令行中以及在浏览器中查看 HTML 跟踪。默认是-1不限制数据 .

**xdebug.manual\_url**

类型: string（字符串）, 默认值: http://www.php.net , Xdebug &lt; 2.2.1

xdebug信息中有关函数和错误信息的超链接将直接链接到PHP帮助手册中的函数页面。最好将此项设置为最近的镜像。

