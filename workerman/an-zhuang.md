# 安装

### Linux环境检查脚本 {#linux环境检查脚本}

```
curl -Ss http://www.workerman.net/check.php | php
```

如果脚本中全部提示ok , 则代表满足WorkerMan运行环境 . 

> 注意 : 检测脚本中没有检测event扩展或者libevent扩展 , 如果并发连接数大于1024建议安装event扩展或者libevent扩展



