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





