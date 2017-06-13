# 配置

Web服务器历史

> 几年前的Web服务器 , 我们都会安装Apache Web服务器和Apache mod\_php模块 . Apache Web服务器会为每个HTTP请求派生一个专门的子进程 . Apache mod\_php模块会在派生的每个子进程中嵌入专门的PHP解释器 . 即使进程只是用来伺服静态资源 , 例如js,css,图像等也会嵌入PHP解释器 . 这么做消耗很大 , 会浪费系统资源 . 过后的几年 , Nginx出来了 .

说时迟那时快 , 我们的VPS已经购买好了 .

#### 首次登陆

```
ssh root@127.0.0.1
之后,可能会提示你,让你确认新服务器的可靠性,输入yes回车即可.
接下来输入密码,bingo登录成功
```

#### 升级软件

```
紧接着我们应该做的就是升级系统中的软件
# Ubuntu
apt-get update;
apt-get upgrade;
# CentOS
yum update
```

#### 非root用户

现在我们的新服务器还不太安全 . 下面是一些能加强新服务器安全性的良好实践  .

**创建非root用户**

```
# Ubuntu
adduser demo
passwd demo
usermod -a -G sudo demo
让demo用户用用sudo权限

# CentOS
adduser demo
passwd demo
usermod -a -G wheel demo
让demo用户用用sudo权限
```

#### SSH密钥对认证

> 因为密码认证有漏洞,会受到暴力攻击.使用ssh登录服务器时应该使用SSH密钥对认证.
>
> 密钥对认证是个复杂的话题,简单来说,在本地设备中创建一对"密钥",其中一个是私钥\(保存在本地设备中\),另一个是公钥\(传到远程服务器中\).之所以叫密钥对,是因为使用公钥加密的消息只能使用对应的密钥解密.
>
> 使用SSH密钥对认证方式登录远程设备时,远程设备会随机创建一个消息,使用公钥加密,然后把密文发给本地设备.本地设备收到密文后使用私钥解密,然后把解密后的消息发给远程服务器.远程服务器验证解密后的消息之后,再赋予你访问服务器的权限,简单的流程就是这样的.
>
> 如要要在多台电脑中登录远程服务器,或许不应该使用SSH密钥对认证.如果想这么做,要在每台本地电脑上生成SSH密钥对,然后再把每个密钥对中的公钥复制到远程服务器中.遇到这种情况,或许最好使用安全的密码进行认证.然而,如果只通过一台本地电脑访问远程服务器\(很多开发者都这样\),SSH密钥对认证是最好的方式.
>
> 另一个推荐,Google身份验证器.

```
ssh-keygen # 生成密钥对
常用参数:
ssh-keygen -t rsa # 加密方式,也可使用dsa加密
ssh-keygen -f test # 指定生成文件的文件名
ssh-keygen -C "test key" # 备注
```

我们直接执行ssh-keygen看看一下执行的步骤

```
ssh-keygen
# 这里输入我们想要的文件名
Generating public/private rsa key pair.
Enter file in which to save the key (/home/huqiu/.ssh/id_rsa):
# 然后会问你是否需要输入密码,这里直接回车跳过
Enter passphrase (empty for no passphrase):
Enter same passphrase again:

# 直接使用的命令
ssh-keygen -t rsa -f niaoyun -C "headplan@163.com"
```

生成之后 , 会看到~/.ssh/id\_rsa.pub\(公钥\)和~/.ssh/id\_rsa\(私钥\).然后我们应该把公钥放到服务器中 .

```
scp ~/.ssh/id_rsa.pub demo@127.0.0.1:
```

然后用demo账户登录服务器,看看里面有没有~/.ssh目录 , 如果没有新建一个

```
mkdir ~/.ssh
# 这个文件是一系列允许登录这台远程服务器的公钥.
touch ~/.ssh/authorized_keys
cat ~/demo.pub >> ~/.ssh/authorized_keys
```

最后要修改几个目录和文件的访问权限 , 只让用户访问~/.ssh目录和~/.ssh/authorzed\_keys文件 .

```
chown -R demo:demo ~/.ssh
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

**禁用密码,禁止根用户登录**

下面我们要禁止所有用户通过密码登录,还要禁止跟用户登录.

编辑SSH服务器软件的配置文件

```
sudo vim /etc/ssh/sshd_config
# 找到下面两个配置改为no
PasswordAuthentication no
PermitRootLogin no

# 重启SSH服务器
# Ubuntu
sudo service ssh restart
# CentOS
sudo systemctl restart sshd.service
```

现在,我们的服务器安全了,但是安全是长久性的任务.所以还建议安装防火墙.

Ubuntu用户可以使用UFW , CentOS用户可以使用iptables.

#### PHP-FPM

PHP FastCGI进程管理器 , 适用于管理PHP进程池的软件 . 用于接收和处理来自Web服务器\(例如nginx\)的请求 .

PHP-FPM软件会创建一个主进程\(通常以操作系统root用户的身份运行\),控制何时以及如何把HTTP请求转发给一个或多个子进程处理 . PHP-FPM主进程还控制着什么时候创建\(处理Web应用更多的流量\)和销毁\(子进程运行时间太久或不再需要了\)PHP子进程.PHP-FPM进程池中的每个进程存在的时间都比单个HTTP请求长,可以处理10,50,100,500或更多的HTTP请求 .

**安装**

最简单的方式是使用操作系统原生的包管理器 :

> 包管理器 , Ubuntu使用aptitude管理器 , CentOS和RHEL使用yum包管理器 .

**Ubuntu14.04LTS**

默认的软件仓库中没有提供最新版本的PHP,因此需要添加社区维护的个人软件包档案PPA , 这是Ubuntu专用的 , 它使用第三方软件仓库扩展了默认的软件 .

1.安装依赖

添加PPA之前要确保系统中有add-apt-repository二进制文件,这个二进制文件包含在Ubuntu包python-software-properties中.安装:

```
sudo apt-get install python-software-properties
```

上面的命令会安装包含add-apt-repository二进制文件的python-software-properties包 . 现在就可以自定义添加PPA了.

2.添加ppa:ondrej/php5-5.6 PPA

开始扩充默认软件仓库

```
sudo add-apt-repository ppa:ondrej/php5-5.6
```

命令添加了PPA到软件源列表 , 还会下载这个PPA的GPG公钥,并将其添加到本地GPG密钥环中.GPG公钥是让Ubuntu验证PPA中的包,确保原始作者构建并签名之后没有被篡改.

然后刷新缓存

```
sudo apt-get update
```

3.安装PHP

```
# 首先安装PHP CLI包
sudo apt-get install php5-cli
# 然后安装几个PHP扩展包
sudo apt-get install php5-curl php5-gd php5-json php5-mcrypt php5-mysqlnd
# 检查是否安装成功,可以看到版本信息
php -v
```

**CentOS7**

这里我们使用EPEL,就是企业版Linux的额外包 , 虽然EPEL和官方发行版没啥关系 , 不过仍然可以用来扩充默认的软件仓库 .

> #### Centos/RedHat 7/6/5切换阿里云源并安装EPEL/IUS/REMI仓库
>
> CentOS安装完毕后，默认的yum源是国外的，国内访问速度很慢，需要切换为国内的源，这里选用的是阿里云的源。此外，一些常用的仓库，如epel和ius，最好也一并安装了。
>
> ##### 切换基本源为阿里云源
>
> 备份
>
> ```
> mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
> ```
>
> 下载新的`CentOS-Base.repo`到`/etc/yum.repos.d/`
>
> * CentOS 5
>
> ```
> wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo
> ```
>
> * CentOS 6
>
> ```
> wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
> ```
>
> * CentOS 7
>
> ```
> wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
> ```
>
> #### 安装第三方源
>
> ##### 安装EPEL仓库
>
> ```
> yum install -y epel-release
> ```
>
> **如果以上命令不起作用：**
>
> * CentOS/RHEL 7
>
> ```
> rpm -Uvh http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
> ```
>
> * CentOS/RHEL 6
>
> ```
> rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
> ```
>
> * CentOS/RHEL 5
>
> ```
> rpm -Uvh http://dl.fedoraproject.org/pub/epel/5/x86_64/epel-release-5-4.noarch.rpm
> ```
>
> ##### 
>
> ##### 安装IUS仓库
>
> ###### RHEL
>
> * RHEL 5
>
> ```
> rpm -Uvh https://rhel5.iuscommunity.org/ius-release.rpm
> ```
>
> * RHEL 6
>
> ```
> rpm -Uvh https://rhel6.iuscommunity.org/ius-release.rpm
> ```
>
> * RHEL 7
>
> ```
> rpm -Uvh https://rhel7.iuscommunity.org/ius-release.rpm
> ```
>
> ###### 
>
> ###### CentOS
>
> * CentOS 5
>
> ```
> rpm -Uvh https://centos5.iuscommunity.org/ius-release.rpm
> ```
>
> * CentOS 6
>
> ```
> rpm -Uvh https://centos6.iuscommunity.org/ius-release.rpm
> ```
>
> * CentOS 7
>
> ```
> rpm -Uvh https://centos7.iuscommunity.org/ius-release.rpm
> ```
>
> ##### 
>
> ##### 安装REMI仓库
>
> * CentOS/RHEL 7
>
> ```
> rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
> ```
>
> * CentOS/RHEL 6
>
> ```
> rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
> ```
>
> * CentOS/RHEL 5
>
> ```
> rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-5.rpm
> ```
>
> ##### 查看yum软件仓库列表
>
> ```
> yum repolist
> ```
>
> ##### 生成缓存
>
> ```
> yum makecache
> ```

1.添加EPEL仓库

```
# 查看添加了那些仓库
yum repolist


# 针对系统架构选择相应的类型:http://dl.fedoraproject.org/pub/epel/7/
rpm -Uvh \
http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-9.noarch.rpm
或者
wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-9.noarch.rpm
rpm -Uvh epel-release-7-2.noarch.rpm
或者直接yum安装
yum -y install epel-release


# 添加remi仓库
CentOS7
$ sudo rpm --import http://rpms.famillecollet.com/RPM-GPG-KEY-remi
$ sudo rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
CentOS6
$ sudo rpm --import http://rpms.famillecollet.com/RPM-GPG-KEY-remi
$ sudo rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
# 默认地REMI是禁用的,检查安装情况
yum repolist disabled | grep remi

# 要搜索或安装REMI仓库中的包,使用这些命令:
$ sudo yum --enablerepo=remi search <keyword>
$ sudo yum --enablerepo=remi install <package-name>
```

2.安装PHP

```
sudo yum -y --enablerepo=epel,remi,remi-php56 install php-cli
```

安装PHP扩展

```
sudo yum -y --enablerepo=epel,remi,remi-php56 \
    install php-gd php-mbstring php-mcrypt php-mysqlnd php-opcache php-pdo
# 检查是否安装成功,可以看到版本信息
php -v
```

以上的内容只是个引子 , 不是这篇内容的主题 .

##### 安装

```
# Ubuntu
sudo apt-get install python-software-properties;
sudo add-apt-repository ppa:ondrej/php5-5.6;
sudo apt-get update;
sudo apt-get install php5-fpm php5-cli php5-curl \
    php5-gd php5-json php5-mcryt php5-mysqlnd;
# CentOS
sudo rpm -Uvh \
    http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-9.noarch.rpm
sudo rpm -Uvh \
    rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
sudo yum -y --enablerepo=epel,remi-php56 install php-fpm php-cli php-gd \
    php-mbstring php-mcrypt php-mysqlnd php-opcache php-pdo php-devel
```

> ##### PHP-FPM配置转入下一篇文章

#### Nginx

和Apache类似 , 也是一个Web服务器 , 而且使用的系统内存通常更少 .

下面的内容主要是安装和把相应的请求转发给PHP-FPM进程池 .

**nginx资料**

* http://nginx.org/
* https://github.com/h5bp/server-configs-nginx
* https://serversforhackers.com/editions/2014/03/25/nginx/



