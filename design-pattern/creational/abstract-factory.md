# 抽象工厂模式 - Abstract Factory

[https://zh.wikipedia.org/wiki/抽象工厂](https://zh.wikipedia.org/wiki/抽象工厂)

#### 描述

抽象工厂模式的实质是提供接口 , 创建一系列相关或独立的对象 , 而不指定这些对象的具体类 . 抽象工厂模式提供了一种方式 , 可以将一组具有同一主题的单独的工厂封装起来 .

在正常使用中 , 客户端程序需要创建抽象工厂的具体实现 , 然后使用抽象工厂作为接口来创建这一主题的具体对象 . 客户端程序不需要知道或关心它从这些内部的工厂方法中获得对象的具体类型 , 因为客户端程序仅使用这些对象的通用接口 . 抽象工厂模式将一组对象的实现细节与他们的一般使用分离开来 .

举例说明 , 比如一个抽象工厂类叫做`DocumentCreator`\(文档创建器\) , 此类提供创建若干种产品的接口 , 包括`createLetter()`\(创建信件\)和`createResume()`\(创建简历\) . 其中 , `createLetter()`返回一个`Letter`\(信件\) , `createResume()`返回一个`Resume`\(简历\) . 系统中还有一些`DocumentCreator`的具体实现类，包括`FancyDocumentCreator`和`ModernDocumentCreator` . 这两个类对`DocumentCreator`的两个方法分别有不同的实现 , 用来创建不同的“信件”和“简历” . 

> 用`FancyDocumentCreator`的实例可以创建`FancyLetter`和`FancyResume`, 用`ModernDocumentCreator`的实例可以创建`ModernLetter`和`ModernResume`

这些具体的“信件”和“简历”类均继承自抽象类 , 即`Letter`和`Resume`类 . 客户端需要创建“信件”或“简历”时 , 先要得到一个合适的`DocumentCreator`实例 , 然后调用它的方法 . 一个工厂中创建的每个对象都是同一个主题的\(“fancy”或者“modern”\) . 客户端程序只需要知道得到的对象是“信件”或者“简历” , 而不需要知道具体的主题，因此客户端程序从抽象工厂`DocumentCreator`中得到了`Letter`或`Resume`类的引用 , 而不是具体类的对象引用 . 

#### 使用

#### 结构


