# 构建PHP扩展

#### 加载共享扩展

前面我们已经提到 , php扩展可以静态地构建到php二进制文件中 , 或者编译成共享对象就是".so"文件 . 静态链接是大多数捆绑扩展的默认值 , 而共享对象可以通过显式传递 "--enable-EXTNAME=shared" 或者 "--with-EXTNAME=shared" 配置 .

静态扩展将始终可用的 , 不需要要额外的配置 . 而共享扩展是需要使用 "extension" 或 "zend\_extension"的ini配置来加载共享扩展的 . 两个选项都采取 ".so" 文件的绝对路径或相对于 "extension\_dir"的路径设置 . \("zend\_extension"的相对路径仅在 php 5.5 中可用 , 以前的版本必须使用绝对路径.\)

例如 , 配置编译 :

```
~/php-src> ./configure --prefix=$HOME/myphp \
                       --enable-debug --enable-maintainer-zts \
                       --enable-opcache --with-gmp=shared
```

在这种情况下 , opcache 扩展和 gmp 扩展都被编译到位于 "modules/" 目录中的共享对象中 . 可以通过更改 extension\_dir 目录或通过传递绝对路径来加载这两个项 :

    ~/php-src> sapi/cli/php -dzend_extension=`pwd`/modules/opcache.so \
                            -dextension=`pwd`/modules/gmp.so
    # or
    ~/php-src> sapi/cli/php -dextension_dir=`pwd`/modules \
                            -dzend_extension=opcache.so -dextension=gmp.so

在执行"make install"步骤时候 , 上面的两个 '.so' 文件将被移动到你安装的php的扩展目录 , 可以使用"php-config --extension-dir"命令查看 . 对于以上的构建选项 , 会显示 "/home/myuser/myphp/lib/php/extensions/no-debug-non-zts-MODULE\_API" . 此值是 "extension\_dir"的ini选项的默认设置\(意思是这里配置了扩展默认目录 , 就不用再配置指定的家在路径了\) , 因此不用显式的指定它 , 可以直接加载扩展 :

```
~/myphp> bin/php -dzend_extension=opcache.so -dextension=gmp.so
```

这给我们留下了一个问题:应该使用哪种机制?共享对象允许您拥有一个基本的php二进制文件 , 并通过php.ini加载附加扩展 . 发行版通过提供一个裸的php包并将扩展作为单独的包分发来使用 . 另一方面 , 如果您正在编译自己的php二进制文件 , 您可能不需要这样做 , 因为您已经知道需要哪些扩展 .

作为一个经验法则 , 您将由php本身捆绑的使用静态链接的扩展 , 其他的所有都使用共享扩展 . 原因很简单 , 将外部扩展作为共享对象来构建是更容易的\(或者至少是更少的侵扰性\) , 就像您在一瞬间会看到的那样 . 另一个好处是您可以在不重建php的情况下更新扩展 .

#### 从PECL安装扩展

PECL是php扩展社区库 , 提供了大量的php扩展 . 当从主php分发中删除扩展时 , 它们通常继续存在于pecl中 . 同样 , 现在与php捆绑在一起的许多扩展都是以前的pecl扩展 .

除非你在你的php版本的配置阶段指定了 "--without-pear" , 否则"make install"将下载并安装PECL作为PEAR的一部分 . 您将在 "$PREFIX/bin" 目录中找到pecl脚本 . 现在安装扩展就像运行 pecl install EXTNAME 一样简单 , 例如:

```
~/myphp> bin/pecl install apcu-4.0.2
```

此命令将下载、编译并安装 "APCu" 扩展 . 结果将会生成一个"apcu.so"文件到扩展目录 , 然后可以通过传递 "extension=apcu.so" ini 选项配置加载 .

虽然对于最终使用的用户来说 pecl 安装是非常方便的 , 但是它对扩展开发人员没什么用 . 在下面 , 我们将介绍两种手动构建扩展的方法 : 要么将其导入主 php 源码\(这允许静态链接\) , 要么通过执行外部生成 \(仅共享\) .

#### 向PHP源码包中添加扩展

第三方扩展与php捆绑的扩展之间没有根本的区别 . 因此 , 只需将其复制到php源码包中 , 然后使用通常的生成过程 , 就可以构建外部扩展 . 我们将以 APCu 为例来演示这一点 .

首先 , 必须将扩展的源代码放到PHP源代码包的"ext/EXTNAME"目录中 . 如果扩展可通过git下载 , 这就像从 "ext/" 中克隆存储库一样简单 :

```
~/php-src/ext> git clone https://github.com/krakjoe/apcu.git
```

另外, 您也可以下载源压缩并提取它 :

```
/tmp> wget http://pecl.php.net/get/apcu-4.0.2.tgz
/tmp> tar xzf apcu-4.0.2.tgz
/tmp> mkdir ~/php-src/ext/apcu
/tmp> cp -r apcu-4.0.2/. ~/php-src/ext/apcu
```

该扩展将包含一个 "config.m4" 文件 , 它指定一个特定的 "extension-specific" 生成指令供 "autoconf" 使用 . 要将它们合并到 "./configure" 脚本中 , 您必须再次运行 "./buildconf" . 为了确保配置文件真的重新生成, 建议预先删除它 :

```
~/php-src> rm configure && ./buildconf --force
```

现在, 您可以使用 "./config.nice" 脚本将 APCu 添加到现有配置中 , 或者重新启动一个全新的配置行 :

```
~/php-src> ./config.nice --enable-apcu
# or
~/php-src> ./configure --enable-apcu # --other-options
```

最后运行 "make -jN" 来执行实际的构建 . 正如我们没有使用 "--enable-apcu=shared" 的扩展是静态链接到PHP的 , 即不需要额外的行动来利用它 . 显然 , 您还可以使用 "make install" 来安装生成的二进制文件 . 

#### 使用phpize构建扩展

还可以通过使用php构建部分中已经提到的phpize脚本 , 来分别构建php的扩展 . 

"phpize" 扮演的角色与用于php构建的 "./buildconf" 脚本类似 . 首先 , 它将通过复制 "$PREFIX/lib/php/build" 的文件将php构建系统导入到您的扩展中。这些文件中有 "acinclude.m4" \(PHP的 "M4" 宏\)、"phpize.m4" \(将在您的扩展中重命名为configure.in , 并包含主生成指令\) 和 "run-tests. php" 脚本 . 

然后, "phpize" 将调用 "autoconf" 来生成 "./configure" 文件 , 可用于自定义扩展的构建 . 注意 , 没有必要将 "--enable-apcu" 传递给它 , 因为这是隐式假定的 . 相反 , 您应该使用 "--with-php-config" 来指定您的 "php-config" 脚本的路径 : 

```
/tmp/apcu-4.0.2> ~/myphp/bin/phpize
Configuring for:
PHP Api Version:         20121113
Zend Module Api No:      20121113
Zend Extension Api No:   220121113

/tmp/apcu-4.0.2> ./configure --with-php-config=$HOME/myphp/bin/php-config
/tmp/apcu-4.0.2> make -jN && make install
```

在构建扩展时, 您应该始终指定 "--with-php-config" 选项 \(除非您只有一个单独的、全局安装的 php\), 否则 "./configure" 将无法正确确定要生成的 php 版本和标志 . 指定 "php-config" 脚本还可确保 "make install" 将正确移动生成的 ".so" 文件 \(可以在 "modules/" 目录中找到\) 到正确的扩展目录 . 

由于 "run-tests.php" 文件在 "phpize" 阶段也被复制 , 所以您可以使用 "make test" \(或对 "run-tests" 的显式调用\) 运行扩展测试 . 

如果增量生成在更改后失败 , 也可以使用 "make clean" 删除已编译的目标对象 , 并允许您强制对扩展进行完全重建 . 此外, "phpize" 提供了一个清洁选项, 通过 "phpize --clean" . 这将删除 "phpize" 导入的所有文件以及由 "./configure" 脚本生成的文件 . 

#### 显示扩展相关信息

PHP CLI二进制文件提供了几个选项来显示有关扩展的信息 . 您已经知道的"-m"选项 , 它将列出所有已加载的扩展 . 您可以使用它来验证是否正确加载了扩展 : 

```
~/myphp/bin> ./php -dextension=apcu.so -m | grep apcu
apcu
```

有几个以 "-r" 开头的进一步公开反射功能的开关 . 例如 , 您可以使用 "-ri" 来显示扩展的配置 : 

```
~/myphp/bin> ./php -dextension=apcu.so --ri apcu
apcu

APCu Support => disabled
Version => 4.0.2
APCu Debugging => Disabled
MMAP Support => Enabled
MMAP File Mask =>
Serialization Support => broken
Revision => $Revision: 328290 $
Build Date => Jan  1 2014 16:40:00

Directive => Local Value => Master Value
apc.enabled => On => On
apc.shm_segments => 1 => 1
apc.shm_size => 32M => 32M
apc.entries_hint => 4096 => 4096
apc.gc_ttl => 3600 => 3600
apc.ttl => 0 => 0
# ...
```

"-re" 开关列出了由扩展添加的所有 ini 设置、常量、函数和类:

```
~/myphp/bin> ./php -dextension=apcu.so --re apcu
Extension [ <persistent> extension #27 apcu version 4.0.2 ] {
  - INI {
    Entry [ apc.enabled <SYSTEM> ]
      Current = '1'
    }
    Entry [ apc.shm_segments <SYSTEM> ]
      Current = '1'
    }
    # ...
  }

  - Constants [1] {
    Constant [ boolean APCU_APC_FULL_BC ] { 1 }
  }

  - Functions {
    Function [ <internal:apcu> function apcu_cache_info ] {

      - Parameters [2] {
        Parameter #0 [ <optional> $type ]
        Parameter #1 [ <optional> $limited ]
      }
    }
    # ...
  }
}
```

"-re" 开关只适用于正常的扩展, Zend扩展使用 "-rz" 代替 . 例如opcache扩展 : 

```
~/myphp/bin> ./php -dzend_extension=opcache.so --rz "Zend OPcache"
Zend Extension [ Zend OPcache 7.0.3-dev Copyright (c) 1999-2013 by Zend Technologies <http://www.zend.com/> ]
```

正如您所看到的 , 这并不显示任何有用的信息 . 原因是 opcache 注册了一个正常的扩展和一个Zend扩展 , 其中前者包含所有的 ini 设置, 常量和函数 . 因此 , 在这个特殊的情况下 , 你仍然需要使用"-re"选项 . 其他的Zend扩展 , 可以使用" -rz "选项 . 



