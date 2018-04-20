# HTTP缓存

框架核心文件实现HttpKernelInterface接口 : 

```php
public function handle(
    Request $request,
    $type = HttpKernelInterface::MASTER_REQUEST,
    $catch = true
) {
    // ...
}
```

在响应时设置缓存10秒

```
try{
    ...
    $response->setTtl(10);
}
```

在入口文件使用缓存

```
$framework = new HttpKernel\HttpCache\HttpCache(
    $framework,
    new HttpKernel\HttpCache\Store(__DIR__.'/../storage/cache'),
    new HttpKernel\HttpCache\Esi(),
    [
        'debug' => true,
    ]
);
```

为了测试响应的缓存 , 在LeapYearController的响应内容中加入随机数 : 

```
$response = new Response($year.'是闰年!'.'现在时间是:'.date('Y-m-d H:i:s', time()).rand());
```



