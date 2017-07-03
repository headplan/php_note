# 将php的session保存到内存中

默认启用的session扩展是以文件形式存储的 . 这里编译时添加`--with-mm`即可将session存储在内存中 .

修改配置文件 , 即可 :

```
session.save_handler = mm
```

这里收集了stackoverflow上的一个问题 :

[https://stackoverflow.com/questions/18337159/what-is-the-php-shared-memory-mm-with-mm-session-module](https://stackoverflow.com/questions/18337159/what-is-the-php-shared-memory-mm-with-mm-session-module)

> \(1\) 请注意, mm 的会话存储模块不能保证对同一会话的并发访问被正确锁定。使用基于共享内存的文件系统 \(如 solaris/linux 上的 tmpfs 或 bsd 上的开发/md\) 将会话存储在文件中可能更为合适, 因为它们已正确锁定。
>
> \(2\) 会话数据存储在内存中, 因此 web 服务器重新启动会删除它。

还有一个其他应用方式的文章 :

[http://blog.csdn.net/load\_life/article/details/7905718](http://blog.csdn.net/load_life/article/details/7905718)

> 今天一直在研究如何将session保存到内存中去，我们先说一下为什么要把session保存到内存中去，有什么好处呢？  
> 　　因为如果我们的[PHP](http://lib.csdn.net/base/php)程序中设置了session\_start\(\)，那么每个访问此文件的人都会产生一个session文件存在系统缓存中/tmp目录下，我们经常写的购物车或者会员系统经常要使用到session，所以如果一个流量很大的网站或者session保存时间设置很长的网站，就会在/tmp目录下建立大量session文件，虽然有时候这些文件大小仅仅为0，可是它确实还是占用了一个文件句柄，我有一台服务器因为设置的保存时间太长了，结果产生了120多万个session，占用了硬盘所有的文件句柄，导致再也不能建立新文件了，我用了35个小时来删除这120多万个session，只要你设置了session\_start\(\)，服务器会根据用户本地的cookie查找session的值，如果有保存session文件那么取回session的值，如果没有那么新建立一个session文件。可想而知，在120多万个文件中查找你要的文件，是多么恐怖的事情啊，我用ll命令等几分钟都没反应呢，所以太多的session文件势必造成系统运行缓慢，所以我想把session保存到内存中去，这样就可以大大加快session的读写速度，还可以减少硬盘的损耗，两全其美啊。哈哈  
> 　　我找到了很多文章说使用MM一个session使用共享内存的模块可以做这个工作，刚好ports里面也有，我就安装了一下，而且还按照说明重新编译了[php](http://lib.csdn.net/base/php)，并且把php.ini的session.save\_handler = files改成了session.save\_handler = mm，结果我不管怎么调用还是抱错说找不到save\_handler mm，这可愁死我了，搞了一个早上，也没找出问题所在。因为MM[测试](http://lib.csdn.net/base/softwaretest)未成功，我这里就不提供MM的安装步骤了。  
> 　　结果我在察看phpinfo的时候，偶尔在session那段发现了Registered save handlers = files user eaccelerator ，呵呵，原来是我安装了eaccelerator，可能它和mm不能共存吧。我把session.save\_handler = eaccelerator，重起apache后，运行正常。去/tmp下察看，确实不在生成新的session文件了，哈哈！  
> 　　另外需要提醒大家一点的就是，虽然session保存到内存中有种种好处，但是如果使用apachectl restart，那么内存中的session会丢失哦，所以不要动不动就重起apache。呵呵！
>
> 不知道eaccelerator和不会安装eaccelerator的
>
> 什么是 eAccelerator ？  
>   
> 　　eAccelerator 是一个开源并且免费的 PHP 加速器，优化器，编码器，同时也能够为 PHP 提供动态内容缓存。它能够将 PHP 脚本缓存为已编译状态以达到提升 PHP 脚本运行性能的目的，因此传统的预编译几乎被消除。eAccelerator 也能够优化 PHP 脚本以提升 PHP 脚本的执行速度。eAccelerator 可有效降低服务器负载并且提高 PHP 程序速度达 1-10 倍。  
>   
> 　　TurckMMCache 是 eAccelerator 的前身。  
> 　　\( [http://sourceforge\[.NET\]\(http://lib.csdn.net/base/dotnet\)/project/turckmm-cache/](http://sourceforge[.NET]%28http://lib.csdn.net/base/dotnet%29/project/turckmm-cache/) by Dmitry Stogov \)  
>   
> 　　eAccelerator 包含一个 PHP 编码器和加载器。您可以使用编码器对 .php 脚本进行编码，从而能够以非源代码方式发布您的 PHP 程序。经过编码的 PHP 程序可以运行在任何安装有PHP 解析环境和 eAccelerator 的站点上，由于编码后的 PHP 程序存储为已编译代码，并且已编译版本中不包含程序的源代码，因此，经过 eAccelerator 编码的 PHP 程序是不能被还原恢复的。当然，一些内部脚本可以被某些不同的反编译引擎工具（如 disassemblers, debuggers等）进行还原恢复，但这并非是微不足道的。  
>   
> 　　eAccelerator 与 Zend Optimizer 加载器兼容。在 php.ini 中，Zend Optimizer 必须在eAccelerator 之后加载。如果您的站点不运行任何经由 Zend 编码器编码的 PHP 脚本，那么我们并不推荐您在安装 eAccelerator 的服务器上安装 Zend Optimizer。  
>   
> 　　eAccelerator 不能运行于 CGI 模式下，但它可以运行于像 lighttpd 类似的 Fast-CGI模式。  
>   
> 　　以下是一些与 eAccelerator 具有相同功能的产品：  
> 　　- Zend Performance Suite \([http://www.zend.com\](http://www.zend.com\)\)  
> 　　- Alternative PHP Cache \([http://pecl.php\[.net\]\(http://lib.csdn.net/base/dotnet\)/package/APC\](http://pecl.php[.net]%28http://lib.csdn.net/base/dotnet%29/package/APC\)\)  
>   
> 　　eAccelerator已经是很常用的PHP平台预编译加速的手段了，安装后php执行速度大幅度提升，所以今天我们来学习从ports安装eAccelerator，并且配置好它。
>
> 引用\# cd /usr/ports/www/eaccelerator
>
>
>
> make
>
>
>
> make install clean
>
>
>
> mkdir /tmp/eaccelerator
>
>
>
> chown -R www /tmp/eaccelerator
>
>
>
> chmod 0700 /tmp/eaccelerator
>
>
>
> vi /usr/local/etc/php.ini
>
> 作为Zend扩展安装配置文件
>
> 引用zend\_extension="/usr/local/lib/php/20060613/eaccelerator.so"
>
>
>
> \[eaccelerator\]
>
>
>
> eaccelerator.shm\_size="16"
>
>
>
> eaccelerator.cache\_dir="/tmp/eaccelerator"
>
>
>
> eaccelerator.enable="1"
>
>
>
> eaccelerator.optimizer="1"
>
>
>
> eaccelerator.check\_mtime="1"
>
>
>
> eaccelerator.debug="0"
>
>
>
> eaccelerator.filter=""
>
>
>
> eaccelerator.shm\_max="0"
>
>
>
> eaccelerator.shm\_ttl="0"
>
>
>
> eaccelerator.shm\_prune\_period="0"
>
>
>
> eaccelerator.shm\_only="0"
>
>
>
> eaccelerator.compress="1"
>
>
>
> eaccelerator.compress\_level="9"
>
> 作为独立模块安装配置文件
>
> 引用\[eaccelerator\]
>
>
>
> extension=”eaccelerator.so”
>
>
>
> eaccelerator.shm\_size=”0″
>
>
>
> eaccelerator.cache\_dir=”/tmp/eaccelerator”
>
>
>
> eaccelerator.enable=”1″
>
>
>
> eaccelerator.optimizer=”1″
>
>
>
> eaccelerator.check\_mtime=”1″
>
>
>
> eaccelerator.debug=”0″
>
>
>
> eaccelerator.filter=”"
>
>
>
> eaccelerator.shm\_max=”0″
>
>
>
> eaccelerator.shm\_ttl=”0″
>
>
>
> eaccelerator.shm\_prune\_period=”0″
>
>
>
> eaccelerator.shm\_only=”0″
>
>
>
> eaccelerator.compress=”1″
>
>
>
> eaccelerator.compress\_level=”9″
>
> 这个是ArthurXF，session全部放进内存中的配置，倾情奉献给大家：
>
> 引用\[eaccelerator\]
>
>
>
> eaccelerator.shm\_size="32"
>
>
>
> eaccelerator.cache\_dir="/tmp/eaccelerator"
>
>
>
> eaccelerator.enable="1"
>
>
>
> eaccelerator.optimizer="1"
>
>
>
> eaccelerator.check\_mtime="1"
>
>
>
> eaccelerator.debug="0"
>
>
>
> eaccelerator.filter=""
>
>
>
> eaccelerator.shm\_max="0"
>
>
>
> eaccelerator.shm\_ttl="900"
>
>
>
> eaccelerator.shm\_prune\_period="1800"
>
>
>
> eaccelerator.shm\_only="0"
>
>
>
> eaccelerator.compress="1"
>
>
>
> eaccelerator.compress\_level="9"
>
>
>
> eaccelerator.sessions="shm"
>
>
>
> eaccelerator.keys="shm"
>
>
>
> eaccelerator.content="shm"
>
> 如果上面两个配置加上去了，都没能启动eAccelerator，那么执行下面的命令：  
> vi /usr/local/etc/php/extensions.ini  
> 在最后加入下面的语句即可  
> extension=eaccelerator.so  
>   
> 安装好了，重起apache，看看phpinfo，是不是多了下面的内容啊？  
> with eAccelerator v0.9.5, Copyright \(c\) 2004-2006 eAccelerator, by eAccelerator  
> 这样就说明安装好了。  
>   
> 下面看看eAccelerator配置选项  
>   
> eaccelerator.shm\_size  
> 指定 eAccelerator 能够使用的共享内存数量，单位：MB。  
> "0" 代表[操作系统](http://lib.csdn.net/base/operatingsystem)默认。默认值为 "0"。  
>   
> eaccelerator.cache\_dir  
> 用户磁盘缓存的目录。eAccelerator 在该目录中存储预编译代码、session 数据、内容等。  
> 相同的数据也可以存储于共享内存中（以获得更快的存取速度）。默认值为 "/tmp/eaccelerator"。  
>   
> eaccelerator.enable  
> 开启或关闭 eAccelerator。"1" 为开启，"0" 为关闭。默认值为 "1"。  
>   
> eaccelerator.optimizer  
> 开启或关闭内部优化器，可以提升代码执行速度。"1" 为开启，"0" 为关闭。默认值为 "1"。  
>   
> eaccelerator.debug  
> 开启或关闭调试日志记录。"1" 为开启，"0" 为关闭。默认值为 "0"。  
>   
> eaccelerator.check\_mtime  
> 开启或关闭 PHP 文件改动检查。"1" 为开启，"0" 为关闭。如果您想要在修改后重新编译 PHP  
> 程序则需要设置为 "1"。默认值为 "1"。  
>   
> eaccelerator.filter  
> 判断哪些 PHP 文件必须缓存。您可以指定缓存和不缓存的文件类型（如 "\*.php \*.phtml"等）  
> 如果参数以 "!" 开头，则匹配这些参数的文件被忽略缓存。默认值为 ""，即，所有 PHP 文件  
> 都将被缓存。  
>   
> eaccelerator.shm\_max  
> 当使用 " eaccelerator\_put\(\) " 函数时禁止其向共享内存中存储过大的文件。该参数指定允许  
> 存储的最大值，单位：字节 \(10240, 10K, 1M\)。"0" 为不限制。默认值为 "0"。  
>   
> eaccelerator.shm\_ttl  
> 当 eAccelerator 获取新脚本的共享内存大小失败时，它将从共享内存中删除所有在  
> 最后 "shm\_ttl" 秒内无法存取的脚本缓存。默认值为 "0"，即：不从共享内春中删除  
> 任何缓存文件。  
>   
> eaccelerator.shm\_prune\_period  
> 当 eAccelerator 获取新脚本的共享内存大小失败时，他将试图从共享内存中删除早于  
> "shm\_prune\_period" 秒的缓存脚本。默认值为 "0"，即：不从共享内春中删除  
> 任何缓存文件。  
>   
> eaccelerator.shm\_only  
> 允许或禁止将已编译脚本缓存在磁盘上。该选项对 session 数据和内容缓存无效。默认  
> 值为 "0"，即：使用磁盘和共享内存进行缓存。  
>   
> eaccelerator.compress  
> 允许或禁止压缩内容缓存。默认值为 "1"，即：允许压缩。  
>   
> eaccelerator.compress\_level  
> 指定内容缓存的压缩等级。默认值为 "9"，为最高等级。  
>   
> eaccelerator.name\_sapce  
> 一个所有键\(keys\)的前缀字符串。如果设置该前缀字符串则允许 .htaccess 或者 主配置  
> 文件在相同主机上运行两个相同的键名。  
>   
> eaccelerator.keys  
> eaccelerator.sessions  
> eaccelerator.content  
> 判断哪些键\(keys\)、session 数据和内容将被缓存。可用参数值为：  
> "shm\_and\_disk" - 同时在共享内存和磁盘中缓存数据（默认值）；  
> "shm" - 如果共享内存用尽或者数据容量大于 "eaccelerator.shm\_max"  
> 则在共享内存或磁盘中缓存数据；  
> "shm\_only" - 仅在共享内存中缓存数据；  
> "disk\_only" - 仅在磁盘中缓存数据；  
> "none" - 禁止缓存数据。  
>   
>   
> eAccelerator 应用程序接口\(API\)  
>   
> eaccelerator\_put\($key, $value, $ttl=0\)  
> 将 $value 存储在共享内存中，并存储 $tll 秒。  
>   
> eaccelerator\_get\($key\)  
> 从共享内存中返回 eaccelerator\_put\(\) 函数所存储的缓存数值，如果不存在或者已经  
> 过期，则返回 null。  
>   
> eaccelerator\_rm\($key\)  
> 从共享内存中删除 $key。  
>   
> eaccelerator\_gc\(\)  
> 删除所有过期的键\(keys\)  
>   
> eaccelerator\_lock\($lock\)  
> 创建一个指定名称的锁\(lock\)。该锁可以通过 eaccelerator\_unlock\(\) 函数解除，在请求  
> 结束时也会自动解锁。例如：
>
> &lt;
>
> ?php
>
>
>
> eaccelerator\_lock\("count"\);
>
>
>
> eaccelerator\_put\("count",eaccelerator\_get\("count"\)+1\)\);
>
>
>
> ?
>
> &gt;
>
> eaccelerator\_unlock\($lock\)  
> 解除指定名称的锁\(lock\)。  
>   
> eaccelerator\_set\_session\_handlers\(\)  
> 安装 eAccelerator session 句柄。  
> 从 PHP 4.2.0 以后，您可以通过设置 php.ini 中的 "session.save\_handler=eaacelerator"  
> 安装 eAccelerator 句柄。  
>   
> eaccelerator\_cache\_output\($key, $eval\_code, $ttl=0\)  
> 在共享内存中缓存 $eval\_code 的输出，缓存 $ttl 秒。  
> 可以调用 mmcach\_rm\(\) 函数删除相同 $key 的输出。例如：
>
> &lt;
>
> ?php eaccelerator\_cache\_output\('test', 'echo time\(\); phpinfo\(\);', 30\); ?
>
> &gt;
>
> eaccelerator\_cache\_result\($key, $eval\_code, $ttl=0\)  
> 在共享内存中缓存 $eval\_code 的结果，缓存 $ttl 秒。  
> 可以调用 mmcach\_rm\(\) 函数删除相同 $key 的结果。例如：
>
> &lt;
>
> ?php eaccelerator\_cache\_output\('test', 'time\(\)." Hello";', 30\); ?
>
> &gt;
>
> eaccelerator\_cache\_page\($key, $ttl=0\)  
> 缓存整个页面，且缓存 $ttl 秒。例如：
>
> &lt;
>
> ?php
>
>
>
> eaccelerator\_cache\_page\($\_SERVER\['PHP\_SELF'\].'?GET='.serialize\($\_GET\),30\);
>
>
>
> echo time\(\);
>
>
>
> phpinfo\(\);
>
>
>
> ?
>
> &gt;
>
> eaccelerator\_rm\_page\($key\)  
> 从缓存中删除由 eaccelerator\_cache\_page\(\) 函数创建的相同 $key 的页。  
>   
> eaccelerator\_encode\($filename\)  
> 返回 $filename 文件经过编译后的编码。  
>   
> eaccelerator\_load\($code\)  
> 加载被 eaccelerator\_encode\(\) 函数编码过的脚本。



