# Functors,Applicatives,Monads

前一章介绍了第一个真正的函数式技巧 , 如功能组合和currying . 在本章中 , 我们继续介绍更多的理论概念 , 例如Monads的概念 . 这里主要还是介绍理论内容 , 后面 , 会有一个章节专门学习使用Monad去解决实际的问题 .

你可能已经听说过 Monad 这个词了 . 通常 , 它被没有接触过函数式编程的人认为是难以理解的 , 尽管在这个主题上有无数的教程 . 事实上, 它确实很难理解 , 但是写这些教程的人往往忘记了他们花了多少时间来正确理解这个想法 . 这是一个常见的教学陷阱 , 可能在本文中描述得更好 .

> [https://byorgey.wordpress.com/2009/01/12/abstraction-intuition-and-the-monad-tutorial-fallacy/](https://byorgey.wordpress.com/2009/01/12/abstraction-intuition-and-the-monad-tutorial-fallacy/)

你可能不会第一次得到一切 . “Monads”是一个非常抽象的概念 , 即使你已经看了很多关于Monads的概念 , 你也可能会不经意的突然模糊了概念 , 这会阻碍你对 Monad 的真正的直觉 .

如果这里还是没有解释清楚 , 我会继续翻译更多相关的资料 , 比如上面提到的文章 . 而现在 , 我们要讨论的主题是 :

* Functors 的相关规则
* Applicative functors 的相关规则
* Monoids 的相关规则
* Monads 的相关规则

这里的概念完全理解之后 , 下一章节的真实例子会让你更加的理解Monad . 



