# 异常处理

**错误处理**

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





