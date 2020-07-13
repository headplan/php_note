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

#### 数据库备份

开启自动数据库备份 , 需添加如下的行到`Homestead.yaml`文件

```
backup: true
```

配置了backup之后 , 当`vagrant destroy`命令被执行销毁时 , Homestead 将导出数据库到`mysql_backup`和`postgres_backup`目录 .

#### 数据库快照

Homestead 支持保存 MySQL 和 MariaDB 数据库的状态并通过[Logical MySQL Manager](https://github.com/Lullabot/lmm)在不同状态间进行切换 . 例如 , 假设你的站点数据库有几个 G 的数据量 , 可以导入这个数据库并保存一份快照 , 在本地工作一段时间后有可能会创建一些新的测试内容 , 你可以通过快照快速恢复到最初的状态 .

在底层 , LMM 使用了 LVM 的支持写时复制的瘦快照功能 , 这意味着当修改表中某条记录时 , 只会将你所做的更改写入磁盘 , 从而在恢复期节省大量时间和磁盘空间 .

由于`lmm`会与 LVM 进行交互 , 所以必须以`root`身份运行 . 要了解所有命令 , 可以在 Homestead 虚拟机中通过`sudo lmm`查看 . 常见的工作流会是这样 :

* 导入数据库到 lmm 默认的`master`分支
* 运行`sudo lmm branch prod-YYYY-MM-DD`保存尚未做任何修改的数据库快照
* 修改数据库记录
* 运行`sudo lmm merge prod-YYYY-MM-D`撤销所有修改
* 运行`sudo lmm delete <branch>`删除不需要的分支

#### 添加额外的站点

到`Homestead.yaml`文件中添加站点 :

```
sites:
    - map: homestead.test
      to: /home/vagrant/project1/public
    - map: another.test
      to: /home/vagrant/project2/public
```

然后添加host , 重启并重置配置`vagrant reload --provision`

#### 站点类型 {#8681e1}

Homestead 支持多种类型的站点 , 允许运行不是基于 Laravel 的项目 .

```yaml
sites:
    - map: symfony2.test
      to: /home/vagrant/my-symfony-project/web
      type: "symfony2"
```

可用的站点类型是 : apache , apigility , expressive , laravel\(默认\) , proxy , silverstripe , statamic , symfony2 , symfony4和zf . 





