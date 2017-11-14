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
```



