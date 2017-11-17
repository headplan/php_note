# 相关函数

#### 变量显示函数

**var\_dump\(\)** : 打印 , 其实是重载xdebug\_var\_dump\(\)

void **xdebug\_var\_dump**\( \[mixed var \[,...\]\] \) : 显示变量的详细信息

void **xdebug\_debug\_zval**\( \[string varname \[, ...\]\] \)  
用于打印一个或多个变量结构的相关信息。包括变量类型，值以及变量引用次数。如果传递的是一个数组，则会递归数组中的所有元素。

void **xdebug\_debug\_zval\_stdout**\(\[string varname \[, ...\]\] \)

返回一个变量的标准输出信息 , 包括类型,值,引用次数等

void **xdebug\_dump\_superglobals**\(\)  
该方法用于打印出所有通过xdebug.dump.\*配置属性在php.ini文件中指定的所有全局变量信息 , 如 , 在php.ini文件中增加如下配置信息

```
xdebug.dump.GET=*
xdebug.dump.SERVER=REMOTE_ADDR
```

> 在代码中通过调用xdebug\_debug\_zval\(\)方法打印出GET下的所有变量信息以及SERVER\['REMOTE\_ADDR'\]变量信息

array **xdebug\_get\_declared\_vars**\(\)  
返回一个数组，包含当前作用域下的所有变量名称，需要打开xdebug\_collect.vars配置。

array **xdebug\_get\_function\_stack**\(\)  
一次打印出出调用xdebug\_get\_function\_stack\(\)方法所在的方法的所有方法调用信息。

integer **xdebug\_get\_stack\_depth**\(\)  
返回当前栈的深度，main\(\)深度为0

