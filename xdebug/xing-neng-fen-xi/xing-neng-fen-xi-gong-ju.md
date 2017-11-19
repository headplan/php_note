# 性能分析工具

这里主推Mac用的qcachegrind图形工具 . Linux可以使用kcachegrind , 还有网页版的webgrind :

```
https://github.com/jokkedk/webgrind
```

**安装**

```
brew install graphviz
brew install qcachegrind
```

> 安装过程会自动安装qt , 因为是qt版的 .

如果Call Graph\(函数调用关系图\)功能无法使用 , 是因为/usr/local/bin/dot的路径不对 , 创建软连即可 : 

```
ln -s /usr/local/bin/dot /usr/bin/dot
```

另外, PHP-FPM也提供了一个slowlog慢日志功能 , 超过request\_slowlog\_timeout配置的函数会被记录下来 .

```
php-fpm.conf:
slowlog = /path/to/slow.log
request_slowlog_timeout = 1
```



