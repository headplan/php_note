# PHP数组的内部实现

* 实现数组使用了两个数据结构 , 一个是**HashTable** , 另一个是**bucket** . 
* **HashTable**结构体用于保存整个数组需要的基本信息 . 
* **Bucket**结构体用于保存具体的数据内容 . 

![](/assets/shuzuneibushix.png)

**什么是HashTable ?**

哈希表 , 是根据关键字\(Key value\)而直接访问在内存存储位置的数据结构 . 也就是说 , 它通过把键值通过一个函数的计算 , 映射到表中一个位置来访问记录 , 这加快了查找速度 . 这个映射函数称做哈希函数 , 存放记录的数组称做哈希表 .

* hash table , 又叫哈希表 , 散列表 , Hash表 . 
* 这种数据结构通过key-&gt;value的映射关系 , 使得普通的查找和插入、删除操作都可以在O\(1\) 的时间内完成 . 
* key-&gt;value的映射是通过Hash函数来实现的 . 

**PHP数组Hashtable结构体**

```
typedef struct _hashtable {
    uint nTableSize;        // hash Bucket的大小,最小为8,以2x增长,意思就是达到峰值后,再去申请一个空间不管超了多少
                            // 可以理解为初始化申请的元素个数,是2的n次方.
    uint nTableMask;        // 这是一个掩码,nTableSize-1,索引取值的优化,193491849 & 127,
                            // 这里nTableSize-1后得到的数字转化为二进制,所以的二进制位都是1,速度快
    uint nNumOfElements;    // hash Bucket中当前存在的元素个数,count()函数会直接返回此值,也说明count()的效率很高
    ulong nNextFreeElement; // 下一个数字索引的位置
    // 下面是三个指针
    Bucket *pInternalPointer;   // 当前遍历的指针foreach比for快的原因之一,reset,current遍历函数使用,也就是current()获取的值
    Bucket *pListHead;          // 存储数组头元素指针
    Bucket *pListTail;          // 存储数组尾元素指针
    Bucket **arBuckets;         // 存储hash数组,实际的存储容器
    unsigned char nApplyCount;  // 标记当前hash Bucket被递归访问的次数(防止多次递归),默认是3,防止自己引用自己,死循环
} HashTable;
```

**Bucket结构体**

```
typedef struct bucket {
    ulong h;            // 对char *key进行hash后的值,或者是用户指定的数字索引值,存储的是一个长整形,也就是数字索引.
    uint nKeyLength;    // hash关键字的长度,如果数组索引为数字,此值为0,这个就是关联索引.

    // 因为PHP的变量都是写时复制    
    void *pData;        // 指向value,一般是用户数据的副本,如果是指针数据,则指向pDataPtr
    void *pDataPtr;     // 如果是指针数据,此值会指向真正的value,同时上面pData会指向此值

    struct bucket *pListNext;   // 整个hash表的下一元素
    struct bucket *pListLast;   // 整个hash表该元素的上一个元素
    // 多个元素会添加内部指针,防止哈希碰撞,也就是两个不同的key,哈希成了相同的值,就有了下面的区分的指针
    struct bucket *pNext;       // 存放在同一个hash Bucket内的下一个元素
    struct bucket *pLast;       // 同一个hash bucket的上一个元素
    // 字符指针
    const char *arKey;          // 保存当前key所对应的字符串值
} Bucket;
```

key值如何通过哈希函数的计算 , 映射到Bucket上的 :

```
typedef struct _zend_hash_key {
    const char *arKey;
    uint nKeyLength;
    ulong h;
} zend_hash_key;
```

**数字索引**

* 直接使用h作为hash值。
* arKey=NULL 且nKeyLength=0

**字符串索引（关联数组）**

* h则是该字符串通过hash函数计算后的hash值。
* arKey保存字符串key, 
* nKeyLength保存该key的长度，

> 在PHP数组中如果索引字符串可以被转换成数字也会被转换成数字索引 .
>
> 所以在PHP中例如'10' , '11'这类的字符索引和数字索引10 , 11没有区别 .

**前面的结构体展示图 : **

![](/assets/shuzijiegouti.png)

![](/assets/shuzijiegouti2.png)

#### HashTable扩容

前面提到nTableSize初始化时是一个固定的值 , 但HashTable的大小并不是固定不变的 , 当nNumOfElements &gt; nTableSize时 , 会对HashTable进行扩容 , 以便于容纳更多的元素 . 前面也讲了 , 增长是2x的 . 扩容也即是对nTableSize这个值的应用 .

```
<?php
$a = array();
$p = (1 << 14) - 1; //每一次移动都表示"乘以 2",也就是2的14次方-1
$b = 1;
for ($i = 0; $i < $p; $i++) {
    $a[] = $b;
}
// 添加元素,打印内存
echo memory_get_usage(true) . "\n";
$a['as1'] = 1; // 此时是2的14次方
echo memory_get_usage(true) . "\n";
$a['as2'] = 1; // 此时是2的14次方+1
echo memory_get_usage(true) . "\n";

// 结果
2097152
2097152
4194304
```

从打印结果上看 , 前两次相同 , 第三次是2x增长 , 刚好符合了前面提到的2x扩容 . \(这里2的14次方是在我的电脑上nTableSize初始化申请的值 . 

**再举个例子 : prev\(\) 的指针实现**

HashTable结构体中 , 有一个成员pInternalPointer , 这个成员便是控制数组的访问指针的 . 

* 先找到数组的当前位置或指针
* 然后访问这个指针的pListLast找到上一个元素
* 由于访问指针已经移动到了适当的位置 , 则直接获取当前指针指向的元素 . 



