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



