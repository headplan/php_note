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

#### 常用代码

在PHP的源码中经常会看到的一些很常见的宏 , 这些代码在PHP的源码中出现的频率极高 , 基本在每个模块都会有他们的身影 .

> 计算机语言如C或汇编语言有简单的宏系统 , 由编译器或汇编器的预处理器实现 . C的宏预处理器的工作只是简单的文本搜索和替换 , 使用附加的文本处理语言如M4 ,  可以获得更精巧的宏 .
>
> 宏 , 是C程序提供的预处理功能之一 . 包括带参数的宏定义和不带参数的宏定义 . 具体是指用一个指定的标志符来进行简单的字符串替换或者进行阐述替换 . 形式为 : \#define 标识符 字符串
>
> 具体查看C\_Note中宏定义的相关文章

**\#\#和\#**

宏是C/C++中非常强大 , 使用也很多的一个功能 . 有时用来实现类似函数内联的效果 , 或者将复杂的代码进行简单封装 , 提高可读性或可移植性等 . 

**双井号\(\#\#\)**

在C语言的宏中，"\#\#"被称为**连接符**\(concatenator\) , 它是一种预处理运算符 , 用来把两个语言符号\(Token\)组合成单个语言符号 . 这里的语言符号不一定是宏的变量 . 并且双井号不能作为第一个或最后一个元素存在 . 如下所示源码 : 

```c
#define PHP_FUNCTION            ZEND_FUNCTION
#define ZEND_FUNCTION(name)             ZEND_NAMED_FUNCTION(ZEND_FN(name))
#define ZEND_FN(name) zif_##name
#define ZEND_NAMED_FUNCTION(name)       void name(INTERNAL_FUNCTION_PARAMETERS)
#define INTERNAL_FUNCTION_PARAMETERS int ht, zval *return_value, zval **return_value_ptr, \
zval *this_ptr, int return_value_used TSRMLS_DC
 
PHP_FUNCTION(count);
 
//  预处理器处理以后， PHP_FUCNTION(count);就展开为如下代码
void zif_count(int ht, zval *return_value, zval **return_value_ptr,
        zval *this_ptr, int return_value_used TSRMLS_DC)
```

宏ZEND\_FN\(name\)中有一个"\#\#"，它的作用一如之前所说，是一个连接符，将zif和宏的变量name的值连接起来。 以这种连接的方式以基础，多次使用这种宏形式，可以将它当作一个代码生成器，这样可以在一定程度上减少代码密度， 我们也可以将它理解为一种代码重用的手段，间接地减少不小心所造成的错误。

**单引号\(\#\)**

"\#"是一种预处理运算符 , 它的功能是将其后面的宏参数进行**字符串化操作 , **简单说就是在对它所引用的宏变量通过替换后在其左右各加上一个双引号 , 用比较官方的话说就是将语言符号\(Token\)转化为字符串 . 例如 : 

```c
#define STR(x) #x
 
int main(int argc, char** argv)
{
    printf("%s\n", STR(It's a long string)); // 输出 It's a long string
    return 0;
}
```

如前文所说，It's a long string 是宏STR的参数，在展开后被包裹成一个字符串了。所以printf函数能直接输出这个字符串， 当然这个使用场景并不是很适合，因为这种用法并没有实际的意义，实际中在宏中可能会包裹其他的逻辑，比如对字符串进行封装等等。





