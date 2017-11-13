# 遍历相关接口

#### Traversable遍历接口

检测一个类是否可以使用foreach进行遍历的接口 . 这是一个无法在 PHP 脚本中实现的内部引擎接口 , 实际编程中我们使用Iterator接口或者IteratorAggregate接口来实现遍历 . 所以这个接口没有任何方法 , 它的作用仅仅是作为所有可遍历类的基本接口 . 

```
Traversable {  
}
```

Traversable 重要的一个用处就是判断一个类是否可以遍历 , 官方给了例子 : 

```
<?php
    if( !is_array( $items ) && !$items instanceof Traversable )
        //Throw exception here
?>
```

注意 , 数组和对象可以通过foreach遍历 , 但它们没有实现Traversable接口 , 所以不是Traversable , 官方也给出了一个例子 : 

```
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

