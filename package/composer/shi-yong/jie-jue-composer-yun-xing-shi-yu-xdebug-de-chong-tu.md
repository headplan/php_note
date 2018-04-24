# 解决Composer运行时与Xdebug的冲突

简单的解决方案就是注释掉配置 : 

```
php --ini
```

查看配置文件位置并修改 : 

```
;zend_extension = "/path/to/my/xdebug.so"
```

更好的解决方案是在shell下创建alias命令 , 调用不包含Xdebug配置的php.ini

```
alias comp='php -c /path/to/xdebug-disabled-php.ini /path/to/composer.phar'
```

如果已经将一些扩展配置路径编译进php , 可以 : 

```
alias comp='php -n /path/to/composer.phar'
```

或直接修改composer运行脚本

```
which composer
```

```
#!/usr/bin/env php
<?php
array_shift($argv);
$arg_string = implode(' ', array_map('escapeshellarg', $argv));
$arg_prefix = preg_match('/--(no-)?ansi/', $arg_string) ? '' : '--ansi ';
$arg_string = $arg_prefix . $arg_string;
passthru("/usr/bin/env php -n -d allow_url_fopen=On -d detect_unicode=Off /path/to/composer.phar $arg_string", $return_var);
exit($return_var);
```



