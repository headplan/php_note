# PHP-FPM

#### 全局配置

```
# 我的配置在这里
/usr/local/php/etc/php-fpm.conf
```

PHP-FPM最重要的全局设置 , 如果在指定的一段时间内有指定个子进程失效了 , 让PHP-FPM主进程重启 . 这是PHP-FPM进程的基本安全保障 , 能解决简单的问题 , 但是PHP代码太坑也是没办法的 . 

```
# 在指定的一段时间内 , 如果失效的PHP-FPM子进程数超过这个值,PHP-FPM主进程就优雅的重启
emergency_restart_threshold = 30
# 设定上一个配置采用的时间跨度
emergency_restart_interval = 60s
```

```
; http://php.net/manual/zh/install.fpm.configuration.php
; php-fpm.conf全局配置详解
; 这个配置所有的相对路径都是相对PHP的安装路径的

[global]
; pid设置,默认在安装目录中的var/run/php-fpm.pid,建议开启
pid = run/php-fpm.pid
; 错误日志,默认在安装目录中的var/log/php-fpm.log
error_log = log/php-fpm.log
; 错误级别
; alert:必须立即处理
; error:错误情况
; warning:警告情况
; notice:一般重要信息
; debug:调试信息
; 默认:notice.
log_level = warning

; 表示在emergency_restart_interval所设值内
; 出现SIGSEGV或者SIGBUS错误的php-cgi进程数
; 如果超过emergency_restart_threshold个
; php-fpm就会优雅重启.这两个选项一般保持默认值
emergency_restart_threshold = 30
emergency_restart_interval = 60s

process_control_timeout = 5s
daemonize = yes
```



