# 基础环境准备

为了后续更好扩展 , 这里选择Laravel框架为基础 , 本地环境使用Homestead配置 .

相关资料

[https://app.vagrantup.com/laravel/boxes/homestead/versions/10.0.0-beta.1](https://app.vagrantup.com/laravel/boxes/homestead/versions/10.0.0-beta.1)

[https://learnku.com/docs/laravel/7.x/homestead/7450](https://learnku.com/docs/laravel/7.x/homestead/7450)

#### 安装 Homestead Vagrant Box {#4820f7}

```
vagrant box add laravel/homestead
```

国内网络环境 , 可以先下载之后再添加box .

```
vagrant box add metadata.json
```

查看metadata.json文件

```json
{
    "name": "laravel/homestead",
    "versions":
    [
        {
            "version": "10.0.0-beta.1",
            "providers": [
                {
                  "name": "virtualbox",
                  "url": "Homestead-10.0.0-beta.1.box"
                }
            ]
        }
    ]
}
```

#### 安装 Homestead {#ebf359}

```
git clone https://github.com/laravel/homestead.git ~/Homestead
```

建议将代码克隆到home目录下的Homestead文件夹中 , 这样Homestead box就可以作为所有Laravel项目的主机 .

检出文档版

```
git checkout release
```

在 Homestead 目录中使用`bash init.sh`命令来创建`Homestead.yaml`配置文件 .

#### 配置 Homestead

配置Homestead.yaml中的provider

```
provider: virtualbox
```

配置共享文件夹

```
folders:
    - map: ~/code/project1
      to: /home/vagrant/project1
```

还可以配置同步文件夹的类型 , 提高性能

```
folders:
    - map: ~/code/project1
      to: /home/vagrant/project1
      type: "nfs"
```

> 注意 : 当使用 NFS 时 , 最好使用[vagrant-winnfsd](https://github.com/winnfsd/vagrant-winnfsd)扩展插件 . 这个插件会替你处理 Homestead box 中的文件或目录权限的问题 . 
>
> [https://github.com/winnfsd/vagrant-winnfsd](https://github.com/winnfsd/vagrant-winnfsd)

还可以可以通过在`options`中列出 Vagrant 的[同步文件夹](https://www.vagrantup.com/docs/synced-folders/basic_usage.html)支持的任何选项

```
folders:
    - map: ~/code/project1
      to: /home/vagrant/project1
      type: "rsync"
      options:
          rsync__args: ["--verbose", "--archive", "--delete", "-zz"]
          rsync__exclude: ["node_modules"]
```

> 详细参考 : [https://www.vagrantup.com/docs/synced-folders](https://www.vagrantup.com/docs/synced-folders)



