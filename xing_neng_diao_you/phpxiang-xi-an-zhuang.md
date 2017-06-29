# PHP详细安装

**参考网站**

[http://www.phpinternalsbook.com/](http://www.phpinternalsbook.com/)

#### 源码安装

**获取源码**

[http://php.net/downloads.php](http://php.net/downloads.php)

[http://git.php.net/](http://git.php.net/)

[https://github.com/php/php-src](https://github.com/php/php-src)

源码下载的方式有上面的两种 , 第一种是源码包下载 , 第二个是直接从git检出 . 两者稍有区别 :

git库检出的源码没有configure脚本 , 所以需要使用buildconf脚本,利用 autoconf 来生成配置脚本configure .

git库检出的源码不包含预生成的解析器 , 所以还需要安装bison\(GNU bison是属于GNU项目的一个语法分析器生成器\) .

**src/目录**

在家目录中创建src/目录 , 用来存放刚才下载的源码 , 进入这个目录 :

```
mkdir ~/src
cd ~/src
```

**下载源码**

```
wget -O php.tar.gz http://hk1.php.net/get/php-7.1.6.tar.gz/from/this/mirror
tar -xzvf php.tar.gz
cd php-*
```

**Git检出**

```
~/src> git clone http://git.php.net/repository/php-src.git
~/src> git clone https://github.com/php/php-src.git
~/src> cd php-src
# 默认是在master分支上,这是当前的开发版本.你也可以直接检出一个稳定版.
~/php-src> git checkout PHP-7.0
```

**配置PHP**

构建工具下载

```
# Ubuntu
apt-get install build-essenial
# CentOS
yum groupinstall "Development Tools"
```

如果是Git仓库的版本 , 你需要以下依赖 :

* gcc - 或其他编译器套件\(build-essenial包含\)
* libc-dev - 它提供C标准库 , 包括头文件\(build-essenial包含\)
* make - 构建管理工具\(build-essenial包含\)
* autoconf - 用来生成配置脚本的 , 版本2.59或更高
* automake - 用来生成Makefile.in文件 , 版本1.4或更高
* libtool - 用来帮助管理共享库\(shared libraries\)
* bison - 用来生成PHP解析器的 , 版本2.4或更高
* re2c\(可选\) - 用来生成PHP分析器的 . 默认git库里已经包括了一个PHP的语法分析器 . 如果想用re2c也是可以的 . 

```
# Ubuntu
apt-get install build-essenial autoconf automake libtool bison re2c
# CentOS
yum groupinstall "Development Tools"
yum install re2c -y
```

执行`./configure --help`可以查看php的配置选项 , 可以直接执行`./configure`查看执行失败所缺少的软件依赖 , 直到执行成功 .

在配置阶段启用的扩展 , 就是./configure时配置需要安装的扩展 , 会需要依赖很多附加库 . 如果有-dev或-devel版本 , 尽量安装这些版本 . 因为开发者版通常不会包含必要的头文件 . 例如默认构建php时 , 需要libxml库 , 这里就可以安装 libxml2-dev 包 .

> 有的系统自带的`autoconf`程序版本会有Bug , 可能导致扩展的配置无法更新 , 如果在执行`./buildconf`时报错 , 可以根据出错信息安装合适版本的autoconf工具 .

**构建初始化**

```
~/php-src> ./buildconf
~/php-src> ./configure
~/php-src> make -jN
```

> 查看CPU核心数 , 然后替换N . `grep "cpu cores" /proc/cpuinfo`
>
> 之后可以make install , 把PHP安装到/usr/local目录 , 可以在配置时指定路径 .
>
> ./configure --prefix=$HOME/myphp
>
> 也可以编译一个最精简的PHP , 使用`./configure --disable-all`来进行配置 .

在默认情况下,PHP将生成CLI和CGI SAPIs二进制文件 , 分别位于`sapi/cli/php`和`sapi/cgi/php-cgi`分别 .

可以尝试运行`sapi/cli/php -v`检查生成情况 .

**./buildconf脚本**

如果是git仓库检出 , 首先要执行这个脚本 . 这个脚本调用了build/build.mk和build/build2.mk文件 . 这些文件的主要作用是调用`autoconf`生成./configure配置文件 , 调用`autoheader`生成`main/php_config.h.in`模板 , 这个模板用来配置生成最终的头文件`main/php_config.h`.

> Both utilities produce their results from theconfigure.infile \(which specifies most of the PHP build process\), theacinclude.m4file \(which specifies a large number of PHP-specific M4 macros\) and theconfig.m4files of individual extensions and SAPIs \(as well as a bunch of otherm4files\).
>
> The good news is that writing extensions or even doing core modifications will not require much interaction with the build system. You will have to write smallconfig.m4files later on, but those usually just use two or three of the high-level macros thatacinclude.m4provides. As such we will not go into further detail here.

./buildconf脚本只有两个选项 :

--debug - 调用autoconf和autoheader时禁用警告静默 . 就是会显示警告 .

--force - 这个选项允许./buildconf脚本运行在发布包上 . 例如下载的是打包好的源码 , 希望生成一个新的./configure配置文件 ,  并清楚配置缓存config.cache和autom4te.cache/ . 如果使用git pull更新了仓库 , 并在make的时候有奇怪的报错 , 通常是因为配置文件中发生了某些更改 , 就可以使用`./buildconf --force` .

**./configure脚本**

一旦生成了'./configure'脚本 , 就可以利用它来定制PHP了 . 可以使用帮助列表列出所有支持的选项 :

```
~/php-src>./configure --help | less
```

帮助的第一部分将列出各种通用选项 , 完全支持autoconf-based的配置 , 就是通用配置 . 例如前面我们已经提到的`--prefix=DIR` , 更改并指定安装目录 . 另一个可能有用的选项是-C , 它将缓存各种测试的结果到config.cache文件中 , 并加快后续的./configure调用 . 当然这个此选项仅在已经构建完成并且希望在不同配置之间快速更改时才有意义 .

除了通用的autoconf选项之外 , 当然还有许多PHP特定的配置 . 例如 , 可以使用 `--enable-NAME` 和 `--disable-NAME` 开关来选择想要编译的扩展和SAPIs . 如果这个扩展或sapi具有外部依赖项 , 则需要使用 `--with-NAME` 和 `--without-NAME` 来配置 . 如果NAME所需的库不是位于默认的安装路径\(例如你自己编译过它\) , 则可以使用`--with-NAME=DIR`指定其位置 .

默认情况下 , php将生成cli和cgi SAPIs , 以及一些扩展 . 我们可以使用 -m 选项查找 php 二进制文件所包含的扩展名 .

```
~/php-src> sapi/cli/php -m
```

现在举个例子 , 如果现在想要停止编译CGI SAPI , Tokenizer和sqlite3的扩展 , 开启编译opcache扩展和gmp扩展 , 配置命令应该这样写

```
~/php-src> ./configure --disable-cgi --disable-tokenizer --without-sqlite3 \
                       --enable-opcache --with-gmp
```

默认情况下 , 大多数扩展将静态编译 , 即它们是生成的二进制文件的一部分 . 默认情况下 , 只有opcache扩展是共享的 , 也就是说 , 它将在modules/directory中生成一个 opcache.so文件 . 还可以通过编写配置--enable-NAME=shared或--with-NAME=shared来将其他扩展编译为共享对象\(但不是所有扩展都支持此功能\) . 



---

Linux环境下 , 如果通过源代码编译安装程序的简单过程可以描述为

```
./configure-->make-->make install
```

其中./configure配置脚本功能就是对你的系统做很多的测试 , 以用来检测出你的安装平台的目标特征 , 比如它会检测你是不是有CC或GCC , 它是个shell脚本 , 是autoconf的工具的基本应用 , 它会产生一个输出文件"./Makefiles" , 接下来make程序通过该文件来实现编译

configure脚本有大量的命令行选项 , 对不同的软件包来说 , 这些选项可能会有变化 , 但是许多基本的选项是不会改变的 . configure脚本位于待安装程序源码根目录下面 , 会有一个configure可执行文件 , 使用`./configure --help`命令就可以看到可用的所有选项 , 尽管许多选项是很少用到的 , 但是当你为了特殊的需求而configure一个包时 , 知道他们的存在是很有益处的 .

**configure脚本选项的配置内容基本上分成9块内容**

1. 配置区【Configuration】
2. 程序安装目录区【Installation directories】
3. 程序名称区【Program names】
4. 系统类型区【System types】
5. 可选特性区【Optional Features】
6. 可选安装包区【Optional Packages】
7. 影响安装的环境变量区【Some influential environment variables】

下面是对PHP最新版7.1.6的拆解 .

---

**配置区【Configuration】**

这块内容主要是对./configure脚本本身运行的过程进行配置 , 如是否显示运行结果给用户 , 是否创建cache文件等等

    Configuration:
      -h, --help              display this help and exit
          --help=short        display options specific to this package
          --help=recursive    display the short help of all the included packages
      -V, --version           display version information and exit
      -q, --quiet, --silent   do not print `checking ...' messages
          --cache-file=FILE   cache test results in FILE [disabled]
      -C, --config-cache      alias for `--cache-file=config.cache'
      -n, --no-create         do not create output files
          --srcdir=DIR        find the sources in DIR [configure dir or `..']

    配置帮助参数:  
      -h, --help              显示帮助信息并退出
          --help=short        显示具体指令的帮助信息
          --help=recursive    显示所有包含此信息的帮助信息
      -V, --version           显示版本信息
      -q, --quiet, --silent   将不打印“CHECKing....”这样的信息
          --cache-file=FILE   测试返回的缓存文件 [disabled]
      -C, --config-cache      `--cache-file=config.cache'的另一种用法
      -n, --no-create         将不创建输出文件
          --srcdir=DIR        查询目标目录的源文件 [configure dir or `..']

**--cache-file=FILE**

'configure'会在你的系统上测试存在的特性\(或者bug!\) , 为了加速随后进行的配置 , 测试的结果会存储在一个cache file里 , 尤其当configure一个复杂的源码树时 , 一个很好的cache file的存在会对性能有很大帮助 .

**--no-create**

'configure'中的一个主要函数会制作输出文件\(./Makefile\) , 此选项阻止'configure'生成这个文件 , 你可以认为这是一种演习\(dry run\) , 尽管缓存\(cache\)仍然被改写了 .

---

**程序安装目录区【Installation directories】**

```
  --prefix=PREFIX         install architecture-independent files in PREFIX
                          [/usr/local]
  --exec-prefix=EPREFIX   install architecture-dependent files in EPREFIX
                          [PREFIX]
```

即如果你想指定安装的程序的具体目录 , 就用这两个选项 , 他们之间的区别就只有是否平台相关性 . 通过指定这两个选项后 , 程序就完全的被安装在指定的目录下面了 , 此时以后删除该程序 , 只需要简单的移除该目录下所有内容就可以了 .

```
  --prefix=PREFIX         安装至指定[PREFIX]的目录
                          默认[/usr/local]
  --exec-prefix=EPREFIX   安装至指定[EPREFIX]的目录
                          默认与[PREFIX]相同
```

这个区块是./configure脚本中经常被配置的选项区 , 这里的主要作用就是配置你要安装的软件的安装后的目录 , 默认情况下\`make install'将把文件安装至'/usr/local/bin' , '/usr/local/lib'等目录 . 但你可以通过以上参数定义\[PREFIX\]的值来改变目录 .

例如默认情况下目录为'/usr/local'使用指令'--prefix'改变目录 .

例子 : '--prefix=$HOME'

为了更好的定义项目 , 使用以下参数改变更多选项.

以下参数可直接定义更细致的系统目录\(以下参数尽量默认\) :

```
Fine tuning of the installation directories:
  --bindir=DIR            user executables [EPREFIX/bin]
  --sbindir=DIR           system admin executables [EPREFIX/sbin]
  --libexecdir=DIR        program executables [EPREFIX/libexec]
  --sysconfdir=DIR        read-only single-machine data [PREFIX/etc]
  --sharedstatedir=DIR    modifiable architecture-independent data [PREFIX/com]
  --localstatedir=DIR     modifiable single-machine data [PREFIX/var]
  --runstatedir=DIR       modifiable per-process data [LOCALSTATEDIR/run]
  --libdir=DIR            object code libraries [EPREFIX/lib]
  --includedir=DIR        C header files [PREFIX/include]
  --oldincludedir=DIR     C header files for non-gcc [/usr/include]
  --datarootdir=DIR       read-only arch.-independent data root [PREFIX/share]
  --datadir=DIR           read-only architecture-independent data [DATAROOTDIR]
  --infodir=DIR           info documentation [DATAROOTDIR/info]
  --localedir=DIR         locale-dependent data [DATAROOTDIR/locale]
  --mandir=DIR            man documentation [DATAROOTDIR/man]
  --docdir=DIR            documentation root [DATAROOTDIR/doc/PACKAGE]
  --htmldir=DIR           html documentation [DOCDIR]
  --dvidir=DIR            dvi documentation [DOCDIR]
  --pdfdir=DIR            pdf documentation [DOCDIR]
  --psdir=DIR             ps documentation [DOCDIR]
```

```
以下参数可直接定义更细致的系统目录:
  --bindir=DIR            用户系统所执行程序目录 默认：[EPREFIX/bin]
  --sbindir=DIR           系统管理员所执行程序目录 默认： [EPREFIX/sbin]
  --libexecdir=DIR        应用程序执行目录 默认：[EPREFIX/libexec]
  --sysconfdir=DIR        只读配置文件目录 默认： [PREFIX/etc]
  --sharedstatedir=DIR    数据文件描述安装目录 默认： [PREFIX/com]
  --localstatedir=DIR     本地数据安装目录 默认： [PREFIX/var]
  --libdir=DIR            对象库 默认：[EPREFIX/lib]
  --includedir=DIR        C语言头文件 默认： [PREFIX/include]
  --oldincludedir=DIR     c语言头文件旧目录 默认： [/usr/include]
  --datarootdir=DIR       read-only arch.-independent data root [PREFIX/share]
  --datadir=DIR           read-only architecture-independent data [DATAROOTDIR]
  --infodir=DIR           info文档目录 [DATAROOTDIR/info]
  --localedir=DIR         本地数据目录 [DATAROOTDIR/locale]
  --mandir=DIR            man 文档目录 [DATAROOTDIR/man]
  --docdir=DIR            ROOT文档目录 [DATAROOTDIR/doc/PACKAGE]
  --htmldir=DIR           html文档目录 [DOCDIR]
  --dvidir=DIR            DVI文档目录 [DOCDIR]
  --pdfdir=DIR            pdf文档目录 [DOCDIR]
  --psdir=DIR             ps文档目录 [DOCDIR]
```

---

> **程序名称区【Program names】**
>
> 这里没有出现的几个配置参数
>
> ```
> --program-prefix=PREFIX            prepend PREFIX to installed program names
> --program-suffix=SUFFIX            append SUFFIX to installed program names
> --program-transform-name=PROGRAM   run sed PROGRAM on installed program names
> ```
>
> ```
> --program-prefix=PREFIX            在安装的程序名前面加上前缀
> --program-suffix=SUFFIX            在安装的程序名前面加上后缀
> --program-transform-name=PROGRAM   在运行的时候要运行sed程序脚本
> ```

---

**系统类型区【System types】: 也叫交叉编译选项**

一个程序开发完成以后 , 对源代码进行编译 , 将编译后的文件发布出去形成所谓的各个平台的安装版本\(非开源的都是这么干的 , 开源的也可以这样编译后不同运行平台的编译版本\) , 这就是所谓的交叉编译 . 下面介绍有关这个平台相关性的选项 .

```
System types:
  --build=BUILD     configure for building on BUILD [guessed]
  --host=HOST       cross-compile to build programs to run on HOST [BUILD]
  --target=TARGET   configure for building compilers for TARGET [HOST]
```

```
系统类型:
  --build=BUILD     配置BUILD方式,指定编译工具所在系统的系统类型 [guessed]
  --host=HOST       指定HTTP服务器将要进行交叉编译时运行的目标系统类型的主机 [BUILD]
  --target=TARGET   指定交叉编译所产生的目标代码类型 [HOST]
```

* 通过--build选项来指定执行代码编译工作的主机 , 通常该值默认是cofig.guess\(该shell脚本和./configure在同一目录\)来猜即可\(一般就是你执行编译操作的主机\) , 当然你也可以通过这个选项指定具体的值 . 
* 编译的程序在什么机器上运行是由--host选项指定的 , 其默认值都是--build , 当两者不一样的时候就是所谓的交叉编译 . 
* --target选项用来配置编译工具 , 它只有在建立交叉编译环境的时候用到 , 正常编译和交叉编译都不会用到 . 他用--build主机上的编译器 , 编译一个新的编译器\(binutils, gcc,gdb等\) , 这个新的编译器将来编译出来的其他程序将运行在target指定的系统上 . 

---

**可选特性区【Optional Features】和 可选安装包区【Optional Packages】**

当你想在./configure时使用某个特性的时候 , 可以来配置该区块中的选项值 , 它主要分为disable和enable两大类 , 具体有哪些特性可以用过`./configure --help`来查询 . 软件的包安装的时候 , 可能会存在依赖 . 加上a软件依赖于b软件 , 那么在安装a软件的时候 , 必须要先安装b软件 , 而此时b软件偏偏不在系统的默认查询目录\(即系统无法查询到 , 或者你不想使用系统默认的b软件而想使用你自己安装的b软件\) , 就可用通过with选项来指定具体的软件包地址 , 通过without选项来指定不使用指定的软件包 .

```
Optional Features and Packages:
  --disable-option-checking  ignore unrecognized --enable/--with options
  --disable-FEATURE       do not include FEATURE (same as --enable-FEATURE=no)
  --enable-FEATURE[=ARG]  include FEATURE [ARG=yes]
  --with-PACKAGE[=ARG]    use PACKAGE [ARG=yes]
  --without-PACKAGE       do not use PACKAGE (same as --with-PACKAGE=no)
  --with-libdir=NAME      Look for libraries in .../NAME rather than .../lib
  --disable-rpath         Disable passing additional runtime library
                          search paths
  --enable-re2c-cgoto     Enable -g flag to re2c to use computed goto gcc extension
  --disable-gcc-global-regs
                          whether to enable GCC global register variables
```

```
配置可特性和包:
  --disable-option-checking 忽略未执行 --enable/--with 的所有选项
  --disable-FEATURE         关闭特征选项.将不包含FEATURE(指定的特性)(与--enable-FEATURE=no一致)
  --enable-FEATURE[=ARG]    启用特征选项.包含FEATURE(指定特性)[ARG=yes]注:结尾为YES或NO(YES为打开,NO为关闭)
  --with-PACKAGE[=ARG]         开启使用的包[ARG=yes]注:ARG可选YES或NO
  --without-PACKAGE         关闭不用的包 (与--with-PACKAGE=no一致)
  --with-libdir=NAME         库文件查找路径设置.../XXX/lib/
  --disable-rpath         禁用传递其他运行库搜索路径

  --enable-re2c-cgoto       Enable -g flag to re2c to use computed goto gcc extension
  --disable-gcc-global-regs whether to enable GCC global register variables
```

---

**影响安装的环境变量区【Some influential environment variables】**

这块主要是影响编译器的编译环境变量

    Some influential environment variables:
      CC          C compiler command
      CFLAGS      C compiler flags
      LDFLAGS     linker flags, e.g. -L<lib dir> if you have libraries in a
                  nonstandard directory <lib dir>
      LIBS        libraries to pass to the linker, e.g. -l<library>
      CPPFLAGS    (Objective) C/C++ preprocessor flags, e.g. -I<include dir> if
                  you have headers in a nonstandard directory <include dir>
      CPP         C preprocessor
      YACC        The `Yet Another Compiler Compiler' implementation to use.
                  Defaults to the first program found out of: `bison -y', `byacc',
                  `yacc'.
      YFLAGS      The list of arguments that will be passed by default to $YACC.
                  This script will default YFLAGS to the empty string to avoid a
                  default value of `-d' given by some make applications.
      CXX         C++ compiler command
      CXXFLAGS    C++ compiler flags
      CXXCPP      C++ preprocessor

---

主要和PHP相关的部分 , 就是可选区和环境变量区之间的部分 . 另开一篇内容 , 继续描述 :

