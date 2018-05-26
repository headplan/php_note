# 设计模式练习

> 代码示例查看本地Practice中的内容 .

#### 简单工厂模式

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



