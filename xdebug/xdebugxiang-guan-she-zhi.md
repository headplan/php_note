# 相关函数

#### 变量显示函数

**var\_dump\(\)** : 打印 , 其实是重载xdebug\_var\_dump\(\)

void **xdebug\_var\_dump**\( \[mixed var \[,...\]\] \) : 显示变量的详细信息

void **xdebug\_debug\_zval**\( \[string varname \[, ...\]\] \)  
用于打印一个或多个变量结构的相关信息。包括变量类型，值以及变量引用次数。如果传递的是一个数组，则会递归数组中的所有元素。

> 此函数显示有关一个或多个包含其类型、值和 refcount 信息的变量的结构化信息。数组用值递归地进行探索。此函数的实现方式与 PHP 的 debug\_zval\_dump \(\) 函数不同, 以解决该函数所具有的问题, 因为变量本身实际上是传递给函数的。Xdebug 的版本更好, 因为它使用变量名来查找内部符号表中的变量, 并直接访问所有属性, 而不必处理将变量实际传递给函数的问题。结果是, 这个函数返回的信息比 PHP 自己的函数更准确地显示 zval 信息。

```php
<?php
    $a = array(1, 2, 3);
    $b =& $a;
    $c =& $a[2];

    xdebug_debug_zval('a');
    xdebug_debug_zval("a[2]");
?>
```

```
Returns:

a: (refcount=2, is_ref=1)=array (
    0 => (refcount=1, is_ref=0)=1, 
    1 => (refcount=1, is_ref=0)=2, 
    2 => (refcount=2, is_ref=1)=3)
a[2]: (refcount=2, is_ref=1)=3
```

> 每个php变量存在一个叫"zval"的变量容器中。一个zval变量容器，除了包含变量的类型和值，还包括两个字节的额外信息。第一个是"is\_ref"，是个bool值，用来标识这个变量是否是属于引用集合\(reference set\)。通过这个字节，php引擎才能把普通变量和引用变量区分开来，由于php允许用户通过使用&来使用自定义引用，zval变量容器中还有一个内部引用计数机制，来优化内存使用。第二个额外字节是"refcount"，用以表示指向这个zval变量容器的变量\(也称符号即symbol\)个数。所有的符号存在一个符号表中，其中每个符号都有作用域\(scope\)，那些主脚本\(比如：通过浏览器请求的的脚本\)和每个函数或者方法也都有作用域。
>
> [http://www.php.net/manual/zh/features.gc.refcounting-basics.php](http://www.php.net/manual/zh/features.gc.refcounting-basics.php)

void **xdebug\_debug\_zval\_stdout**\(\[string varname \[, ...\]\] \)

返回一个变量的标准输出信息 , 包括类型,值,引用次数等 . 

> 与 xdebug\_debug\_zval \(\) 的区别在于, 信息不是通过 web 服务器 API 层显示的 , 而是直接显示在标准输出上 \(这样, 当您在单一进程模式下使用 Apache 运行它时, 它就会在控制台上完成\)。

void **xdebug\_dump\_superglobals**\(\)  
该方法用于打印出所有通过xdebug.dump.\*配置属性在php.ini文件中指定的所有全局变量信息 , 如 , 在php.ini文件中增加如下配置信息

```
xdebug.dump.GET=*
xdebug.dump.SERVER=REMOTE_ADDR
```

> 在代码中通过调用xdebug\_debug\_zval\(\)方法打印出GET下的所有变量信息以及SERVER\['REMOTE\_ADDR'\]变量信息

array **xdebug\_get\_declared\_vars**\(\)  
返回一个数组，包含当前作用域下的所有变量名称，需要打开xdebug\_collect.vars配置。

array **xdebug\_get\_function\_stack**\(\)  
一次打印出出调用xdebug\_get\_function\_stack\(\)方法所在的方法的所有方法调用信息。

integer **xdebug\_get\_stack\_depth**\(\)  
返回当前栈的深度，main\(\)深度为0

