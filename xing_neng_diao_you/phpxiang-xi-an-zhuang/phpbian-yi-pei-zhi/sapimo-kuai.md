# SAPI模块【SAPI modules】

包含了各种服务器抽象层的代码 . 一些与服务相关的操作都是通过SAPI接口实现 . 这些内置实现的物理位置在PHP源码的SAPI目录 . 这个目录存放了PHP对各个服务器抽象层的代码 , 例如命令行程序的实现 , Apache的mod\_php模块实现以及fastcgi的实现等等 .

在各个服务器抽象层之间遵守着相同的约定 , 这里我们称之为SAPI接口 .  每个SAPI实现都是一个\_sapi\_module\_struct结构体变量 . \(SAPI接口\) . 在PHP的源码中 , 当需要调用服务器相关信息时 , 全部通过SAPI接口中对应方法调用实现 , 而这对应的方法在各个服务器抽象层实现时都会有各自的实现 .

**目录结构**

```
drwxrwxr-x 2 vagrant vagrant 4096 6月   7 09:09 apache2handler
drwxrwxr-x 3 vagrant vagrant 4096 6月   7 09:09 cgi
drwxrwxr-x 3 vagrant vagrant 4096 6月   7 09:09 cli
drwxrwxr-x 2 vagrant vagrant  108 6月   7 09:09 embed
drwxrwxr-x 4 vagrant vagrant 4096 6月   7 09:09 fpm
drwxrwxr-x 2 vagrant vagrant  142 6月   7 09:09 litespeed
drwxrwxr-x 4 vagrant vagrant 4096 6月   7 09:09 phpdbg
drwxrwxr-x 2 vagrant vagrant 4096 6月   7 09:09 tests
```

所以SAPI模块的编译配置项 , 也是根据这些内容展开的 .

```
SAPI modules:

  --with-apxs2=FILE       Build shared Apache 2.0 Handler module. FILE is the optional
                          pathname to the Apache apxs tool apxs
  --disable-cli           Disable building CLI version of PHP
                          (this forces --without-pear)
  --enable-embed=TYPE     EXPERIMENTAL: Enable building of embedded SAPI library
                          TYPE is either 'shared' or 'static'. TYPE=shared
  --enable-fpm            Enable building of the fpm SAPI executable
  --with-fpm-user=USER    Set the user for php-fpm to run as. (default: nobody)
  --with-fpm-group=GRP    Set the group for php-fpm to run as. For a system user, this
                          should usually be set to match the fpm username (default: nobody)
  --with-fpm-systemd      Activate systemd integration
  --with-fpm-acl          Use POSIX Access Control Lists
  --with-litespeed        Build PHP as litespeed module
  --enable-phpdbg            Build phpdbg
  --enable-phpdbg-webhelper  Build phpdbg web SAPI support
  --enable-phpdbg-debug      Build phpdbg in debug mode
  --disable-cgi           Disable building CGI version of PHP
```

```
SAPI modules:

  --with-apxs2=FILE       编译共享的apache2.0处理程序的模块.FILE是可选的Apache apxs工具的路径,默认指向apxs.
  --disable-cli           PHP4.3.0之后的版本有效.禁止编译PHP的CLI版本(使用它将同时强制使用--without-pear选项)
  --enable-embed=TYPE     实验性:建立内嵌的SAPI库.参数为shared,static.鸟哥的博客中有对次解释.http://www.laruence.com/2008/09/23/539.html
  # PHP容许你在C/C++语言中调用PHP/ZE提供的函数
  --enable-fpm            Enable building of the fpm SAPI executable
  --with-fpm-user=USER    Set the user for php-fpm to run as. (default: nobody)
  --with-fpm-group=GRP    Set the group for php-fpm to run as. For a system user, this
                          should usually be set to match the fpm username (default: nobody)
  --with-fpm-systemd      Activate systemd integration
  --with-fpm-acl          Use POSIX Access Control Lists
  --with-litespeed        Build PHP as litespeed module
  --enable-phpdbg            Build phpdbg
  --enable-phpdbg-webhelper  Build phpdbg web SAPI support
  --enable-phpdbg-debug      Build phpdbg in debug mode
  --disable-cgi           禁止编译CGI版本的PHP.PHP4.3.0之后的版本有效.
  # PHP5.3.0起,这个选项会启用FastCGI,而在以前必须使用--enable-fastcgi启用FastCGI
```



