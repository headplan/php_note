# 使用命令行

Yaf支持在命令行下运行 , 以此来方便开发人员调试 .

#### 使用样例

要使得yaf在命令行模式下运行 , 有俩种方式 :

第一种方式专门为用Yaf开发Contab等任务脚本设计的方式 , 这种方式下 , 对Yaf的唯一要求就是能自动加载所需要的Model或者类库 , 所以可以简单的通过`Yaf_Application::execute`来实现 .

第二种方式 , 是为了在命令行下模拟请求 . 运行和Web请求一样的流程 , 从而可以用来在命令行下测试Yaf应用 , 对于这种方式 , 唯一的关键点就是请求体 , 默认的请求是由yaf\_application实例化 , 并且交给yaf\_dispatcher的 , 而在命令行模式下 , Yaf\_Application并不能正确的实例化一个命令行请求 , 所以需要变更一下 , 请求需要手动实例化 .

```php
<?php
$request = new Yaf_Request_Simple();
print_r($requst);
```

如上面的例子 , `Yaf_Request_Simple`的构造函数可以不接受任何参数 , 在这种情况下 , `Yaf_Request_Simple`会在命令行参数中 , 寻找一个字符串参数 , 如果找到 , 则会把请求的request\_uri置为这个字符串 .

> 当然 , Yaf\_Request\_Simple是可以接受5个参数的 .

运行上面的代码 :

```
$ php request.php
Yaf_Request_Simple Object
(
[module] => 
[controller] => 
[action] => 
[method] => CLI
[params:protected] => Array
(
)

[language:protected] => 
[_base_uri:protected] => 
[uri:protected] => 
[dispatched:protected] => 
[routed:protected] => 
)
```

带参数运行 :

```
$ php request.php  "request_uir=/index/hello"
Yaf_Request_Simple Object
(
[module] => 
[controller] => 
[action] => 
[method] => CLI
[params:protected] => Array
(
)

[language:protected] => 
[_base_uri:protected] => 
[uri:protected] => index/hello  //注意这里
[dispatched:protected] => 
[routed:protected] => 
)
```

还可以完全指定Yaf\_Request\_Simple::\_\_construct的5个参数 :

```php
<?php
$request = new Yaf_Request_Simple("CLI", "Index", "Controller", "Hello", array("para" => 2));
print_r($requst);
```

运行输出 :

```
$ php request.php 
Yaf_Request_Simple Object
(
[module] => Index
[controller] => Controller
[action] => Hello
[method] => CLI
[params:protected] => Array
(
[para] => 2
)

[language:protected] => 
[_base_uri:protected] => 
[uri:protected] => 
[dispatched:protected] => 
[routed:protected] => 1    //注意这里
)
```

> 可以看到一个比较特别的就是 , routed属性变为了TRUE , 这就代表着如果我们手动指定了构造函数的参数 , 那么这个请求不会再经过路由 , 而直接是路由完成状态 .

#### 分发请求

要走命令行 , 可以修改入口文件 :

```php
<?php
$app = new Yaf_Application("conf.ini");
$app->bootstrap()->run();
```

修改为

```php
<?php
$app = new Yaf_Application("conf.ini");
$app->getDispatcher()->dispatch(new Yaf_Request_Simple());
```



