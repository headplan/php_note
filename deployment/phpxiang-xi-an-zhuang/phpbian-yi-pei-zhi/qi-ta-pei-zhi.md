# 其他配置

```
PEAR:

  --with-pear=DIR         Install PEAR in DIR [PREFIX/lib/php] 默认安装
  --without-pear          Do not install PEAR 不安装

Zend:

  --enable-maintainer-zts Enable thread safety - for code maintainers only!! 参考下面的[线程安全]文章
  --disable-inline-optimization
                          If building zend_execute.lo fails, try this switch
                          # 原来优化时需要开启,现在已经默认开启了.
                          # 使 PHP 有可能更快地执行（不需使用类似 Zend Engine 的软件进行额外的操作码优化）
                          如果编译zend_execute.lo失败，则使用此参数
  --disable-zend-signals  whether to enable zend signal handling # 信号处理zend signal看一下下面你的文章[zend signal]

TSRM(Thread Safe Resource Manager线程安全资源管理器相关配置):

  --with-tsrm-pth=pth-config
                          Use GNU Pth 使用GNU方式管理线程
  --with-tsrm-st          Use SGI's State Threads 使用SGI静态线程方式管理线程
  --with-tsrm-pthreads    Use POSIX threads (default) 使用POSIX线程方式管理线程

Libtool:

  --enable-shared=PKGS    Build shared libraries default=yes
  --enable-static=PKGS    Build static libraries default=yes
  --enable-fast-install=PKGS
                          Optimize for fast installation default=yes
  --with-gnu-ld           Assume the C compiler uses GNU ld default=no
  --disable-libtool-lock  Avoid locking (might break parallel builds)
  --with-pic              Try to use only PIC/non-PIC objects default=use both
  --with-tags=TAGS        Include additional configurations automatic
```



