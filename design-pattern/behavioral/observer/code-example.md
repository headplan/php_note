# 代码示例

因为SPL已经为我们准备好了观察者模式的接口 , 这里完整的记录用到的和SPL相关的接口等 , SPL部分相关内容可以直接跳转这里 .

创建主体和客体\(观察者\)接口 :

这里直接继承SPL提供的接口 , 实现抽象类 , 并对监听者操作"订阅/退订"的存储方式提供了几个选择 .

* Subject.php - 这里存储观察者到一个数组\(使用spl\_object\_hash\(\)函数\)或者SplObjectStorage对象

```php
<?php

namespace Behavioral\Observer;

use SplSubject;
use SplObserver as Observer;
use SplObjectStorage;

abstract class Subject implements SplSubject
{
    /**
     * 存储观察者
     *
     * @var array
     */
    protected $observers;

    public function __construct()
    {
        //$this->observers = [];
        $this->observers = new SplObjectStorage();
    }

    /**
     * 添加(注册)一个观察者
     *
     * @param \SplObserver $observer
     */
    public function attach(Observer $observer)
    {
        //$this->observers[spl_object_hash($observer)] = $observer;
        $this->observers->attach($observer);
    }

    /**
     * 删除一个观察者
     *
     * @param \SplObserver $observer
     */
    public function detach(Observer $observer)
    {
        //unset($this->observers[spl_object_hash($observer)]);
        $this->observers->detach($observer);
    }

    /**
     * 消息通知:当状态发生改变时,通知所有观察者
     */
    abstract public function notify();
}
```

* Observer.php - SPL的接口无法传递更多的参数 , 这里可以使用func\_get\_arg去接参数 , 或者添加一个方法 . 

```php
<?php

namespace Behavioral\Observer;

use SplObserver;
use SplSubject as Subject;

abstract class Observer implements SplObserver
{
    /**
     * 在目标发生改变时接收目标发送的通知;
     * 当关注的目标调用其notify()方法时被调用;
     *
     * @param \SplSubject $subject
     */
    public function update(Subject $subject)
    {
        $this->doUpdate($subject);
    }

    abstract public function doUpdate(Subject $subject);
} 
```

其他示例查看本地代码 .

---



