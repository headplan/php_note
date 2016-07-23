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
file_put_contents('LOG', date('Y-m-d H:i:s') . "FUCK YOU\n", FILE_APPEND);
fastcgi_finish_request();
sleep(10);
file_put_contents('LOG', date('Y-m-d H:i:s') . "FUCK YOU\n", FILE_APPEND);
sleep(10);
file_put_contents('LOG', date('Y-m-d H:i:s') . "FUCK YOU\n", FILE_APPEND);
```

代码里用sleep模拟耗时的操作,浏览时没有被堵塞,程序却都执行了,具体看日志.

从代码的可移植性讲的话,可以在代码中附上如下代码

```php
if (!function_exists("fastcgi_finish_request")) {
    function fastcgi_finish_request() {
    }
}
```

不会造成代码部署在非fpm环境下造成问题.下面是一个兼容的例子

```php
<?php
// 你要跳转的url
$url = "http://www.baidu.com/";
// 如果使用的是php-fpm
if(function_exists('fastcgi_finish_request')){ 
    header("Location: $url"); 
    ob_flush(); 
    flush(); 
    fastcgi_finish_request();
// Apache ?
}else{ 
    header('Content-type:text/html;charset=utf-8');
    if(function_exists('apache_setenv'))
        apache_setenv('no-gzip', '1');
    ini_set('zlib.output_compression', 0); 
    ini_set('implicit_flush', 1); 
    echo "<script>location='$url'</script>"; 
    ob_flush(); 
    flush();
}
// 这里是模拟你的耗时逻辑
sleep(2); 
file_put_contents('/tmp/test.log', 'ok');
```

