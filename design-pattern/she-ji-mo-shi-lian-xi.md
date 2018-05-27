# 设计模式练习

> 代码示例查看本地Practice中的内容 .

#### 简单工厂

以初期的电商网站为例 , 现在有几大分类 , 例如 : 图书 , 酒 , 水果 . 我们创建三个类.

```php
Books.php
Wines.php
Fruits.php
```

他们分别返回一个所有产品的清单 , 叫getList\(\) :

```php
public function getList()
{
    return [
        ["goods_id"=>201, "goods_name"=>"苹果"],
        ["goods_id"=>202, "goods_name"=>"橘子"],
        ["goods_id"=>203, "goods_name"=>"香蕉"],
        ["goods_id"=>204, "goods_name"=>"葡萄"],
        ["goods_id"=>205, "goods_name"=>"大鸭梨"],
    ];
}
```

接下来创建工厂 , 让前面的所有商品类的取出实例化 , 都交给工厂来解决 :

```php
<?php

namespace Practice;

class GoodsFactory
{
    public static function getGoods($tpye)
    {
        $object = false;
        switch ($tpye)
        {
            case 'Books':
                $object = new Books();
                break;
            case 'Fruits':
                $object = new Fruits();
                break;
            case 'Wines':
                $object = new Wines();
                break;
        }
        return $object;
    }
}
```

#### 实现注册树 , 也可以叫数据中心

现在 , 要在图书的分类中 , 还要取出水果和酒的数据 , 根据上面的工厂 , 我们需要这样写 :

```
use Practice\GoodsFactory;

$object = GoodsFactory::getGoods('Books');
$object2 = GoodsFactory::getGoods('Fruits');
$object3 = GoodsFactory::getGoods('Wines');

$result_books = $object->getList();
$result_fruits = $object2->getList();
$result_wines = $object3->getList();
```

可以发现 , 前面我们没有约束的接口或者抽象类 , 但获取数据都调用了getList\(\)方法 , 现在 ,我们先定义个接口 , 规定getList是必须存在的 , 并在工厂中约束 .

```php
<?php

namespace Practice;

interface GInerface
{
    public function getList();
}
```

接下来就可以创建中心了 :

```php
<?php

namespace Practice;

class GoodsDataCenter
{
    public static $object = [];

    public static function set($key, $value)
    {
        self::$object[$key] = $value;
    }

    // 这里可以写一个get方法来获取,但是还是没有解决要写很多tpye,然后获取对象的问题
    public static function get($key)
    {
        return self::$objects[$key];
    }

    // 还可以不冲一个unset方法
    public static function unset($key)
    {
        self::unset(self::$objects[$key]);
    }

    // 使用魔术方法__callStatic去调用getList方法
    public static function __callStatic($methodName, $methodArgs)
    {
        $result = [];

        foreach (self::$objects as $object) {
            if (method_exists($object, $methodName)) {
                $r = $object->$methodName($methodArgs);
                if ($r) {
                    $result[] = $r;
                }
            }
        }

        return $result;
    }  

}
```

然后把工厂中最后返回的数据 , 塞入到GoodsDataCenter中 :

```php
if ($object instanceof GInerface) {
    GoodsDataCenter::set($tpye, $object);
}
```

改造的差不多了 , 现在可以直接在GoodsFactory中注册 , 然后调用数据中心 , 直接获取要所有注册的数据 .

```php
GoodsFactory::getGoods('Books');
GoodsFactory::getGoods('Fruits');

var_export(GoodsDataCenter::getList());
```

这里再简单的修改一下工厂方法 , 让其可以使用可变参数 :

```php
public static function getGoods($type)
{
    $argc = func_num_args();
    if ($argc > 1) {
        $type = func_get_args();
    }
    if (is_string($type)) {
        self::getGoodsType($type);
    } elseif (is_array($type)) {
        foreach ($type as $value) {
            self::getGoodsType($value);
        }
    }
}
```

```php
GoodsFactory::getGoods(['Books', 'Fruits']);

var_export(GoodsDataCenter::getList());
```

#### 模板化对象 , 抽象管理

现在 , 我们想获取一个实体 , 也就是前面Books,Fruits等的详情页面的信息 , 即一条数据的详细信息 , 首先想到的 , 就是在Books等类中新建一个方法 , 来获取数据 . 参数是商品的ID , 其实就是$methodName\($methodArgs\);的参数值 , 因为使用的是\_\_callStatic\(\)方法 , 这里的$methodArgs参数默认是一个数组 , 如果只是单条数据 , 那参数就是$methodArgs\[0\] .

```php
public function getDetails($goods_id)
{
    // 查询返回单条数据
    // select($goods_id[0])
    return ["goods_id"=>104, "goods_name"=>"python不学即会"];
}
```

这里 , 我们想要的不仅仅是一条数据 , 或许还有一些其他简单的操作 , 比如获取统计数,获取点击量,再打一个log等 , 当然没有太过复杂的逻辑 , 但如果这些操作都是分开的 , 我们就需要一个实际的对象来管理这些操作 , 这里使用PHP7的匿名类来实现 :

```php
public function getDetails($goods_id)
{
    return new class($goods_id[0])
    {
        public function __construct($goods_id)
        {
            if ($goods_id == 104) {
                $data = ["goods_id"=>104, "goods_name"=>"python不学即会"];
            }
            foreach ($data as $k => $v) {
                $this->$k = $v;
            }
        }
    };
}
```

基本的获取对象已经完成 , 现在要添加打日志 , 获取点击量等操作 , 对这些同时获取的操作进行管理 , 我们就需要抽象出一个管理这些操作的加载骨架 , 这里新建一个公共类 , 而且是抽象的 :

```php
<?php

namespace Practice;

abstract class GoodsDetailsLoad
{
    public function loadData($goods_id, $type)
    {
        $this->setClick($goods_id);
        $this->setLog($goods_id);
        $this->setCom();

        return new class($goods_id, $type)
        {
            public function __construct($goods_id, $type)
            {
                $data = [];
                if ($goods_id == 104 && $type == 'Books') {
                    $data = ["goods_id"=>104, "goods_name"=>"python不学即会"];
                }
                foreach ($data as $k => $v) {
                    $this->$k = $v;
                }
            }
        };
    }

    abstract public function setClick($id);
    abstract public function setLog($id);

    public function setCom()
    {
        echo '公共方法';
    }
}
```

这里抽象了点击数和日志的抽象 , 并在loadData中加载 . 现在就可以在Books中继承 , 然后完成各自的内容 . 同时进行了加载 . 也方便管理通用以及自定义的方法 . 

```
public function getDetails($goods_id)
{
    return parent::loadData($goods_id[0], 'Books');
}
```

这里数据部分都写死了 , 没有判断多余部分是否存在 , 这里只考虑抽象管理的应用 . 其他略...

