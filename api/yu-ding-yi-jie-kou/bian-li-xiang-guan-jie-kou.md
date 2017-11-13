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

```
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



