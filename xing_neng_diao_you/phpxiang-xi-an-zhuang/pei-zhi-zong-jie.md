# 配置总结

#### 安装依赖

```
# 基础依赖可以按照前面的安装
# Ubuntu
apt-get install build-essenial autoconf automake libtool bison re2c
# CentOS
yum groupinstall "Development Tools"
yum install re2c -y
```

**其他依赖**

```
yum -y install gcc gcc-c++ \

# 默认依赖
libxml2 libxml2-devel \
pcre pcre-devel \
glibc glibc-devel \ # 编译安装libiconv替换,--with-iconv-dir=DIR

# 扩展依赖
```

#### 基本配置

```
# 系统账户
run_user=php

# 安装目录
php_install_dir=/usr/local/php7
```

**创建用户**

```
# 创建用户&用户组
# groupadd -r 表示加入组ID号,低于499系统账号
# useradd -r -g 同样是系统账号,并用-g指定组,也就是php.
# 这里的-s是指定用户登入后所使用的shell,如果指定为-s /bin/false,则表示禁止登陆
# -d php_install_dir 指定其实目录
# -M 不要自动建立用户的登入目录

# 检查有没有这个用户
id -u $run_user >/dev/null 2>&1
[ $? -ne 0 ] && groupadd -r $run_user && useradd -r -g $run_user -s /bin/false -d $php_install_dir -M $run_user
```

**编译安装**

```
make clean
./buildconf --force
[ ! -d "$php_install_dir" ] && mkdir -p $php_install_dir

./configure --prefix=$php_install_dir \
--with-config-file-path=$php_install_dir/etc \
--with-config-file-scan-dir=$php_install_dir/etc/php.d \
--enable-fpm --with-fpm-user=$run_user --with-fpm-group=$run_user --with-fpm-systemd --with-fpm-acl\
# 其他SAPI和General有关的配置
--enable-embed --enable-phpdbg --enable-phpdbg-webhelper --enable-phpdbg-debug \

# 扩展相关配置



$PHP_cache_tmp --disable-fileinfo \
```



