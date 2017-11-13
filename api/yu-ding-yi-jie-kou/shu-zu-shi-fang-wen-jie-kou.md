# 数组式访问接口

#### ArrayAccess

ArrayAccess接口提供像访问数组一样访问对象的能力 .

```php
ArrayAccess {  
    // 获取一个偏移位置的值  
    abstract public mixed offsetGet ( mixed $offset )  
    // 设置一个偏移位置的值  
    abstract public void offsetSet ( mixed $offset , mixed $value )  
    // 检查一个偏移位置是否存在  
    abstract public boolean offsetExists ( mixed $offset )  
    // 复位一个偏移位置的值  
    abstract public void offsetUnset ( mixed $offset )  
}  
```



