# 数组的遍历

* for : 语句循环遍历 
* foreach : 循环遍历 
* while : \(list\($key, $val\) = each\($fruit\)\) 
* array\_walk、array\_map : 回调遍历
* current和next : 内部指针遍历

array\_walk 引用的方式对数组进行遍历 , 返回值不重要 , 目的是直接改变掉数组 .

```
array_walk(array &$array, callable $callback [, mixed $userdata = NULL ])
```

array\_map 为了改变数组的数据 , 支持多个数组数据合并 , 目的是返回新的数组 .

```
array_map(callable $callback, array $array1 [, array $... ])
```

walk和map的回调函数位置也不一样 .

**foreach 和 for 性能对比**

通过运行示例来体验性能的不同 , 一般php推荐使用foreach来遍历数组 .

**foreach遍历中的顺序**

```
$a = array();
$a[2] = 3;
$a[1] = 2;
$a[0] = 1;
foreach ($a as $v) {
    echo $v;
}

# 输出的不是按照数组key顺序遍历的,而是2,1,0输出的.
```

**foreach遍历中的引用**

```
下面代码的执行结果是？
$arr = array(1,2,3);
foreach($arr as &$v){}
foreach($arr as $v){
  echo $v;
}
// 结果
122
```

```
第一次 foreach( $arr as $k => &$v)
循环1：$v = &$arr[0] = 1;
循环2：$v = &$arr[1] = 2;
循环3：$v = &$arr[2] = 3;
循环4：$v = &$arr[3] = 4;
第二次 foreach( $arr as $k => $v)
循环1：$v = &$arr[3] = arr[0] = 1; 此时 $arr[3] = 1
循环2：$v = &$arr[3] = arr[1] = 2; 此时 $arr[3] = 2
循环3：$v = &$arr[3] = arr[2] = 3; 此时 $arr[3] = 3
循环4：$v = &$arr[3] = arr[3] = 3;
此时 $arr = array( 'a'=>1,'b'=>2,'c'=>3,'d'=>3);
```

```
<?php
$a = array(1,2,3);
foreach($a as &$v) {$v *= $v;}
var_dump($a);
foreach($a as $v){
    echo $v;
}
// 结果
array(3) {
  [0]=>
  int(1)
  [1]=>
  int(4)
  [2]=>
  &int(9)
}
144

# 第一步中留下一个引用变量没有unset,将数组最后一个变量变成引用.
```

看两个图片 , 解释上面例子的过程 :

![](/assets/shuzubinali1.png)![](/assets/shuzubianli2.png)

**如何杜绝foreach的引用隐患?**

因为引用地址一致存在 , 所以要unset\(\) .

```
// 方法一
foreach($a as $k=>&$v){
…
unset($v);
}

// 方法二
foreach($a as $k=>&$v){
    …
}
unset($v)
```

**foreach引用的好处**

节省时间、空间



