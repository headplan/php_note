# 包管理器

https://packagist.org/

https://pear.php.net/

https://pecl.php.net/

利用包和库管理工具,更好的管理代码中使用的包.

Composer是一个依赖管理器,可以用来安装所使用的库需要的代码,包括外部包.利用Composer,在项目中加入适当版本的代码会很容易,不论是项目开始时还是决定将一个包升级到更新版本时,都可以使用Composer安装所需的代码.Packagist是寻找Composer包的主要基地,它提供了数以万计的PHP包.

PEAR表示PHP扩展与应用存储库,这是一个协同工作的开源类集合.可以使用PEAR类解析XML,实现认证系统,生成CAPTCHA,发送带附件的MIME邮件以及完成其他很多常见或不常见的任务.

PECL表示PHP扩展社区库,是一系列用C编写的PHP扩展,这些扩展与PHP主版本发布的扩展类似,不过会有特定的关注点,例如MongoDB数据库驱动程序或OAuth1.0扩展.

Composer、PEAR、PECL共同提供了一个丰富的高质量的可重用代码集,但他们之间还有所不同,Composer并未与PHP捆绑,所以可以为各个项目安装同一个包的不同版本的副本,而PEAR和PECL如果已经有了HTTP2.0的库就无法提供HTTP1.8版本的库.

