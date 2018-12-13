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

