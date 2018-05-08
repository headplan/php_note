# 数组与数据结构

#### 链表

链表能存储多个数据元素 , 他就像一条线一样 , 把所有存储的数据元素连在一起 , 所以也称作是线性的数据结构 .

**单链表**

![](/assets/array_danlianbiao.png)

**双链表**

![](/assets/array_shuanglianbiao.png)

**循环链表**

![](/assets/array_xunhuanlianbiao.png)

#### 哈希表

散列表\(Hash table , 也叫哈希表\) , 是根据关键字\(Key value\)而直接访问在内存存储位置的一种数据结构 .

类似一个本新华字典 .

#### 栈

栈作为一种数据结构，是一种只能在一端进行插入和删除操作的特殊线性表。它按照后进先出的原则存储数据\(LIFO, Last In First Out , 先入后出\) .

```php
<?php

$arr = [1,2,3,4];

array_push($arr, 5);
array_push($arr, 6);

print_r($arr);

array_pop($arr);

print_r($arr);

// 结果
Array
(
    [0] => 1
    [1] => 2
    [2] => 3
    [3] => 4
    [4] => 5
    [5] => 6
)
Array
(
    [0] => 1
    [1] => 2
    [2] => 3
    [3] => 4
    [4] => 5
)
```

#### 队列

```php
<?php

$arr = [1,2,3,4];

array_push($arr, 5);
array_push($arr, 6);

print_r($arr);

array_shift($arr);
print_r($arr);
array_shift($arr);
print_r($arr);

// 结果
Array
(
    [0] => 1
    [1] => 2
    [2] => 3
    [3] => 4
    [4] => 5
    [5] => 6
)
Array
(
    [0] => 2
    [1] => 3
    [2] => 4
    [3] => 5
    [4] => 6
)
Array
(
    [0] => 3
    [1] => 4
    [2] => 5
    [3] => 6
)
```

队列就是是先进先出\(FIFO, First-In-First-Out\)的线性表 . 队列只允许在后端\(称为rear\)进行插入操作 , 在前端\(称为front\)进行删除操作 .

#### 约瑟夫环问题



