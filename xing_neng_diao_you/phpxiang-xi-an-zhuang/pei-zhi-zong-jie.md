# 配置总结

```
# 创建用户&用户组
# groupadd -r 表示加入组ID号,低于499系统账号
# useradd -r -g 同样是系统账号,并用-g指定组,也就是php.
# 这里的-s是指定用户登入后所使用的shell,如果指定为-s /bin/false,则表示禁止登陆
# -d /usr/local/php7 指定其实目录
# -M 不要自动建立用户的登入目录
groupadd -r php && useradd -r -g php -s /bin/false -d /usr/local/php7 -M php
```

#### 安装依赖

```
# 基础依赖可以按照前面的安装
# Ubuntu
apt-get install build-essenial
# CentOS
yum groupinstall "Development Tools"
```



