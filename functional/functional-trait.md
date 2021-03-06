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

在上面的艾玛示例中 , 隐藏的输入和输出我们还是很容易发现的 , 但是在一个面向对象的代码库中 , 你很快就会迷失 . 当然 , 这样做是没有问题的 , 但是 , 类似上面代码中的隐藏输入输出 , 即使我们能够一眼看出 , 它也会产生一些后果 :

* 增加认知负担 . 现在 , 你必须考虑在Session或Player类中发生了什么.
* 测试结果可能因相同的输入参数而异 , 因为软件的某些其他状态已更改 , 导致难以理解行为 . 
* 不清楚**函数签名**\[1\]或API让函数可以得到什么 , 因此需要阅读它们的代码或文档 . 

前面提到的两个函数的问题都是需要阅读和更新程序的现有状态来解决的 , 这些内容会在后面一一解答 , 现在我们需要了解是函数的特性 . 例如`nextMessage()`这个函数 , 熟悉依赖注入的朋友应该知道使用静态调用注入会话变量的实例可以避免其问题 , 当然这样做将解决隐藏的输入问题 , 但我们修改`$_SESSION`元变量的状态的事实仍然是一个副作用 .

了解函数特性的目的 , 也是为了发现不纯函数以及它们为什么是重要的 , 以及我们的主题函数编程和提高代码质量 .

后面会有更多的内容讨论隐藏输入的原因 , 以及隐藏输出的副作用等等 .

这里的副作用 , 还有一个相关的名词 , 叫**自由变量** . 维基百科中给出的定义是 :

> 在计算机编程中, 术语 "自由变量" 是指函数中使用的变量 , 它不是局部变量 , 也不是该函数的参数 .

在PHP中 , 通过use关键字到PHP闭包的变量 , 称为**自由变量** .

> \[1\] : 函数签名或方法签名 , 函数名称不能完全标识一个函数 , 因此我们用“函数签名\(**function signature**\)”来唯一标识一个函数 . 
>
> “函数签名”经过不同“编译器/链接器”的“名称修饰\(name decoration\)”得到不同的“修饰后名称\(decorated name\)” . 由于不同编译器采用不同的名字修饰方法 , 必然会导致由不同编译器产生的目标文件无法正常链接 , 这是导致不同编译器之间不能互操作的主要原因之一 .



