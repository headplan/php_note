# 堆栈跟踪

当 Xdebug 被激活时, 它将显示一个堆栈跟踪, 每当 PHP 决定显示一个通知, 警告, 错误等。可以将堆栈跟踪显示的信息及其呈现方式进行配置, 以满足您的需要。

Xdebug 在错误情况下显示的堆栈跟踪 \(如果 display\_errors 在 php 中设置为 on\) 在它们显示的信息量中相当保守。这是因为大量的信息会减慢脚本的执行速度, 并在浏览器中呈现堆栈跟踪本身。但是, 可以使堆栈跟踪显示具有不同设置的更详细的信息。

#### 堆栈跟踪中的变量

默认情况下, Xdebug 现在将在它生成的堆栈跟踪中显示变量信息。在收集或显示时, 变量信息可以占用相当多的资源。但是, 在许多情况下, 显示变量信息很有用, 这就是为什么 Xdebug 有设置 Xdebug.collect\_params .

#### 相关配置参数

**xdebug.collect\_vars**

类型: boolean（布尔型）, 默认值: 0

这个设置告诉Xdebug来搜集在一定范围内使用的变量的信息。这一过程很慢，因为Xdebug必须对PHP的opcode数组进行反向工程。这个设置不会记录不同变量的变量值，如果需要记录变量值，可以使用xdebug.collect\_params。只有当你希望使用xdebug\_get\_declared\_vars\(\)参数时，xdebug.collect\_params才需要被启用。

**xdebug.dump\_globals**

类型: boolean（布尔型）, 默认值: 1

控制是否显示由xdebug.dump.\* 参数指定的超全局变量的值。

**xdebug.dump.\***  
类型: string（字符串）, 默认值: Empty

> \* = COOKIE, FILES, GET, POST, REQUEST, SERVER, SESSION。这7个参数控制当错误情况发生时，显示哪些超全局变量。在php.ini设置中，要显示的超全局变量之间用逗号隔开，但是要确保没有添加 空格。

如果要在错误发生时显示REMOTE\_ADDR和REQUEST\_METHOD，添加如下设置:

```
xdebug.dump.SERVER = REMOTE_ADDR,REQUEST_METHOD
```

**xdebug.show\_local\_vars**

类型: integer（整型）, 默认值: 0

当此项设置为不等于0的值时，Xdebug在错误发生时生成的堆栈信息仍然将在最顶层显示所有变量的信息。注意，使用这个选项可能会生成很多信息，所以此参数默认是关闭的。

