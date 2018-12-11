# PEAR/PECL

#### Mac上安装PECL

下载pear

```
curl -O http://pear.php.net/go-pear.phar
```

安装pear

```
sudo php -d detect_unicode=0 go-pear.phar
```

执行以上命令后会进行安装过程，会有一些配置选项

```
输入1 回车 配置pear路径为:/usr/local/pear
输入4 回车 配置命令路径为:/usr/local/bin
```

回车两次 , 其他让其默认 , 安装完成

可以通过命令检查pear安装是否成功`pear version`或`pecl version`



