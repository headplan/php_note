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
|  |  |  |
|  |  |  |
|  |  |  |
|  |  |  |



