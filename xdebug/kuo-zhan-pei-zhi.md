# 扩展配置

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



