# 错误显示

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

