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



