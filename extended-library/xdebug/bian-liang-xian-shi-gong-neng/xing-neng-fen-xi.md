# 性能分析

**xdebug.profiler\_enable**

类型:integer（整型）, 默认值: 0

启用Xdebug的效能分析器，效能文件被创建在 profile output directory目录. 这些文件可以被 KCacheGrind读取以呈现在你面前。此指令不能在脚本中通过ini\_set\(\)来设置，如果你想有选择的启用效能分析， 请设置xdebug.profiler\_enable\_trigger为1，不要使用此指令。

**xdebug.profiler\_enable\_trigger**

类型:integer（整型）, 默认值: 0

当此选项设置为1,通过使用XDEBUG\_PROFILE作为GET/POST参数,或者以XDEBUG\_PROFILE作为名称来设置一个cookie，你可以触发生成效能分析文件。 这样，效能数据将会被写入defined directory目录. 如果不想为每一个请求都生成效能分析文件，你需要将 xdebug.profiler\_enable设置为0. 可以通过 xdebug profiler\_enable\_trigger\_value 配置对触发器本身的访问 , 也就是设置值 . 

类似Trace配置 . 

**xdebug.profiler\_enable\_trigger\_value**

类型:string（字符串）, 默认值: "" , Xdebug &gt;= 2.3

此设置可用于限制谁可以使用 XDEBUG. profiler\_enable\_trigger 中概述的 XDEBUG\_PROFILE 功能。设置的值等于XDEBUG\_PROFILE触发的值才能生成性能分析文件 . 类似Trace配置 . 

**xdebug.profiler\_output\_dir**

类型:string（字符串）, 默认值: /tmp

效能分析器的输出信息将被写入的目录，确保执行PHP脚本的用户拥有对目录的写权限。这条指令不能通过 ini\_set\(\)函数设置。

类似Trace配置 . 

**xdebug.profiler\_output\_name**

类型:string（字符串）, 默认值: cachegrind.out.%p

这项设置决定被用来转存效能信息文件的名称。它通过格式操作符来鉴别名称格式，跟 sprintf\(\)和strftime\(\)函数类似。有若干格式操作符可以被用来格式化文件名称。类似Trace配置 . 

**xdebug.profiler\_append**

类型:integer（整型）, 默认值: 0

当此选项设置为1, 在一个新的请求被映射到相同的文件\(依赖于xdebug.profiler\_output\_name的设置\)时，效能分析文件的内容不会被覆盖。新的内容将会被追加到原文件的最后。

**xdebug.profiler\_aggregate**

类型:integer（整型）, 默认值: 0

将此设置设置为1时, 将为多个请求编写单个探查器文件。可以浏览多个页面或重新加载页面以获得所有请求的平均值。该文件将被命名为. cachegrind. 合计。您将需要移动此文件以获取另一轮聚合数据。

