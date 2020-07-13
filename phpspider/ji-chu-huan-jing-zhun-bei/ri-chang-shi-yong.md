# 日常使用

#### 全局访问 Homestead

在文件系统的任意路径都能运行`vagrant up`命令启动 Homestead 虚拟机 , 在文件~/.bash\_profile中添加函数

```bash
function homestead() {
    ( cd ~/Homestead && vagrant $* )
}
```



