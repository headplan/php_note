# Pure functions

#### 纯函数

假设有一个函数签名函数`getCurrentTvProgram(Channel $channel)` , 这个函数没有指示任何的纯度 , 也不知道这个函数背后可能隐藏的复杂性 .

你可能会得到实际在给定频道上播放的程序 . 你不知道的是 , 如果你登录到系统中 , 该函数是否检查过 . 也许为了分析 , 有某种数据库更新了数据 . 可能该函数还将返回一个异常 , 因为日志文件处于只读状态 . 你不能确切地知道 , 所有上述的问题或副作用 . 那么对于与这些隐藏依赖项相关的所有复杂性 , 你将面临三选项 :

* 深入到文档或代码中 , 了解所发生的一切
* 让依赖关系更加的明显
* 什么也不做 , 求佛 ...

最后一项纯属凑选项 , 第一个选择可能是最好的 , 但是你的同事其他所有人都要和你一样深入文档 , 看一遍 . 答案显而易见 ...

#### 关于封装

封装是为了隐藏实现细节 . 纯度是为了使隐藏的依赖性已知 . 两者都是有用的, 更好的做法是让他们没有任何形式的冲突 . 如果你足够细心 , 那么你就可以实现这两个目标 , 哈哈哈 . 他们喜欢干净 , 简单的解决方案 . 

用简单的术语解释 : 

* 封装是关于隐藏内部实现
* 避免副作用的原因是让外部输入已知
* 避免副作用的结果是使外部变化已知

#### 发现副作用原因

再来看看`getCurrentTvProgram(Channel $channel)`函数 , 下面的实现不是Pure的 , 问题出在哪呢? 根据现在我们知道的Pure函数的知识 : 当使用相同的参数调用时 , 它们总是返回相同的结果 . 

```php
<?php
function getCurrentTvProgram(Channel $channel): string
{
    // 假设这里的getProgramAt()是一个Pure方法.
    return $channel->getProgramAt(time());
}
```

找到了?对 , 就是time\(\)函数 , 让不同时间调用返回的结果不同了 , 接下来改进一下 : 

```php
<?php
functiongetTvProgram(Channel $channel, int $when): string
{
    return $channel->getProgramAt($when);
}
```

OK , 现在我们的函数已经Pure了 , 而且现在我们可以按名称去更改一天中的任何时间 , 还可以不用改变当前的时间去测试函数 . 

下面的例子也是一个隐藏输入带来的副作用 , 看看他们是为啥不纯的 : 

```php
<?php
$counter = 0;
function increment()
{
    global $counter;
    return ++$counter;
}

function increment2()
{
    static $counter = 0;
    return ++$counter;
}

function get_administrators(EntityManager $em)
{
    // Let's assume $em is a Doctrine EntityManager allowing
    // to perform DB queries
    return $em->createQueryBuilder()
    ->select('u')
    ->from('User', 'u')
    ->where('u.admin = 1')
    ->getQuery()->getArrayResult();
}

function get_roles(User $u)
{
    return array_merge($u->getRoles(), $u->getGroup()->getRoles());
}
```

* 第一个例子 , global让其使用全局范围中的一些变量 , PHP的范围规则也告诉我们 , 所有使用global的函数 , 都可能不纯洁 . 

* 第二个示例中的静态关键字是一个很好的指示器 , 我们可以尝试在函数调用之间存储状态 . 在本例中 , 它是在每次运行时递增的计数器 . 这个函数显然是不纯的 . 但是 , 与全局变量相反 , 静态关键字的使用可能只是在调用之间缓存数据的一种方式 , 因此必须在得出结论之前检查使用它的原因 . 

#### 发现副作用结果

关于对象方法

结束语

