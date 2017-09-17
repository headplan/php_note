# 源码结构

#### PHP源码目录结构

**根目录: /**

* 这个目录包含的东西比较多，主要包含一些说明文件以及设计方案。 其实项目中的这些README文件是非常值得阅读的例如：
  * /README.PHP4-TO-PHP5-THIN-CHANGES 这个文件就详细列举了PHP4和PHP5的一些差异。
  * 还有有一个比较重要的文件/CODING\_STANDARDS，如果要想写PHP扩展的话，这个文件一定要阅读一下， 不管你个人的代码风格是什么样，怎么样使用缩进和花括号，既然来到了这样一个团体里就应该去适应这样的规范，这样在阅读代码或者别人阅读你的 代码是都会更轻松。
  * 根目录中所有大写的文件 , 都可以读一读 . 
* **build**
  顾名思义，这里主要放置一些和源码编译相关的一些文件，比如开始构建之前的buildconf脚本等文件，还有一些检查环境的脚本等。里面包括wk，awk和sh脚本用于编译处理，其中m4文件是linux下编译程序自动生成的文件，可以使用buildconf命令操作具体的配置文件
* **ext**
  官方扩展目录，包括了绝大多数PHP的函数的定义和实现，如array系列，pdo系列，spl系列等函数的实现，都在这个目录中。个人写的扩展在测试时也可以放到这个目录，方便测试和调试。例如Mysql，gd，zlib，xml，iconv 等我们熟悉的扩展库，ext\_skel是linux下扩展生成脚本，windows下使用ext\_skel\_win32.php脚本生成，每个扩展目录下包括php\_扩展名.c文件和phpt批处理测试脚本 . 
* **main**
  这里存放的就是PHP最为核心的文件了，主要实现PHP的基本设施，这里和Zend引擎不一样，Zend引擎主要实现语言最核心的语言运行环境。包括php.h,main.c,logos.h数组等等，是php程序的主要部分，定义了程序的SAPI接口全局变量等等。
* **netware**
  网络目录，以前的版本没有此目录，里面就两个文件sendmail\_nw.h和start.c，分别定义SOCK通信说需要的头文件和具体实现。
* **pear**
  “PHP 扩展与应用仓库”，包含PEAR的核心文件。install-pear.txt文件中详细说明了怎么样安装具体的扩展包
* **sapi**
  包含了各种服务器抽象层的代码，例如apache的mod\_php，cgi，fastcgi以及fpm等等接口。
* **scripts**
  Linux下的脚本目录
* **tests**
  PHP的测试脚本集合，包含PHP各项功能的测试文件 . 主要是phpt脚本，由--TEST--，--POST--，--FILE--和--EXPECT--三个部分组成。有些需要初始化的可以加--INI--部分 . 
* **travis**
  Travis CI的文件夹
* **TSRM**
  PHP的线程安全是构建在TSRM库之上的，PHP实现中常见的\*G宏通常是对TSRM的封装，TSRM\(Thread Safe Resource Manager\)线程安全资源管理器。保证在单线程和多线程模型下的线程安全和代码一致性。
* **win32**
  这个目录主要包括Windows平台相关的一些实现，比如sokcet的实现在Windows下和\*Nix平台就不太一样，同时也包括了Windows下编译PHP相关的脚本。
* **Zend**
  Zend引擎的实现目录，比如脚本的词法语法解析，opcode的执行以及扩展机制的实现 , 还包括PHP的生命周期，内存管理，变量定义和赋值以及函数宏定义等等。

#### PHP的测试

关于PHP的测试 , 查看另一篇翻译改编的内容 , 关于PHP5或PHP7的内部功能的测试

#### PHP源码阅读工具

**使用VIM + Ctags**

Vim不用说了 , 这里推荐在Linux下编写代码的读者或多或少的试一试ctags , ctags支持非常多的语言 , 可以将源代码中的各种符号\(如:函数、宏类等信息\)抽取出来做上标记并保存到一个文件中 , 供其他文本编辑工具\(VIM , EMACS等\)进行检索 . 它保存的文件格式符合[UNIX的哲学-小即是美](http://zh.wikipedia.org/zh/Unix哲学) , 使用也比较简洁 :

```
#在PHP源码目录(假定为/server/php-src)执行:
$ cd /server/php-src
$ ctags -R

#小技巧：在当前目录生成的tags文件中使用的是相对路径，
#若改用 ctags -R /server/ ，可以生成包含完整路径的ctags，就可以随意放到任意文件夹中了。 

#在~/.vimrc中添加:
set tags+=/server/php-src/tags
#或者在vim中运行命令:
:set tags+=/server/php-src/tags
```

**使用IDE**

这里可以使用CLion

