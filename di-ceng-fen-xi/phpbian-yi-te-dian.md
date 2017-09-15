# PHP编译特点

#### PHP代码执行图解

词法分析器-&gt;语法分析器-&gt;编译器\(编译成opcode\)-&gt;**执行引擎\(执行opcode\)**

![](/assets/php_diceng_1.png)

**编译型语言**

对于C语言 , C++ , 编译成机器码\(二进制\)来运行 .

java语言 , 把.java编译成.class , 称为bytecode , 由jvm\(虚拟机\)来运行 . 

**解释语言**

解释器解释执行 . 典型的如linux shell .

解释器逐行来执行命令 . 

---

PHP稍有特殊之处 , 虽然是一个脚本语言 , 但不是靠解释器解释 . 

而是由Zend虚拟机来执行 , 屏蔽了操作系统的区别 . 

php代码编译成opcode , 由Zend虚拟机来执行opcode . 

但是 , PHP脚本一结束 , opcode就清除了 , 这也是和java的区别 , 因为java会编译打包成.class文件 , 可以把bytecode打包发布 . 

对于opcode的缓存 , php本身不支持 , 但是apc , xcache等加速器 , 实现了这样的效果 . 



