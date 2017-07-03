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
# ===== [iconv] - 依赖libiconv
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



