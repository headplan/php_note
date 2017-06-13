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

