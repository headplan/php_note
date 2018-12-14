# The Yaf\_Application class

Yaf\_Application代表一个产品/项目 , 是Yaf运行的主导者 , 真正执行的主题 . 它负责接收请求 , 协调路由 , 分发 , 执行 , 输出 . 

Yaf\_Application实现了单例模式 . Yaf\_Application不能够被序列化和反序列化 . 使用PHPUnit来为Yaf写一些测试用例的时候 , 可以使用PHPUnit的@backupGlobals注释来控制全局变量的备份和恢复操作 , 来解决序列化的问题 . 

