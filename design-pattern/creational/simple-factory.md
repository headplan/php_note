# 简单工厂模式 - Simple Factory

[https://zh.wikipedia.org/wiki/工厂方法\#简单工厂](https://zh.wikipedia.org/wiki/工厂方法#简单工厂)

#### 描述

简单工厂模式是一个精简版的工厂模式 . 它与静态工厂模式最大的区别是它不是『静态』的 . 因为非静态 , 所以可以拥有多个不同参数的工厂 , 可以为其创建子类 . 甚至可以模拟\(Mock\)他 , 这对编写可测试的代码来讲至关重要 . 这也是它比静态工厂模式受欢迎的原因 .

#### 使用

**工厂方法模式\(Factory Method\)**通常伴随着对象的具体类型与工厂具体类型的一一对应 , 客户端代码根据需要选择合适的具体类型工厂使用 . 然而 , 这种选择可能包含复杂的逻辑 . 这时 , 可以创建一个单一的工厂类 , 用以包含这种选择逻辑 , 根据参数的不同选择实现不同的具体对象 . 这个工厂类不需要由每个具体产品实现一个自己的具体的工厂类 , 所以可以将工厂方法设置为静态方法 , 即**静态工厂模式\(Static Factory\)** . 而且 , 工厂方法封装了对象的创建过程 . 如果创建过程非常复杂\(比如依赖于配置文件或用户输入\) , 工厂方法就非常有用了 . 比如 , 一个程序要读取图像文件 . 程序支持多种图像格式 , 每种格式都有一个对应的`ImageReader`类用来读取图像 . 程序每次读取图像时 , 需要基于文件信息创建合适类型的`ImageReader` . 这个选择逻辑就可以包装在一个简单工厂中 . 

#### 结构

![](/assets/simple-factory.png)
