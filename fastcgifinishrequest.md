# fastcgi\_finish\_request

boolean **fastcgi\_finish\_request** \( void \)

此函数冲刷\(flush\)所有响应的数据给客户端并结束请求.这使得客户端结束连接后,需要大量时间运行的任务能够继续运行.

当PHP运行在FastCGI模式时,PHP FPM提供了一个名为fastcgi\_finish\_request的方法.按照文档上的说法,此方法可以提高请求的处理速度,如果有些处理可以在页面生成完后再进行,就可以使用这个方法.

例子:

```php
<?php
echo 'test';
fastcgi_finish_request(); /*完成响应,关闭连接*/
/*记录日志*/
file_put_contents('LOG', 'FUCK YOU');
echo 'end';
```

通过浏览器访问此脚本, 结果发现并没有输出相应的字符串,但却生成了相应的文件.由此说明在调用fastcgi\_finish\_request后,客户端响应就已经结束,但与此同时服务端脚本却继续运行!

```php
<?php
echo 'test';
file_put_contents('LOG', date('Y-m-d ') . 'FUCK YOU', FILE_APPEND);
```











