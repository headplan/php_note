# ezSQL简单应用

```
include_once "ez_sql_core.php"; # 引入核心文件
include_once "ez_sql_mysql.php"; # 引入数据库文件
# 常用函数
$db = new ezSQL_mysql('root','123','db01','127.0.0.1'); # 连接数据库
$db->query($sql); # 返回bool,insert,update,select
$db->get_var($sql); # 返回数值
$db->get_row($sql); # 返回一行
$db->get_col($sql); # 返回一列
$db->get_results($sql); # 返回结果集,第二个参数ARRAY_A:关联数组,ARRAY_N:索引数组,不写输出对象,之后可以执行前面的函数却出一条
$db->select($dbname); # 返回整个库的结构,之后可以使用之前的函数获取数据,也可以用来选定新的数据库进行之前的操作
$db->debug(); # 输出结果
$db->vardump(); # 打印结果
$db->get_col_info(); # 获得一列的信息
$db->hide_errors(); # 关闭错误输出
$db->show_errors(); # 开启错误输出
$db->escape(); # 格式化任何非标准PHP查询,防注入

# 常用变量
$db->num_rows : 输出之前对数据库进行查询的影响行数
$db->insert_id : 从之前的insert查询中获取所生成的自增ID
$db->rows_affected : 获取之前所生效的INSERT,UPDATE,以及DELETE所影响的行数
$db->num_queries : 对当前代码所生效的查询进行追踪
$db->debug_all : 如果将这个变量设置为true,它将输出代码中所有的查询和结果
$db->cache_dir : 设置mysql结果的缓存路径,文件夹需要设置755以上
$db->cache_queries : 打开查询缓存
$db->cache_inserts : 打开插入缓存
$db->use_disk_cache : 打开全局磁盘缓存
$db->cache_timeout : 缓存时间,int,小时

```

