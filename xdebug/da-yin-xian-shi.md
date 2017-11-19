# 打印显示

**xdebug.cli\_color**

类型: integer（整型）, 默认值: 0 , 在Xdebug &gt;= 2.2

如果这项被设置为1, 当使用CLI模式并且输出端是打字机时，Xdebug将会为变量信息和堆栈轨迹输出添加颜色. 在Windows系统下, ANSICON工具需要被安装.

如果这项被设置为2, Xdebug将始终为变量信息和堆栈轨迹输出添加颜色, 不管它是否连接到一台打字机也不管ANSICON是否被安装. 在这种情况下, 你可能会停止转义代码.

**xdebug.overload\_var\_dump**

类型: boolean（布尔型）, 默认值: 1, 在Xdebug 2.1 版中引进

默认情况下 , 当php.ini的html\_errors设置为1时，Xdebug将使用重载的var\_dump\(\)函数来显示变量信息。如果你不想这样，那么你可以把此项设置为0，但是在此之前你有必要先检查，确保没有关闭html\_errors。

也可以使用2作为此设置的值 . 除了格式化var\_dump\(\)输出很好 , 它还将添加文件名和行号到输出 . 

> 使得"xdebug file\_link\_format"的设置也受到重视 . \(在Xdebug 2.3之后的版本中\)

Xdebug 替换 PHP 的 var\_dump \(\) 函数来显示变量 . Xdebug 的版本包含不同类型的不同颜色 , 并对数组元素/对象属性、最大深度和字符串长度的大小进行限制 . 还有其他一些处理变量显示的函数 .

* xdebug.var\_display\_max\_children=-1
* xdebug.var\_display\_max\_data=-1
* xdebug.var\_display\_max\_depth=-1

**xdebug.var\_display\_max\_children**  
整数类型，默认值128。用于控制通过xdebug\_var\_dump\(\), xdebug.show\_local\_vars或函数轨迹时显示数组中子数组的个数或对象中属性的个数，设定为-1关闭该限制。

**xdebug.var\_display\_max\_data**  
整数类型，默认值521。用于控制xdebug\_var\_dump\(\), xdebug.show\_local\_vars或函数轨迹时显示输出的字符串的长度，设定为-1关闭该限制。

**xdebug.var\_display\_max\_depth**  
整数类型，默认值3。用于控制通过xdebug\_var\_dump\(\), xdebug.show\_local\_vars或函数轨迹时打印数组或对象时显示的层数，即深度。可设定的最大值为1023，也可以将其设定为-1以达到设定最大值的效果。

上面三个设置不影响通过远程调试功能发送到客户端的子级的数量 .

