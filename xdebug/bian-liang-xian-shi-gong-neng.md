# 相关配置

#### 错误显示

**xdebug.default\_enable**

类型: boolean（布尔型）, 默认值: 1

如果此设置为 1, 则默认情况下会在错误事件上显示堆栈轨迹跟踪。可以使用 xdebug\_disable \(\) 禁用显示堆栈轨迹跟踪的代码。由于这是 Xdebug 的基本功能之一, 因此最好将此设置设置为1。

**xdebug.dump.\***

类型: string（字符串）, 默认值: Empty

\* = COOKIE, FILES, GET, POST, REQUEST, SERVER, SESSION。这7个参数控制当错误情况发生时，显示哪些超全局变量。在php.ini设置中，要显示的超全局变量之间用逗号隔开或用\*表示所有，但是要确保没有添加空格。如果要在错误发生时显示REMOTE\_ADDR和REQUEST\_METHOD，添加如下设置:

```
xdebug.dump.SERVER = REMOTE_ADDR,REQUEST_METHOD
xdebug.dump.GET = *
```

**xdebug.dump\_globals**

类型: boolean（布尔型）, 默认值: 1

控制是否显示由xdebug.dump.\* 参数指定的超全局变量的值 , 就是前面设置的内容。

**xdebug.dump\_once**

类型: boolean（布尔型）, 默认值: 1

控制是对所有的错误情况都显示超全局变量的值\(设置为0关闭一次显示\) , 默认只对第一个发生的错误情况显示超全局变量的值。

> 这里的意思就是关闭了一次显示 , 发生两次以上错误时也显示前面配置的全局变量 .

**xdebug.dump\_undefined**

类型: boolean（布尔型）, 默认值: 0

如果你想显示超全局变量中未定义的值，你应该将此项设置为1，否则使用默认值。设置1开启后 , 前面定义显示的全局变量会显示等于undefined .

---

#### 打印显示

**xdebug.cli\_color**

类型: integer（整型）, 默认值: 0 , 在Xdebug &gt;= 2.2

如果这项被设置为1, 当使用CLI模式并且输出端是打字机时，Xdebug将会为变量信息和堆栈轨迹输出添加颜色. 在Windows系统下, ANSICON工具需要被安装.

如果这项被设置为2, Xdebug将始终为变量信息和堆栈轨迹输出添加颜色, 不管它是否连接到一台打字机也不管ANSICON是否被安装. 在这种情况下, 你可能会停止转义代码.

**xdebug.overload\_var\_dump**

类型: boolean（布尔型）, 默认值: 1, 在Xdebug 2.1 版中引进

默认情况下 , 当php.ini的html\_errors设置为1时，Xdebug将使用重载的var\_dump\(\)函数来显示变量信息。如果你不想这样，那么你可以把此项设置为0，但是在此之前你有必要先检查，确保没有关闭html\_errors。

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

---

#### 

---

xdebug.coverage\_enable

xdebug.extended\_info

xdebug.file\_link\_format

xdebug.force\_display\_errors

xdebug.force\_error\_reporting

xdebug.halt\_level

xdebug.idekey

xdebug.manual\_url

xdebug.max\_nesting\_level

xdebug.max\_stack\_frames

xdebug.scream

---

xdebug.profiler\_aggregate

xdebug.profiler\_append

xdebug.profiler\_enable

xdebug.profiler\_enable\_trigger

xdebug.profiler\_enable\_trigger\_value

xdebug.profiler\_output\_dir

xdebug.profiler\_output\_name

---

xdebug.remote\_addr\_header

