# 日常使用

#### 全局访问 Homestead

在文件系统的任意路径都能运行`vagrant up`命令启动 Homestead 虚拟机 , 在文件~/.bash\_profile中添加函数

```bash
function homestead() {
    ( cd ~/Homestead && vagrant $* )
}
```

如果写在.zshrc文件中 , 不需要function关键字

```
homestead() {
    ( cd ~/Homestead && vagrant $* )
}
```

#### 通过 SSH 连接

可以通过在 Homestead 目录下运行`vagrant ssh`终端命令以 SSH 的方式连接到虚拟机 , 也可以使用前面添加的函数的全局方式

```
homestead ssh
```

#### 连接数据库

`homestead`默认已经在虚拟机中为 MySQL 和 PostgreSQL 数据库做好了配置 . 要从主机的数据库客户端连接到 MySQL 或 PostgreSQL , 应该连接到`127.0.0.1` , 端口`33060`\(MySQL\)或`54320`\(PostgreSQL\) . 用户名和密码分别是`homestead`/`secret` .

这里的33060和54320是映射的端口 . 



