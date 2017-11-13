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

**代码示例**

```

```

**问题记录**

如果在实现ArrayAccess的类的对象上调用array\_key\_exists\(\) , 那么ArrayAccess:: offsetExists\(\)将不会被调用 . 

**应用场景**

在 ArrayAccess 对象中使用的索引不仅限于字符串和整数, 就像数组: 您可以使用任何类型的索引, 只要您编写实现来处理它们。例如用在SplObjectStorage类上 . 

