# 静态工厂模式 - Static Factory

[https://zh.wikipedia.org/wiki/工厂方法\#简单工厂](#)

#### 描述

与抽象工厂模式类似 , 此模式用于创建一系列相关或相互依赖的对象 . 『静态工厂模式』与『抽象工厂模式』的区别在于 , 只使用一个静态方法来创建所有类型对象 , 此方法通常被命名为`factory`或`build` . 简单工厂模式也类似 . 

#### 使用

Zend Framework : `Zend_Cache_Backend`或`_Frontend`使用工厂方法创建缓存后端或前端 . 

#### 结构

![](/assets/static-factory.png)

