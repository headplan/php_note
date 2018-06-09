# 理解RPC远程调用

远程调用 , 即Remote Procedure Call . 也就是RPC调用 . 

简单理解 : 

1.本地调用 , 远程机器执行 . 

2.某些调用形式 , 用浏览器访问可以看到xml描述 . 

3.某些语言中调用形式 , 需要在本地生成一个伪调用类 . 

4.如果PHP做服务端 , java等其他语言也可以调用 . 

#### 相关代码

> 代码查看本地rpc\_server.php等 .

通过浏览器访问rpc服务 , 其中判断浏览器过来的url , 然后判断指定文件是否存在 , 这个文件就是我们提供的远程调用的服务 . 比如 , 新建service文件夹 , 创建一个news.php文件 , 创建NewsService类作为被调用的服务 . 

```php
<?php

class NewsService
{
    public function display()
    {
        return 'I\'m RPC Server';
    }

    public function show()
    {
        return 'Github被微软收购.';
    }
}
```



