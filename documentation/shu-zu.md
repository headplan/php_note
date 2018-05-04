# 数组

#### 数组的定义

```
$items = array();
$items = [];
$items = ['abc']; # 直接初始化
# 使用对象定义数组,前提是对象实现了PHP自带的ArrayAccess接口,查看预定义接口部分.
$obj = new Obj;
$obj[] = 'a';
$obj[] = 'b';
$obj[] = 'c';
print_r($obj);
```

#### 数组的key和value的限制条件

意思是什么类型的值可以当做数组的key , 数组的value值又可以存什么类型 . 



