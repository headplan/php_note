# 变量显示功能

Xdebug 替换 PHP 的 var\_dump \(\) 函数来显示变量 . Xdebug 的版本包含不同类型的不同颜色 , 并对数组元素/对象属性、最大深度和字符串长度的大小进行限制 . 还有其他一些处理变量显示的函数 .

* xdebug.var\_display\_max\_children=-1
* xdebug.var\_display\_max\_data=-1
* xdebug.var\_display\_max\_depth=-1

#### 配置var\_dump\(\)的显示

**xdebug.var\_display\_max\_children**  
整数类型，默认值128。用于控制通过_xdebug\_var\_dump\(\),var\_dump\(\)方法时_显示数组中子数组的个数或对象中属性的个数，_设定为-1关闭该限制。_

_**xdebug.var\_display\_max\_data**  
整数类型，默认值521。用于控制xdebug\_var\_dump\(\),var\_dump\(\)方法时_显示输出的字符串的长度，设定为-1关闭该限制。

_**xdebug.var\_display\_max\_depth**  
整数类型，默认值3。用于控制通过xdebug\_var\_dump\(\),var\_dump\(\)方法时打印数组或对象时显示的层数，即深度。可设定的最大值为1023，也可以将其设定为-1以达到设定最大值的效果。_

这些设置不影响通过远程调试功能发送到客户端的子级的数量 .

