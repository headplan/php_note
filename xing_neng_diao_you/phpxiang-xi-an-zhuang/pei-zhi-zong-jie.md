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

# FPM方式
--enable-fpm --with-fpm-user=$run_user --with-fpm-group=$run_user --with-fpm-systemd (--with-fpm-acl) \
# Apache模块 --with-apxs2=$apache_install_dir/bin/apxs

# 其他SAPI和General有关的配置(生成环境不建议开启)
--enable-embed --enable-phpdbg --enable-phpdbg-webhelper --enable-phpdbg-debug \

# 扩展相关配置(默认)
--disable-fileinfo --with-iconv-dir=/usr/local \
--with-libxml-dir=/usr \ # 很多扩展需要这个路径支持
# 扩展相关配置(其他)
# MySQL支持
--enable-mysqlnd --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd \

# GD库支持
--with-gd --enable-gd-native-ttf --with-freetype-dir --with-jpeg-dir --with-png-dir --with-zlib \

--enable-bcmath \
--enable-shmop \
--enable-exif \
--enable-sysvsem \
--enable-mbregex \
--enable-mbstring \
--enable-pcntl
--enable-sockets
--enable-ftp \
--enable-intl \
--enable-zip \
--enable-soap \
--with-mcrypt \
--with-openssl \
--with-curl=/usr/local \
--with-mhash \  
--with-xmlrpc \ 
--with-xsl \
--with-gettext \ 
--disable-rpath \ # 关闭额外运行库支持

--enable-gd-jis-conv 
--enable-sysvmsg
--enable-sysvshm
--with-pcre-dir  
--with-readline 
--with-recode 
--with-tidy
--with-bz2   

# 这三个配置已经默认了
--enable-xml
--disable-debug 
--enable-inline-optimization
# 此外下面两项也已经默认开启了
--enable-opcache
--with-pear

# ${THREAD} = grep "cpu cores" /proc/cpuinfo
make ZEND_EXTRA_LIBS='-liconv' -j ${THREAD}
make install
```

**加入环境变量**

    [ -z "`grep ^'export PATH=' /etc/profile`" ] && echo "export PATH=$php_install_dir/bin:\$PATH" >> /etc/profile
    [ -n "`grep ^'export PATH=' /etc/profile`" -a -z "`grep $php_install_dir /etc/profile`" ] && sed -i "s@^export PATH=\(.*\)@export PATH=$php_install_dir/bin:\1@" /etc/profile
    # 这里的点空格表示source
    . /etc/profile

**配置文件**

```
# 如果php.d目录不存在,则创建该目录
[ ! -e "$php_install_dir/etc/php.d" ] && mkdir -p $php_install_dir/etc/php.d
# 复制配置文件到php安装目录的/etc目录下
/bin/cp php.ini-production $php_install_dir/etc/php.ini
```

然后是配置文件的优化 , 这里查看关于PHP配置文件的笔记 . 如果有扩展 , 配置可以放到 , 

```
$php_install_dir/etc/php.d/ext-opcache.ini
```

**然后是FPM文件加入到开机启动**

```
/bin/cp sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
chmod +x /etc/init.d/php-fpm
```

**添加服务**

```
chkconfig --add php-fpm; chkconfig php-fpm on;
```

然后是优化配置FPM , 这部分查看PHP-FPM配置文件笔记 . 

**最后启动**

```
service php-fpm start
```

**参考资料**

http://blog.csdn.net/u010861514/article/details/51926575

https://typecodes.com/web/centos7compilephp7.html

---

#### 动态编译加载其他php模块

```
# 查看
ls php-src/ext/
# 假设编译mbstring模块
# 进入mbstring的源码包目录,利用phpize生成configure文件
cd mbstring/
/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config
make && make install
# 编译完成后生成文件
ls /usr/local/php/lib/php/extensions/no-debug-non-zts-20131226/
# 添加配置文件到php.d目录
echo "extension=mbstring.so" > $php_install_dir/etc/php.d/ext-mbstring.ini
```



