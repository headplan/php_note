# 配置文件

传递一个config数组或ini配置文件 , Yaf的配置文件会和用户的配置文件合并 . 区别在于 , Yaf的配置文件是以"yaf."或"application."开头 , 如果两项都存在 , 则"application."生效 .

```php
<?php
$configs = array(
    "application" => array(
        "directory" => dirname(__FILE__),
        "dispatcher" => array(
              "catchException" => 0,
        ),
        "view" => array(
               "ext" => "phtml",
        ),
    ),
);
$app = new Yaf_Application($config);
```

```
[yaf]
yaf.directory = APPLICATION_PATH "/appliation"
yaf.dispatcher.catchException = 0

[product : yaf]
; user configuration list here
```

**Yaf必填的配置项**

| 名称 | 值类型 | 说明 |
| :--- | :--- | :--- |
| application.directory | String | 应用的绝对目录路径,唯一一个没有默认值的选项,需要手动设置 |
| application.ext | String | PHP脚本的扩展名,类的自动加载需要用到它 |
| application.view.ext | String | 视图模板扩展名 |
| application.modules | String | 注册的模块列表,以逗号分隔,用于路由处理,特别是当PATH\_INFO超过三段的时候,Yaf需要用它来判断第一段是否是一个模块. |
| application.library | String | 本地类库的目录,该配置项也可以是一个数组,当它是数组的时候,类库的路径将尝试使用application.library.directory的值 |
| application.library.directory | String | application.library的别名 |
| application.library.namespace | String | 逗号分隔的本地类库命名空间前缀 |
| application.bootstrap | String | Bootstrap类脚本文件的绝对路径 |
| application.baseUri | String | 路由处理中需要忽略的路径前缀.举个例子,请求"/prefix/controller/action"时.如果你将application.baseUri设置为"/prefix",那么只有"/controller/action"会被当做路由路径.这个值通常不需要设置. |
| application.dispatcher.throwException | bool | 开启此项,Yaf会在发生错误的地方抛出异常. |
| application.dispatcher.catchException | bool | 是否使用默认的异常捕获Controller.如果开启,在有未捕获的异常的时候,控制权会交给ErrorController的errorAction方法,可以通过$request-&gt;getException\(\)获得此异常对象. |
| application.dispatcher.defaultRoute | String | 默认路由,如果未指定,静态路由会被当做是默认路由. |
| application.dispatcher.defaultModule | String | 默认模块名 |
| application.dispatcher.defaultController | String | 默认控制器名 |
| application.dispatcher.defaultAction | String | 默认动作名 |
| application.system | String | 在application.ini中设置Yaf运行时配置.但是请注意只有PHP\_INI\_ALL的配置项才可以在这里被修改, 此选项从2.2.0开始引入. |



