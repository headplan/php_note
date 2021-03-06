# 堆栈跟踪

当 Xdebug 被激活时, 它将显示一个堆栈跟踪, 每当 PHP 决定显示一个通知, 警告, 错误等。可以将堆栈跟踪显示的信息及其呈现方式进行配置, 以满足您的需要。

Xdebug 在错误情况下显示的堆栈跟踪 \(如果 display\_errors 在 php 中设置为 on\) 在它们显示的信息量中相当保守。这是因为大量的信息会减慢脚本的执行速度, 并在浏览器中呈现堆栈跟踪本身。但是, 可以使堆栈跟踪显示具有不同设置的更详细的信息。

#### 堆栈跟踪中的变量

默认情况下, Xdebug 现在将在它生成的堆栈跟踪中显示变量信息。在收集或显示时, 变量信息可以占用相当多的资源。但是, 在许多情况下, 显示变量信息很有用, 这就是为什么 Xdebug 有设置 Xdebug.collect\_params .

#### 相关配置参数

#### 运行轨迹

当 Xdebug 被激活时, 它将显示一个堆栈跟踪, 每当 PHP 决定显示一个通知, 警告, 错误等。可以将堆栈跟踪显示的信息及其呈现方式进行配置, 以满足您的需要。

Xdebug 在错误情况下显示的堆栈跟踪 \(如果 display\_errors 在 php 中设置为 on\) 在它们显示的信息量中相当保守。这是因为大量的信息会减慢脚本的执行速度, 并在浏览器中呈现堆栈跟踪本身。但是, 可以使堆栈跟踪显示具有不同设置的更详细的信息。

**xdebug.auto\_trace**

类型: boolean（布尔型）, 默认值: 0

当开启这项配置时，在脚本运行之前，对函数调用的追踪就会启用.这使得开发者可以在auto\_prepend\_file文件中追踪脚本.

**xdebug.trace\_enable\_trigger**

类型: boolean（布尔型）, 默认值: 0, 在Xdebug 2.2

当此设置设置为1时也就是开启状态 , 可以使用XDEBUG\_TRACE参数GET/POST或者cookie来触发跟踪文件的生成 , 也可以理解为手动生成跟踪文件 . 为了防止 Xdebug 为每个请求生成跟踪文件 , 需要将Xdebug的auto\_trace设置为0关闭 , 还可以通过配置 xdebug.trace\_enable\_trigger\_value对触发器本身的访问 , 即给GET/POST或者cookie设置个XDEBUG\_TRACE的值 .

**xdebug.trace\_enable\_trigger\_value**

类型:string , 默认值 : "" , 在Xdebug &gt;= 2.3支持

此设置可用于限制谁可以使用 XDEBUG. trace\_enable\_trigger 中概述的 XDEBUG\_TRACE 功能 , 设置的值等于XDEBUG\_TRACE触发的值才能生成跟踪文件 .

还有两个函数可以在代码中更好的控制生成跟踪的位置 :

```
xdebug_start_trace(); // 开始记录回溯
xdebug_stop_trace(); // 结束记录回溯
```

**xdebug.trace\_options**

类型:integer（整型）, 默认值:0

当设置为1时，后来的请求所产生的轨迹信息将会被附加至之前轨迹信息之后，而不会覆盖原信息 .

---

**xdebug.trace\_format**

类型:integer（整型）, 默认值:0

轨迹文件的格式 . 这个属性控制了3种书写格式 :

* \[0\] - 易于阅读的格式 - 这是默认的 . 同时展示内容还会受**xdebug.collect**和**xdebug.show**相关配置影响 . 
* \[1\] - 机器容易解析的格式
* \[2\] - HTML格式

计算机格式化的字段 :

[https://xdebug.org/docs/all\_settings\#trace\_format](https://xdebug.org/docs/all_settings#trace_format)

---

**xdebug.trace\_output\_dir**

类型:string, 默认值:/tmp

轨迹输出文件所在目录 , 确保当前执行PHP脚本的用户拥有对该目录的写入权利 .

**xdebug.trace\_output\_name**

类型:string, 默认值:trace.%c

轨迹文件的文件名。此设置使用特定的格式来命名轨迹文件，和sprintf\(\)与strftime\(\)十分类似。有若干格式可以用来命名轨迹文件，’.xt’后缀通常会自动添加。

可用的格式操作符有:

| 操作符 | 意义 | 示例格式 | 示例文件名 |
| :--- | :--- | :--- | :--- |
| %c | crc32 of the current working directory | trace.%c | trace.1258863198.xt |
| %p | pid | trace.%p | trace.5174.xt |
| %r | random number | trace.%r | trace.072db0.xt |
| %s | script name 2 | cachegrind.out.%s | cachegrind.out.\_home\_httpd\_html\_test\_xdebug\_test\_php |
| %t | timestamp \(seconds\) | trace.%t | trace.1179434742.xt |
| %u | timestamp \(microseconds\) | trace.%u | trace.1179434749\_642382.xt |
| %H | $\_SERVER\[‘HTTP\_HOST’\] | trace.%H | trace.kossu.xt |
| %R | $\_SERVER\[‘REQUEST\_URI’\] | trace.%R | trace.\_test\_xdebug\_test\_php\_var=1\_var2=2.xt |
| %U | $\_SERVER\[‘UNIQUE\_ID’\] 3 | trace.%U | trace.TRX4n38AAAEAAB9gBFkAAAAB.xt |
| %S | session\_id \(from $\_COOKIE if set\) | trace.%S | trace.c70c1ec2375af58f74b390bbdd2a679d.xt |
| %% | literal % | trace.%% | trace.%%.xt |

**\[收集\]**

**xdebug.collect\_assignments**

类型: boolean（布尔型）, 默认值: 0, 在Xdebug 2.1

此设置 \(默认为 0\) 控制 Xdebug 是否应将变量赋值添加到函数跟踪。

**xdebug.collect\_includes**

类型: boolean（布尔型）, 默认值: 1

此项，默认为1, 控制Xdebug是否应该将在include\(\), include\_once\(\), require\(\)或require\_once\(\)函数中使用的文件名写入追踪文件。

**xdebug.collect\_params**

类型: integer（整型）, 默认值: 0

此参数，默认为0，当在函数轨迹或堆栈轨迹中记录一个函数调用时，控制Xdebug是否应该搜集传递给函数的参数 . 也可以理解为跟踪级别 .

默认值为0，因为对于大脚本来说，它可能消耗大量的内存而使脚本无法运行。启用该选项是安全的，但是如果脚本中含有大量函数调用或者有大数据结构作 为参数的话，可能会遇到一些问题。 Xdebug 2改进了内存使用，此问题不再存在，因为它不再将这些信息存储在内存中。相反，Xdebug会将这些信息写入硬盘，这意味着你得留意硬盘使用量。

此参数用5个不同的可选值 , 每个不同的值都会对应显示不同的信息 .

| 值 | 显示的信息 |
| :--- | :--- |
| 0 | 无 |
| 1 | 变（常）量类型和长度（大小）\(如string\(6\), array\(8\)\)。 |
| 2 | 变（常）量类型和长度（大小）, 含有用以显示完整信息的帮助工具1。 |
| 3 | 完整变量内容\(包括通过xdebug.var\_display\_max\_children, xdebug.var\_display\_max\_data和xdebug.var\_display\_max\_depth参数设置的限制信息。 |
| 4 | 完整变量内容和名称。 |
| 5 | PHP 序列化的变量内容, 没有名称。\(新 Xdebug 2.3\) |

**xdebug.collect\_return**

类型:boolean（布尔型）, 默认值:0

此设置，默认值为0，控制Xdebug是否应该将函数调用的返回值写入轨迹文件。

> 对于计算机化的跟踪文件 \(xdebug. trace\_format = 1\), 这只适用于 xdebug 2.3 起。

**xdebug.collect\_vars**

类型: boolean（布尔型）, 默认值: 0

这个设置告诉Xdebug来搜集在一定范围内使用的变量的信息。这一过程很慢，因为Xdebug必须对PHP的opcode数组进行反向工程。这个设置不会记录不同变量的变量值，如果需要记录变量值，可以使用xdebug.collect\_params。只有当你希望使用xdebug\_get\_declared\_vars\(\)函数\(即返回申明的变量集合\)时，才需要启用此设置 .

**\[展示\]**

**xdebug.show\_error\_trace**

类型: integer（整型）, 默认值: 0

当此设置设置为1时, Xdebug 将在引发错误时显示堆栈跟踪-即使此错误实际上已被捕获。

**xdebug.show\_exception\_trace**

类型: integer（整型）, 默认值: 0

当此设置设置为1时, Xdebug 将在引发异常或错误时显示堆栈跟踪-即使此异常或错误已被实际捕获。

PHP 7 中引入了错误 "异常"。

**xdebug.show\_local\_vars**

类型: integer（整型）, 默认值: 0

当此项设置为不等于0的值时，Xdebug在错误发生时生成的堆栈信息仍然将在最顶层显示所有变量的信息。注意，使用这个选项可能会生成很多信息，所以此参数默认是关闭的。

**xdebug.show\_mem\_delta**

类型: integer（整型）, 默认值: 0

当此项设置为不等于0的值时，Xdebug生成的人类可读的轨迹文件将会显示函数调用间不同的内存使用情况。如果Xdebug被设置为生成机器可读的轨迹文件，那么将始终显示此信息。  




