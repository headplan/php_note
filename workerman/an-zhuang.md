# 安装

### Linux环境检查脚本 {#linux环境检查脚本}

```
curl -Ss http://www.workerman.net/check.php | php
```

如果脚本中全部提示ok , 则代表满足WorkerMan运行环境 .

> 注意 : 检测脚本中没有检测event扩展或者libevent扩展 , 如果并发连接数大于1024建议安装event扩展或者libevent扩展

#### pcntl

pcntl扩展是PHP在Linux环境下进程控制的重要扩展 , WorkerMan用到了其进程创建、信号控制、定时器、进程状态监控等特性 . 

#### posix扩展

posix扩展使得PHP在Linux环境可以调用系统通过POSIX标准提供的接口 . WorkerMan主要使用了其相关的接口实现了守护进程化、用户组控制等功能 . 

#### libevent扩展 或 Event扩展

libevent扩展\(或者event扩展\)使得PHP可以使用系统Epoll、Kqueue等高级事件处理机制 , 能够显著提高WorkerMan在高并发连接时CPU利用率 . 在高并发长连接相关应用中非常重要 . libevent扩展\(或者event扩展\)不是必须的 , 如果没安装 , 则默认使用PHP原生Select事件处理机制 . 

WorkerMan实际上就是一个PHP代码包 , 如果你的PHP环境已经装好 , 只需要把WorkerMan源代码或者demo下载下来即可运行 . 

