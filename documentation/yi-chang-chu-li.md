# 异常处理

* 文件找不到
* 数据库连接失败
* URL接口访问失败

不可能die\(\) , 或者return false . 如果对方需要接收一个array , 那false就会报错 .

**更好的处理方式 : 异常处理**

```
try {
    # 抛出不同种类的异常
    throw new GreenException();
    throw new RedException();
    return;
} catch (GreenException $e) {
    # 接Exception
    echo $e->getMessage();
} catch (RedException $e2) {
    echo $e2->getMessage();
} finally {
    // 都会执行,除非try中die了
    return;
}
```

* try 中 return 后 finally 会继续执行 , 如果 finally 中也有return , 则最终返回值为 finally 中 return 的值 . 
* try 中 die 或 exit 后 finally 不会执行 . 

```php
<?php
if(php_sapi_name() != 'cli') ob_start('nl2br');

function trytest() {
    try {
        echo "try\n";
        //throw new Exception("I'm dying, Help!!");
        //die; 
        return false;
    } catch ( Exception $e ) {
        echo $e->getMessage()."\n";
    } finally {
        echo "finally\n";
        return true;
    }
}

var_dump(trytest());
```

**PHP 中充满了Notice,Warning等等 . **

`Warning: file_get_contents() expects at least 1 parameter, 0 given in /2.php on line 9`

怎样接住这些错误?

使用函数

```php
set_error_handler(callable $handler[, int $error_types ]);
```

可以设置自定义的错误处理函数 .

* 默认的PHP错误处理流程会绕过 $error\_types 中指定的错误类型 , 除非 $handler 中返回false
* error\_reporting\(\) 不再有效 , 除非自己在 $handler 中按当前的 error\_reporting 作出判断
* 收到错误后是否 die 由$handler 决定

```php
<?php
# 可通过 set_error_handler + ErrorException 拦截php默认报错,转为异常

function trytest() {
    try {
        file_get_contents(); // parameter missing. w
    } catch ( Exception $e ) {
        echo $e->getMessage()."\n";
    } finally {
        return true;
    }
}

function myErrorHandler($errno, $errstr, $errfile, $errline){
    throw new ErrorException("Exception: ".$errstr, 0, $errno, $errfile, $errline);
}

set_error_handler("myErrorHandler");

trytest();
```

如果是致命错误 , 比如调用一个没有定义的函数 , 依然是会报错的 : 

```
Fatal error: Call to undefined function asdfwe() 
```

set\_error\_handler 只能拦截部分错误 , 对于如下拦截不了的怎么办? 比如 : 

```
E_ERROR, E_PARSE, E_CORE_ERROR, E_CORE_WARNING, E_COMPILE_ERROR, E_COMPILE_WARNING
```

答案是使用register\_shutdown\_function\(\)函数 , 在代码执行完毕之前要做的事 , 拦截方式 : 

```php
<?php

error_reporting(0);

register_shutdown_function(function() {
    if ($error = error_get_last()) {
        print_r($error);
    }
});

// Fatal error: Call to undefined function asdfwe()
try {
    asdfwe();
} finally {
    echo 123;
}
```



