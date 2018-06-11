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

利用`php -v`命令查看当前的PHP CLI的版本 , 根据版本下载PHP源代码 . http://php.net/releases/

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



