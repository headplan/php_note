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



