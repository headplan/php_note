# 递归

在学术意义上, 递归是将问题划分为更小的概念相同问题的实例。例如, 如果您需要以递归方式扫描目录, 则首先扫描起始目录, 然后扫描其子项和子项的子级。大多数编程语言通过允许函数调用自身来支持递归。这个想法通常被描述为递归。

让我们看看如何使用递归来扫描目录 : 

```php
<?php
function searchDirectory($dir, $accumulator = [])
{
    foreach (scandir($dir) as $path) {
    // Ignore hidden files, current directory and parent directory
    	if(strpos($path, '.') === 0) {
	    continue;
	}

	$fullPath = $dir.DIRECTORY_SEPARATOR.$path;
		
	if (is_dir($fullPath)) {
	    $accumulator = searchDirectory($path, $accumulator);
	} else {
	    $accumulator[] = $fullPath;
	}
    }
	
    return $accumulator;
}
```

我们首先使用 scandir 函数来获取所有文件和目录。然后, 如果我们遇到一个子目录, 我们再次调用它的函数。否则, 我们只需将文件添加到累加器中。此函数是递归的, 因为它调用自身。

您可以使用控制结构来编写这篇文章, 但是由于您事先不知道文件夹层次结构的深度, 代码可能会变得更加杂乱和难以理解。

一些书籍和教程使用斐波那契数列, 或计算一个阶乘作为递归例子, 但公平地讲, 这些是相当差的, 因为他们更好地实现使用传统的循环第二, 并提前计算的条件第一。

相反, 让我们把我们的头环绕在一个更有趣的挑战, 汉诺塔。对于那些不知道这款游戏的人来说, 传统的版本有三根不同大小的圆盘堆叠在一起, 最小的在顶部。在游戏开始的时候, 所有的棋子都在最左边的标尺上, 目标是把它们带到最右边的一个。游戏遵循以下规则 : 

* 一次只能移动一个光盘
* 只有杆的最顶端的圆盘可以移动
* 不能将光盘放在较小光盘的顶部

如果我们想解决这个游戏, 更大的光盘必须放在最后一杆。为了做到这一点, 我们需要首先移动所有其他光盘到中间杆。遵循这一思路, 我们可以得出三大步骤, 我们必须实现:

* 移动所有光盘, 但更大的一个到中间。
* 将大光盘向右移动。
* 将所有光盘移到大的上面。

步骤1和3是初始问题的较小版本。这些步骤中的每一个都可以减少到一个较小的版本, 直到我们只有一个磁盘移动-一个递归函数的完美情况。让我们尝试实现 . 

为了避免扰乱我们的功能与有关的杆和光盘的变量, 我们将假设计算机将发号施令的人作出的举动。在我们的代码中, 我们还将假定最大的光盘是数字 1, 较小的光盘有较大的数字:

```php
<?php
function hanoi(int $disc, string $source, string $destination, string $via)
{
    if ($disc === 1) {
        echo("Move a disc from the $source rod to the $destination rod\n");
    } else {
        // step 1 : move all discs but the first to the "via" rod
        hanoi($disc - 1, $source, $via, $destination);
        // step 2 : move the last disc to the destination
        hanoi(1, $source, $destination, $via);
        // step 3 : move the discs from the "via" rod to the destination
        hanoi($disc - 1, $via, $destination, $source);
    }
}
```

我们使用函数`hanoi(3, 'left', 'right', 'middle')`输入三光盘, 我们得到以下输出:

* 将光盘从左杆移动到右杆
* 将圆盘从左杆移动到中间杆
* 将圆盘从右杆移动到中间杆
* 将光盘从左杆移动到右杆
* 将圆盘从中间杆移动到左杆
* 将圆盘从中间杆移动到右杆
* 将光盘从左杆移动到右杆

在递归方面需要一段时间，而不是使用更传统的循环，并且明显的递归不是一个更好的所有问题您想要解决的银弹。

某些功能语言根本没有循环结构, 这迫使您使用递归。这不是 PHP 的情况, 因此, 让我们使用正确的工具的工作。如果你能把这个问题看成是一个较小的相似问题的组合, 通常会很容易使用递归。例如, 试图找到一个迭代解决方案的汉诺塔需要大量的仔细思考。或者, 您可以尝试重写目录扫描功能, 只使用循环来说服自己。

递归有用的其他一些方面有 :

* 为具有多个级别的菜单生成数据结构
* 遍历 XMLdocument
* 呈现一系列可能包含子组件的 CMS 组件

一个很好的经验法则是, 当你的数据有一个树状结构, 有一个根节点和一个孩子时, 尝试递归。

虽然通常更容易阅读, 一旦你已经得到了它的控制, 递归来与内存成本。在大多数应用程序中, 您不应该遇到任何困难, 但我们将在10章、PHP 框架和 FP 中进一步讨论这个主题, 并提出一些避免这些问题的方法。

