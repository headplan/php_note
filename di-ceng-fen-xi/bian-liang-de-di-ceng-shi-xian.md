# 变量的底层实现

> PHP底层的Zend虚拟机是用C语言来实现的 , C语言是强类型 , 而PHP是弱类型语言 , 那这是如何实现的呢 ?

查看PHP的源码包 : 

https://github.com/php/php-src

其中 , 最核心的代码在Zend目录 , 这是Zend虚拟机的实现 . 包括栈 , 数据类型 , 编译器等 .

main目录是PHP的一些内建函数 , 最主要函数都在这里放着 . 

最大的一个目录ext是PHP的扩展 . PHP的大部分功能 , 都是以extenstion形式来完成的 , 所以开发了一个扩展 , 可以放在ext目录下 . 

---

进入Zend目录 , 打开zend.h文件 , 寻找下面的代码 : 

```c
struct _zval_struct {
	/* Variable information */
	zvalue_value value;		/* value */
	zend_uint refcount__gc;
	zend_uchar type;	/* active type */
	zend_uchar is_ref__gc;
};
```



