# 整理 - 默认启用的扩展

```
# ===== [libxml] - 依赖libxml2,libxml2-devel
--with-libxml-dir=DIR
# ===== [SQLite3]
# ===== [ctype]
# ===== [DOM] - 依赖libxml2,libxml2-devel
--with-libxml-dir=DIR
# ===== [fileinfo] - 通常禁用,因为小内存无法编译
# ===== [Filter] - 依赖pcre,pcre-devel
--with-pcre-dir
# ===== [hash]
# ===== [iconv] - 依赖libiconv,这个依赖在glibc,glibc-devel中.iconv是内建glibc的组成部分
# 这里的glibc可能会转码支持的不完善,所以一般会换成编译安装libiconv,需要用到下面的配置参数
--with-iconv-dir=DIR
# ===== [json]
# ===== [opcache]
# ===== [Hugepages]
# ===== [PDO] - 一般会使用PDO兼容一些数据库,常用的有MySQL(有依赖)
--without-pdo-sqlite=DIR 默认支持sqlite
# ===== [Phar]
# ===== [Posix]
# ===== [session] - 默认开启的,还可以配置把会话保存到共享内存中
--with-mm=DIR 参考相关文章
# ===== [SimpleXML] - 依赖libxml2,libxml2-devel
--with-libxml-dir=DIR
# ===== [Tokenizer] - 内置解析器的接口
# ===== [XML] - 依赖libxml2,libxml2-devel
--with-libexpat-dir=DIR 这是一个过时的依赖不用安装
# ===== [XMLReader]
# ===== [XMLWriter]
--with-libxml-dir=DIR 上面三个XML相关扩展都依赖libxml2
```



