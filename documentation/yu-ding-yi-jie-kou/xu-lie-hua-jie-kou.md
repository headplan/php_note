# 序列化接口

Serializable是PHP的自定义序列化的接口 . 实现此接口的类将不再支持[\_\_sleep\(\)](http://php.net/manual/zh/language.oop5.magic.php#language.oop5.magic.sleep)和[\_\_wakeup\(\)](http://php.net/manual/zh/language.oop5.magic.php#language.oop5.magic.sleep) . 不论何时，只要有实例需要被序列化，serialize 方法都将被调用。它将不会调用 \_\_destruct\(\) 或有其他影响，除非程序化地调用此方法。当数据被反序列化时，类将被感知并且调用合适的 unserialize\(\) 方法而不是调用 \_\_construct\(\)。如果需要执行标准的构造器，你应该在这个方法中进行处理。

```php
Serializable {
    /* 方法 */
    abstract public string serialize ( void )
    abstract public mixed unserialize ( string $serialized )
}
```

**代码示例**

```php
<?php

namespace InterfaceTest;

use Serializable;

class TestSerializable implements Serializable
{
    private $data;

    public function __construct()
    {
        $this->data = '我是数据.';
        echo "我构造了!";
    }

    public function serialize()
    {
        return serialize($this->data);
    }

    public function unserialize($serialized)
    {
        $this->data = unserialize($serialized);
    }

    public function getData()
    {
        return $this->data;
    }

    public function __destruct()
    {
        echo "我析构了!";
    }
}

$testSerializable = new \InterfaceTest\TestSerializable();
$test = serialize($testSerializable);
$testObj = unserialize($test);
echo $testObj->getData();
```

上面的代码输出 :

```
我构造了!初始化数据.我析构了!我析构了!
```

刚好解释了文档描述的内容 , 初始化时构造了 , 调用`serialize`时序列化调用对象的`serialize`方法 , 并且没有被析构 , 后面两次析构分别是第一次的`$testObj`的析构和后面`$testSerializable`的析构 .

更具文档中的描述 "如果需要执行标准的构造器，你应该在这个方法中进行处理。" , 这个方法是说的`unserialize()`方法 , 下面是一个多条数据的例子 .

```php
<?php

namespace InterfaceTest;

use Serializable;

class TestSerializable2 implements Serializable
{
    protected $data1;

    protected $data2;

    protected $data3;

    public function __construct($data1, $data2, $data3)
    {
        $this->data1 = $data1;
        $this->data2 = $data2;
        $this->data3 = $data3;
    }

    public function serialize()
    {
        return serialize([
            $this->data1,
            $this->data2,
            $this->data3,
        ]);
    }

    public function unserialize($serialized)
    {
        list($this->data1, $this->data2, $this->data3) = unserialize($serialized);
    }
}
```



