# Yaf

[http://php.net/manual/zh/book.yaf.php](http://php.net/manual/zh/book.yaf.php)

手册 : [http://www.laruence.com/manual/index.html](http://www.laruence.com/manual/index.html)

#### 运行时配置

| 名字 | 默认值 | 其他值 | 可修改范围 | 更新记录 |
| :--- | :--- | :--- | :--- | :--- |
| yaf.library | NULL | /your-path | PHP\_INI\_ALL | 全局类库的目录路径 |
| yaf.action\_prefer | 0 | 1 | PHP\_INI\_ALL | 打开的情况下,对于只有一段路由信息, 则优先设置Action, 否则优先设置Controller |
| yaf.lowcase\_path | 0 | 1 | PHP\_INI\_ALL | 是否忽略路径大小写,自动加载路径按照小写来对待 |
| yaf.use\_spl\_autoload | 0 | 1 | PHP\_INI\_ALL | 开启的情况下,Yaf在加载不成功的情况下,会继续让PHP的自动加载函数加载,从性能考虑,除非特殊情况,否则保持这个选项关闭 |
| yaf.forward\_limit | 5 |  | PHP\_INI\_ALL | forward最大嵌套深度 |
| yaf.name\_suffix | 1 |  | PHP\_INI\_ALL | 在处理Controller, Action, Plugin, Model的时候, 类名中关键信息是否是后缀式,比如UserModel,而在前缀模式下则是ModelUser |
| yaf.name\_separator | "" | \_ | PHP\_INI\_ALL | 在处理Controller, Action, Plugin, Model的时候, 前缀和名字之间的分隔符,默认为空,也就是UserPlugin,加入设置为"\_",则判断的依据就会变成:"User\_Plugin",这个主要是为了兼容ST已有的命名规范 |
| yaf.cache\_config | 0 |  | PHP\_INI\_SYSTEM |  |
| yaf.environ | product | debug | PHP\_INI\_SYSTEM | 线上产品设置为product |
| yaf.use\_namespace | 0 |  | PHP\_INI\_SYSTEM |  |



