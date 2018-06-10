# 多进程处理

安装多进程控制扩展 : pcntl

过程略...

```
php -m | grep pcntl
```

相关手册

http://php.net/manual/zh/intro.pcntl.php

#### 简介

PHP的进程控制支持实现了Unix方式的进程创建, 程序执行, 信号处理以及进程的中断。 进程控制不能被应用在Web服务器环境，当其被用于Web服务环境时可能会带来意外的结果。

这份文档用于阐述每个进程控制函数的通常用法。关于Unix进程控制的更多信息建议您查阅 系统文档中关于fork（2）,waitpid（2），signal（2）等的部分或更全面的参考资料比如 《Unix环境高级编程》（作者：W. Richard Stevens，Addison-Wesley出版）。

PCNTL现在使用了ticks作为信号处理的回调机制，ticks在速度上远远超过了之前的处理机制。 这个变化与“用户ticks”遵循了相同的语义。您可以使用**declare\(\)**语句在程序中指定允许发生回调的位置。这使得我们对异步事件处理的开销最小化。在编译PHP时 启用pcntl将始终承担这种开销，不论您的脚本中是否真正使用了pcntl。

