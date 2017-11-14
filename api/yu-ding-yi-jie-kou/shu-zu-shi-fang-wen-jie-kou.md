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

```php
<?php

namespace InterfaceTest;

use ArrayAccess;

class TestArrayAccess implements ArrayAccess
{
    # 存储数据
    private $data = [];

    public function __construct($data)
    {
        $this->data = $data;
    }

    /**
     * 以对象的方式访问数组中的数据
     *
     * @param $key
     * @return mixed
     */
    public function __get($key)
    {
        return $this->data[$key];
    }

    /**
     * 以对象方式添加一个数组元素
     *
     * @param $key
     * @param $val
     */
    public function __set($key, $val)
    {
        $this->data[$key] = $val;
    }

    /**
     * 以对象方式判断数组元素是否设置
     *
     * @param $key
     * @return bool
     */
    public function __isset($key)
    {
        return isset($this->data[$key]);
    }

    /**
     * 以对象方式删除一个数组元素
     *
     * @param $key
     */
    public function __unset($key)
    {
        unset($this->data[$key]);
    }

    public function offsetGet($offset)
    {
        return $this->offsetExists($offset) ? $this->data[$offset] : null;
    }

    public function offsetSet($offset, $value)
    {
        if (is_null($offset)) {
            $this->data[] = $value;
        } else {
            $this->data[$offset] = $value;
        }
    }

    public function offsetExists($offset)
    {
        return isset($this->data[$offset]);
    }

    public function offsetUnset($offset)
    {
        if ($this->offsetExists($offset)) {
            unset($this->data[$offset]);
        }
    }

    public function __invoke()
    {
        return $this->data;
    }
}
```

**问题记录**

如果在实现ArrayAccess的类的对象上调用array\_key\_exists\(\) , 那么ArrayAccess:: offsetExists\(\)将不会被调用 .

实现 ArrayAccess 的对象不支持递增/递减运算符 , 当然引用传递也是不支持的 :

```php
<?php

class MyArray implements ArrayAccess
{
    // offsetSet, offsetGet etc implemented
}

$v = 'hi';
$x = new MyArray() ;
$x[0] = 0 ;
$x[0]++ ; //error 'Indirect modification of overloaded element has no effect'
$x[0] += 1 ; // this works OK.
$x['a'] = $v;
$x['b'] = &$v; //Fatal error: Cannot assign by reference to overloaded object
$v = 'baby';
```

**应用场景**

在 ArrayAccess 对象中使用的索引不仅限于字符串和整数\(意思就是不限于标量键\) , 就像数组 . 可以使用任何类型的索引 , 只要编写实现来处理它们 . 例如用在SplObjectStorage类上 .

尽管不限于标量键 , 但别忘记 :

```
$x[1]  offset is integer 1
$x['1'] offset is integer 1
$x['1.'] offset is string '1.'
```

还有前面提到的问题 , 其实大部分php的数组函数都不能与 ArrayAccess 对象一起使用\(比如sizeof\(\),array\_values\(\)等等\) , 这点需要注意 . 但是还是有解决方式的 , 那就是使用\_\_invoke\(\)魔术方法 , 尝试以调用函数的方式调用一个对象 :

```php
# 在之前的类中添加
public function __invoke()
{
    return $this->data;
}

# 使用方式如下
$testArrayAccess = new \InterfaceTest\TestArrayAccess($data);
$arr = array_values($testArrayAccess());
var_dump($arr);
# 现在不会报错了
```

对于前面提到的引用传递问题 , 也可以在方法中添加 , 或者直接&\_\_invoke\(\)也可以 , 同时这也解决了一些array函数无法使用的问题 , 例如 : 

```php
int array_push ( array &$array , mixed $value1 [, mixed $... ] )
```

这种函数的问题也解决了 , 代码 : 

```php
public function &__invoke()
{
    return $this->data;
}
```



