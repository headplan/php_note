# 扩展安装

### 已有PHP环境安装缺失扩展

#### 安装pcntl和posix扩展

**Centos系统**

如果php是通过yum安装的 , 则命令行运行 :

```
yum install php-process
```

即可安装pcntl和posix扩展 .

如果安装失败 , 或者不是通过yum安装的PHP , 可以选择源码编译安装 . 源码编译安装一般都是安装PHP自带的扩展 , 这里以安装pcntl扩展为例 :

利用`php -v`命令查看当前的PHP CLI的版本 , 根据版本下载PHP源代码 . [http://php.net/releases/](http://php.net/releases/)

解压源码压缩包 :

```
tar -zxvf php-5.100.100.tar.gz
```

进入源码中的ext/pcntl目录 . 运行`phpize`命令 . 然后运行`configure`命令 :

```
./configure
```

然后是`make`和`make install`命令 . 安装完成 , 配置ini文件 .

通过运行`php --ini`查找php.ini文件位置 , 然后在文件中添加`extension=pcntl.so`

一般PHP自带的扩展都可以这样安装 , 也可以重新编译整个PHP , 编译时候带上这两个扩展的编译参数 :

```php
./configure --enable-pcntl --enable-posix ...等等
```

**debian/ubuntu/mac os系统**

编译安装 , 同上 .

#### 安装event或libevent扩展

为了能支持更大的并发连接数 , 建议安装event扩展或者libevent扩展\(二者作用相同 , 二选一即可\) . 这里以Event为例 , 因为支持PHP7 .

**Centos系统**

安装event扩展依赖的libevent-devel包 :

```
yum install libevent-devel -y
```

安装event扩展

```
pecl install event
```

注意提示 : `Include libevent OpenSSL support [yes]:` 时输入no回车 , 其它直接敲回车就行 .

添加配置

```
echo extension=event.so > /etc/php.d/30-event.ini
```

**debian/ubuntu系统安装**

安装event扩展依赖的libevent-dev包 :

```
apt-get install libevent-dev -y
```

安装event扩展 :

```
pecl install event
```

其他同上 .

**mac os 系统安装一般作为开发机 , 不用安装event扩展 . **

---

### 全新系统安装\(全新安装PHP+扩展\)

#### Centos系统安装

命令行运行 , 安装PHP命令行以及相应扩展 :

```php
yum install php-cli php-process git gcc php-devel php-pear libevent-devel -y
```

安装event扩展 , 命令行运行

```php
pecl install event
```

> **注意 : **
>
> 提示`Include libevent OpenSSL support [yes] :`时输入`no`回车 , 其它直接敲回车就行 .

配置php.ini

```
echo extension=event.so > /etc/php.d/30-event.ini
```

然后clone代码 :

```php
git clone https://github.com/walkor/Workerman
```

#### debian/ubuntu系统安装

命令行运行 , 安装PHP命令行以及相应扩展 :

```
apt-get install php5-cli git gcc php-pear php5-dev libevent-dev -y
```

其他同上 .

#### MacOS系统安装

Mac系统自带PHP Cli , 但是可能缺少`pcntl`扩展 . 可以按照前面的编译安装 . event扩展也是 , 这里省略步骤 .

也可以使用Mac系统的brew安装 .

```
brew install php70
brew install php70-event
```

---

### Event扩展说明

Event扩展不是必须的 , 当业务需要支撑大于1000的并发连接时 , 推荐安装Event , 能够支持巨大的并发连接 . 如果业务并发连接比较低 , 例如1000以下并发连接 , 则可以不用安装 .

> Event是libevent的OOP封装 , 比libevent扩展提供了更完善的OOP结构和大量的内置可重用组件 , libevent不支持php 7及后续版本 , 已经逐步被pecl舍弃 , 其最新版本只维护到2013年且长期处于beta状态 .
>
> 所以 , 建议优先使用event而不是libevent扩展 . 广义上的libevent是指libevent这个事件库 , 但PHP中的一个扩展也称为libevent , 实际使用时要注意加以区分 .

Event扩展依赖libevent2库 , 安装event时可能遇到缺少依赖库的错误 . 安装库 :

```
yum install libevent2
```

如果有旧版就删除旧版再安装 , `yum remove libevent` .

然后再使用`pecl install event`安装即可 .

> 注意 : 一些php安装中可能没有pecl命令 , 这个命令集成在pear包中 , 编译php时需要启用pear包 . 别忘记 , 不要在confiure参数中添加`--without-pear`了



