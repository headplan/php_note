# 向左和向右折叠

功能语言通常实现两个版本的折叠\(fold\) , foldl 和 foldr . 不同的是 , 第一个是从左边和第二个是从右边折叠 . 

例如, 如果您有数组 \[1、2、3、4、5\], 并且要计算其总和, 你可以有任何一个 \(\(1 + 2\) + 3\) + 4\) + 5 或 \(\(5 + 4\) + 3\) + 2+ 1 . 如果有初始值, 它将始终是计算中使用的第一个值 . 

如果要应用于值的操作是可交换的, 则左和右变量都将产生相同的结果。交换操作的概念来自于数学, 在7章, 功能技术和主题中有解释。

对于允许无限列表的语言, 例如, 根据列表的生成方式, 两个折叠中的一个可以计算一个值并停止。此外, 如果语言实现了尾部调用消除, 我们将在7章、功能技术和主题中讨论的主题, 选择右侧开始折叠可能会避免堆栈溢出并允许操作完成。

由于没有无限列表或尾调用消除是由 PHP 执行的, 在我看来, 没有任何理由来困扰的区别。如果你有兴趣，array\_reduce函数从左边折叠起来，实现一个从右边执行相同功能的函数 , 不会太复杂 , 试试 . 
