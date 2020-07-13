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



