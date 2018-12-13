# 异常和错误

Yaf实现了一套错误和异常捕获机制 , 主要是对常见的错误处理和异常捕获方法做了一个简单抽象 , 方便应用组织自己的错误统一处理逻辑 .

Yaf自身出错时候 , 根据配置可以分别采用抛异常或者触发错误的方式来通知错误 . 在appliation.dispatcher.throwException\(配置文件, 或者通过Yaf\_Dispatcher::throwException\(true\)\)打开的情况下 , Yaf会抛异常 , 否则则会触发错误 . 

那么对应的 , 就有俩套错误处理方式可供应用选用 . 

