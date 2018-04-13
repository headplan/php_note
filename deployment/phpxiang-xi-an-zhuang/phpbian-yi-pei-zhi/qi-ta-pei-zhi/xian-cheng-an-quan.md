# 线程安全

针对`--enable-maintainer-zts`参数

线程安全解释：从PHP5.2.10版本开始，有None-Thread Safe与Thread Safe两种版本的可供选择，这两种版本有何不同，作为使用者来说又应该如何选择呢？下面聚友将为您讲述。

先从字面意思上理解，None-Thread Safe就是非线程安全，在执行时不进行线程（thread）安全检查；Thread Safe就是线程安全，执行时会进行线程（thread）安全检查，以防止有新要求就启动新线程的 CGI 执行方式耗尽系统资源。

再来看PHP的两种执行方式：ISAPI和FastCGI。FastCGI执行方式是以单一线程来执行操作，所以不需要进行线程的安全检查，除去线程安全检查的防护反而可以提高执行效率，所以，如果是以 FastCGI（无论搭配 IIS 6 或 IIS 7）执行 PHP ，都建议下载、执行 non-thread safe 的 PHP （PHP 的二進位檔有兩種包裝方式：msi 、zip ，請下載 zip 套件）。而线程安全检查正是为ISAPI方式的PHP准备的，因为有许多php模块都不是线程安全的，所以需要使用Thread Safe的PHP。

说说你的疑问吧，首先这是和多线程有关系的，如果你要使用多线程（php的，如：pthreads）,那么该参数是必须的。



