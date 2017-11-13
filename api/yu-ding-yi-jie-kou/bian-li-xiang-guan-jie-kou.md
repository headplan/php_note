# 遍历相关接口

#### Traversable遍历接口

检测一个类是否可以使用foreach进行遍历的接口 . 这是一个无法在 PHP 脚本中实现的内部引擎接口 , 实际编程中我们使用Iterator接口或者IteratorAggregate接口来实现遍历 . 所以这个接口没有任何方法 , 它的作用仅仅是作为所有可遍历类的基本接口 .

```
Traversable {  
}
```

Traversable 重要的一个用处就是判断一个类是否可以遍历 , 官方给了例子 :

```php
<?php
    if( !is_array( $items ) && !$items instanceof Traversable )
        //Throw exception here
?>
```

注意 , 数组和对象可以通过foreach遍历 , 但它们没有实现Traversable接口 , 所以不是Traversable , 官方也给出了一个例子 :

```php
$myarray = array('one', 'two', 'three');
$myobj = (object)$myarray;

if ( !($myarray instanceof \Traversable) ) {
    print "myarray is NOT Traversable";
}
if ( !($myobj instanceof \Traversable) ) {
    print "myobj is NOT Traversable";
}

foreach ($myarray as $value) {
    print $value;
}
foreach ($myobj as $value) {
    print $value;
}
```

意思是 , 所有对象都可以使用 foreach 进行迭代 , 并且遍历每个属性 . Traversable的目的只是告诉你类是否实现了迭代器Iterator接口或者IteratorAggregate接口 , 用来告诉你一些这里有自定义的一些东西 .

PHP7以后 , iterable伪类型将匹配遍历和数组 , 因为有了类型提示type-hinting . 还可以使用一个新的函数去判断 :

```php
if ( !is_iterable( $items ))
    //Throw exception here
```

函数直接验证变量的内容是否被 iterable pseudo-type 接受 , 即判断它是一个数组或实现Traversable遍历的对象 .

#### Iterator迭代器接口

可在内部迭代自己的外部迭代器或类的接口 . 意思就是Iterator接口允许对象以自己的方式迭代内部的数据 , 从而使它可以被循环访问 .

```php
Iterator extends Traversable {  
    //返回当前索引游标指向的元素  
    abstract public mixed current ( void )  
    //返回当前索引游标指向的键  
    abstract public scalar key ( void )  
    //移动当前索引游标到下一元素  
    abstract public void next ( void )  
    //重置索引游标  
    abstract public void rewind ( void )  
    //判断当前索引游标指向的元素是否有效  
    abstract public boolean valid ( void )  
}
```

**代码示例**

```php
<?php

namespace Iterable;

use Iterator;

class TestIterator implements Iterator
{
    # 迭代数据
    private $_var = ['apple', 'banana', 'orange'];
    # 索引游标起始位置
    private $_key = 0;

    /**
     * TestIterator初始化
     */
    public function __construct()
    {
        var_dump("执行".__METHOD__);
        $this->_key = 0;
    }

    /**
     * 返回当前索引游标指向的元素(值)
     */
    public function current()
    {
        var_dump("执行".__METHOD__);
        return $this->_var[$this->_key];
    }

    /**
     * 返回当前索引游标指向的键
     */
    public function key()
    {
        var_dump("执行".__METHOD__);
        return $this->_key;
    }

    /**
     * 移动当前索引游标到下一元素
     */
    public function next()
    {
        var_dump("执行".__METHOD__);
        ++$this->_key;
    }

    /**
     * 索引游标指向初始位置
     */
    public function rewind()
    {
        var_dump("执行".__METHOD__);
        $this->_key = 0;
    }

    /**
     * 判断当前索引游标指向的元素是否有效
     */
    public function valid()
    {
        var_dump("执行".__METHOD__);
        return isset($this->_var[$this->_key]);
    }
}
```

PHP 已经提供了一些用于日常任务的迭代器 , 可以查看SPLd迭代器 , 这部分另开一篇记录 , 下面继续说和Traversable相关的类 .

#### IteratorAggregate聚合式迭代器接口

它提供了创建外部迭代器的接口 . 意思是对一个非迭代器对象 , 用函数getIterator返回迭代器 .

```php
IteratorAggregate extends Traversable {
    //获取一个外部迭代器  
    abstract public Traversable getIterator ( void )  
}
```

实现getIterator方法时必须返回一个实现了Iterator接口的类的实例 . 下面来看看代码示例 :

```php
<?php

namespace Iterable;

use IteratorAggregate;
use ArrayIterator;

class TestIteratorAggregate implements IteratorAggregate
{
    public $_var1 = '小明';
    public $_var2 = '小莉';
    public $_var3 = '小红';

    public function __construct()
    {
        $this->_var4 = '小刚';
    }

    /**
     * 获取一个外部迭代器
     * @return \ArrayIterator
     */
    public function getIterator()
    {
        return new ArrayIterator($this);
    }
}
```

这里的ArrayIterator迭代器会把对象或数组封装为一个可以通过foreach来操作的类 . ArrayIterator就是SPL的迭代器 . 再来看一个例子:

```php
<?php

namespace Iterable;

use IteratorAggregate;
use ArrayIterator;

class TestIteratorAggregate2 implements IteratorAggregate
{
    # 迭代数据
    private $_var = [];

    # 索引类型
    const TYPE_INDEX = 1;

    # 关联类型
    const TYPE_ASSOC = 2;

    /**
     * TestIteratorAggregate2 constructor.
     *
     * @param array $var
     * @param int $type
     */
    public function __construct(array $var, $type = self::TYPE_INDEX)
    {
        $this->setVar($var,$type);
    }

    /**
     * @param array $var
     * @param $type
     */
    public function setVar(array $var, $type)
    {
        reset($var);
        while (list($k, $v) = each($var)) {
            $type == self::TYPE_INDEX ? $this->_var[] = $v : $this->_var[$k] = $v;
        }
    }

    /**
     * @return \ArrayIterator
     */
    public function getIterator()
    {
        return new ArrayIterator($this->_var);
    }
}
```

其实 , 我们也可以使用前面我们自己实现的TestIterator迭代器去处理迭代方式 . 简单修改即可 . 查看本地代码示例 . 

```

```



