# The Yaf\_Bootstrap\_Abstract class

Bootstrap是用来在Application运行\(run\)之前做一些初始化工作的机制 . 可以通过继承Yaf\_Bootstrap\_Abstract 来定义自己的Bootstrap类 . 在Bootstrap类中所有以"\_init"开头的公有的方法 , 都会被按照定义顺序依次在Yaf\_Application::bootstrap\(\)被调用的时刻调用 . 





