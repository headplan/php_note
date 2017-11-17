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



