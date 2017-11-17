# 安装

本节介绍如何安装 Xdebug .

#### 预编译的 Windows 模块

有一些用于Windows的预编译模块 , 它们都是PHP的 "非调试版本" . 可以在这里下载 :

[https://xdebug.org/download.php](https://xdebug.org/download.php)

按照以下说明安装Xdebug :

[https://xdebug.org/wizard.php](https://xdebug.org/wizard.php)

#### PECL安装

Xdebug 0.9.0可以通过PEAR/PECL安装 . 只适用于与PEAR 0.9. 1-dev版或更高的版本 , 在一些UNIX中 .

PEAR/PECL安装 :

```
pecl install xdebug
```

安装之后还是需要配置ini文件 :

```
zend_extension="/usr/local/php/modules/xdebug.so"
```

> 注意 : 这里是zend\_extension而不是extension

#### Mac安装

通过Homebrew安装

```
brew install homebrew/php/<php-version>-xdebug
# eg.
brew install homebrew/php/php71-xdebug
brew search xdebug # 可以搜索软件包
```

安装之后配置文件会存在自定义配置路径中 :

```
/usr/local/etc/php/7.1/conf.d/ext-xdebug.ini
```

#### 源码安装

下载稳定版

```
git clone git://github.com/xdebug/xdebug.git
```

还可以在[https://github.com/derickr/xdebug](https://github.com/derickr/xdebug)迁出其他版本 .

**编译**

可以使用前面提到的向导检查phpinfo\(\)输出的所有内容 ,

```
https://xdebug.org/wizard.php
php -i "(command-line 'phpinfo()')" > phpinfo.php
# 把所有内容复制到其中执行后,即可以查看安装流程等.
```

它为您提供了要下载的正确文件 , 以及要使用的路径 .

将Xdebug与PHP的其余部分分开编译时 , 需要访问脚本 "phpize" 和 "php configure" , 可以去PHP的源文件中找 .

编译安装步骤 :

```
tar -xvzf xdebug-2.5.4.tgz
cd xdebug-2.5.4
phpize
./configure
make
make install
cp modules/xdebug.so /usr/local/lib/php/extensions/no-debug-non-zts-20160303
# 配置php.ini
zend_extension = /path/to/xdebug.so
```

#### 配置PHP使用Xdebug

前面安装之后 , 需要添加配置到php.ini . 对于PHP5.3之前的版本和PHP线程的使用 , 配置 :

```
zend_extension_ts="/wherever/you/put/it/xdebug.so"
```

> 注意 : 如果是自己编译PHP , 配置用了--enable-debug , 配置必须写zend\_extension\_debug .

如果要将 Xdebug 和 OPCache 一起使用 , 则必须在 OPCache 后加载 Xdebug . 否则 , 它们将无法正常工作 .

总之 , 从 php 5.3 开始 , 必须使用 zend\_extension 的 php. ini 设置名称 , 而不是 zend\_extension\_ts, 也不 zend\_extension\_debug . 但是 , 编译选项 \(ZTS/normal build; debug/non-debug\) 仍然需要与 PHP 使用的内容相匹配 . 

配置之后 , 重启web服务器 . 就可以在phpinfo查看有没有xdebug了 , 也可以使用php -m查看加载的模块 . 

> Xdebug应该出现两次 , 一次在 "PHP 模块" 下 , 一次在 "Zend模块" 下



