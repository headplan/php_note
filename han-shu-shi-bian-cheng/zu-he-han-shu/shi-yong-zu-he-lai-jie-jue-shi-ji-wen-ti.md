# 使用组合来解决实际问题

举个例子, 假设你的老板进来了, 希望你能用过去30天登记的所有用户的电话号码生成一个新的报告 . 我们假定我们有以下类代表我们的用户 . 显然 , 一个真正的类将存储和返回真实的数据 , 但让我们只定义我们的 API :

```php
<?php
class User
{
    public function phone(): string
    {
        return '';
    }

    public function registration_date(): DateTime
    {
        return new DateTime();
    }
}

$users = [new User(), new User(), new User()]; // etc.
```

如果没有任何关于函数编程的知识 , 您可以编写如下内容 :

```php
<?php
class User
{
    public function phone(): string
    {
        return '';
    }

    public function registration_date(): DateTime
    {
        return new DateTime();
    }
}

$users = [new User(), new User(), new User()]; // etc.
```

首先看看我们的函数告诉我们 , 它不是纯的 , 因为在函数内部计算了“limit” , 因此后续的调用可能会导致不同的用户列表 . 我们还可以利用“map”和“filter”函数 :

```php
<?php

function getUserPhonesFromDate($limit, $users)
{
    return array_map(function(User $u) {
        return $u->phone();
    }, array_filter($users, function(User $u) use($limit) {
        return $u->registration_date()->getTimestamp() > $limit;
    }));
}
```

根据您的喜好 , 代码现在可能会更容易阅读 , 或者根本没有 , 但至少我们有一个纯函数 , 我们的关注点更分离 . 不过, 我们可以做得更好 . 首先 , "function-php"库有一个函数可以让我们创建一个调用一个对象的方法的帮助器 :

```php
<?php
use function Functional\map;
use function Functional\filter;
use function Functional\partial_method;

function getUserPhonesFromDate2($limit, $users)
{
    return map(filter(function(User $u) use($limit) {
            return $u->registration_date()->getTimestamp()->$limit;
        }, $users),partial_method('phone')
    );
}
```

这个函数能好一些 , 但是如果我们接受必须创建一些新的帮助函数 , 我们可以更好地改进解决方案 . 此外 , 这些帮助函数是新的构建块, 我们将能够重用 :

```
<?php
function greater($limit) {
return function($a) {
return $a > $limit;
};
}
function getUserPhonesFromDate3($limit, $users)
{
return map(
filter(compose(
partial_method('registration_date'),
partial_method('getTimestamp'),
greater($limit)
),
164
$users),
partial_method('phone')
);
}
```

如果我们有 curryied 版本的 "filter" 和 "map" 函数, 我们甚至可以创建一个函数, 它只需要一个日期并返回一个新的函数, 可以进一步组合和重用 :

```
<?php
use function Functional\partial_right;
$filter = curry('filter');
$map = function($cb) {
return function($data) use($cb) {
return map($data, $cb);
};
};
function getPhonesFromDate($limit)
{
return function($data) use($limit) {
$function = compose(
$filter(compose(
partial_method('getTimestamp'),
partial_method('registration_date'),
greater($limit)
)),
$map(partial_method('phone'))
);
return $function($data);
};
}
```

这里提醒一下 , 一个良好的参数顺序是必要的 , 因为"functional-php"库中的map函数与PHP中的原始函数具有相同的签名 , 所以我们必须手动“curry”一下 .

我们编写的函数看起来比最初的命令更长一点 , 但是在我看来 , 它更容易阅读 . 您可以轻松地跟踪正在发生的事情 :

1. 使用以下方法筛选数据 :

2. 注册日期

3. 到的时间戳

4. 检查是否大于给定限制

5. 将电话方法映射到结果

如果你发现这个名称partial\_method不是理想的 , 并且对组合功能的调用看起来有点麻烦 , 我完全同意 . 我们可以在假设有一种语言 , 有一个组合运算符 , 可以自动currying , 提供了一些语法糖来来延迟一个方法的调用 , 例如可能是这样的 :

```
getFromDate($limit) = filter(
    (->registration_date) >>
    (->getTimestamp) >>
    (> $limit)
) >> map(->phone)
```

既然我们有了自己的函数 , 你的老板就会带着新的需求回到你的办公室 . 事实上 , 他只想从最近30天的三次最新登记 . 简单点 , 让我们用更多的构造块组成新函数 :

```php
<?php
use function Functional\sort;
use function Functional\compare_on;
function take(int $count) {
return function($array) use($count) {
return array_slice($array, 0, $count);
};
};
function compare($a, $b) {
return $a == $b ? 0 : $a < $b ? -1 : 1;
}
function getAtMostThreeFromDate($limit)
{
return function($data) use($limit) {
$function = compose(
partial_right(
'sort',
compare_on('compare',
partial_method('registration_date'))
),
take(3),
getPhonesFromDate($limit)
);
return $function($data);
};
}
```

为了从数组的开始处取一定数量的项, 我们需要在 "array\_slice" 函数周围创建一个 "take" 函数 . 我们还需要一个函数来比较值, 因为 "DateTime" 函数重载 "比较" 运算符, 所以我们可以这样做 .

再次, "functional-php" 库得到的排序函数错误的参数顺序 , 所以我们需要部分应用, 而不是 ' curry ' . "compare\_on" 函数创建了一个比较函数和一个 "reducer", 在每个项目被比较时被调用 . 在我们的例子中, 我们要比较注册日期, 所以我们重用我们不同的方法应用 . 

我们需要在过滤之前执行排序操作 , 因为我们的getPhonesFromDate方法只返回电话号码 . 我们所产生的功能本身就是其他功能的“curryied”组合 , 从而允许轻松重用 . 

我希望这个小小的例子 , 已经说服你使用小功能作为构建块并组合它们 , 来解决问题的力量 . 

最后一点 , 就像您可能从示例中收集到的那样 , PHP 很遗憾地遗漏了许多实用程序函数, 从而使也让我们可以自由的去编写和函数式相关的代码 .  不过 , 即使是广泛使用的functional-php库 , 也会导致一些参数命令错误 , 并且缺少一些重要的代码片段 , 例如currying . 

