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



#### 使用phpize构建扩展

#### 显示扩展相关信息



