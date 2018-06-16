# 源码解析

WorkerMan中有两个重要的类**Worker**与**Connection** . 

Worker类用于实现端口的监听 , 并可以设置客户端连接事件、连接上消息事件、连接断开事件的回调函数 , 从而实现业务处理 . 

可以设置Worker实例的进程数\(Count属性\) , Worker主进程会fork出Count个子进程同时监听相同的端口 , 并行的接收客户端连接 , 处理连接上的事件 . 

每个客户端连接对应一个Connection对象 , 可以设置对象的onMessage、onClose等回调 , 同时提供了向客户端发送数据send接口与关闭连接close接口 , 以及其它一些必要的接口 . 

可以说Worker是一个监听容器 , 负责接受客户端连接 , 并把连接包装成Connection对象式提供给开发者操作 . 



