# 理解MySQL网络协议

#### 代理

这里以MySQL为例 , 连接MySQL服务可以使用TCP协议 , 例如使用MySQL的客户端连接 , 网络连接 . 如果是在本机 , 也可以使用sock协议 .

这里我们使用自己的服务端当成MySQL服务器 , 然后使用MySQL客户端去连接服务 :

```php
$headplan_worker->onConnect = function ($connection)
{
    echo 'I am MySQL' . PHP_EOL;
};
```

在控制台中可以看到 , 输出的结果I am MySQL . 



