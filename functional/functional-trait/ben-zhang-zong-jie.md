# 本章总结

为什么要用纯函数 , 不变性和最终的引用透明性 , 如果PHP开发人员只能受益于少量的优势 , 还有必要了解它们么 ?

首先 , 与不变性的RFC一样 , 事情正在朝着正确的方向发展 . 这意味着 , 最终 , PHP引擎将开始并入其中的一些先进的编译器技术 . 当这种情况发生时 , 如果您的代码库已经使用了这些功能技术 , 那么您将会获得巨大的性能提升 .

其次 , 在我看来 , 所有这些都是减轻认知负担的主要好处 . 当然 , 需要一些时间来习惯这种新的编程风格 . 但一旦你练习了一下 , 你会很快发现你的代码更容易阅读和理解 . 您的应用程序将包含较少的错误的必然结果 .

最后 , 如果您愿意使用一些外部库 , 或者您可以处理不总是很好的语法 , 那么您现在就可以从其他改进中受益了 . 显然 , 我们不能改变 PHP 的核心来添加我们之前讨论的编译器优化 , 但是我们将在下面的章节中看到如何模拟引用透明性的一些好处 .

---

本章包含了大量的理论 . 我希望你不介意太多 . 有必要打下基础 , 让我们分享一个共同的词汇 , 也解释为什么这些概念是重要的 . 你现在很清楚什么是纯函数和不变性 , 你学会了一些技巧来发现不纯的功能 .

我们还讨论了这两个属性如何导致所谓的引用透明度的好处是什么 . 我们还了解到 , 可悲的是 , PHP本身并不支持大部分好处 . 然而 , 关键的是 , 使用函数式可以减少理解代码的认知负担 , 从而使阅读变得更容易 . 现在, 您的代码将更易于维护和重构 , 并且您可以快速查找和修复 bug , 这是纯函数的好处 . 通常 , 纯函数也更容易测试 , 这也会导致更少的 bug .

由于概念理论太多 , 所以更详细的理论内容可以查看新增的Functional Note中的文章 .

