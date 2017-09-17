# 函数式特性

> ##### 学习纯函数,函数引用的透明性,函数的不可变性三个基本概念 .

讨论主题 :

* 隐藏的输入与输出
* 函数的纯度
* 不变性
* 引用透明

首先 , 我们从输入与输出开始 . 

#### 两种输入与输出

看下面这个简单的函数 : 

```php
<?php
function add(int $a, int $b): int
{
    return $a + $b;
}
```

这个函数的输入和输出很容易看出来 , 即有两个参数和一个返回值。我们可以毫不怀疑地说 , 这是个Pure函数 . 

这是我们常见的 , 也很容易看懂的第一种输入与输出 . 

接下来 , 看看下面两个函数 : 

```php
<?php
function nextMessage(): string
{
    return array_pop($_SESSION['message']);
}
// 一个简单的游戏得分更新的函数
function updateScore(Player $player, int $points)
{
    $score = $player->getScore();
    $player->setScore($score + $points);
}
```

第一个函数没有明显的输入 . 但是很明显可以看出 , 我们是从`$_SESSION`变量中得到一些数据来创建输出值的 , 所以我们有一个隐藏的输入 . 而且 , 在`$_SESSION`中也存在这一个隐藏的副作用 , 因为`array_pop`方法会删除我们刚从消息列表中得到的消息 . 

再来看第二个函数 , 它没有明显的输出 , `return`都没有 , 而且更新`$player`的分数显然的也是一个副作用 , 此外 , 从`$player`得到的`$score`也可以视为这个函数的第二个输入 . 

