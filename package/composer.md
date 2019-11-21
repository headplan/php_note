# Composer

### 简介

Composer是PHP的一个依赖管理工具.它允许你申明项目所依赖的代码库,它会在你的项目中为你安装他们.

**依赖管理**

Composer不是一个包管理器.虽然它涉及"packages"和"libraries",但它在每个项目的基础上进行管理,在你项目的某个目录中\(例如`vendor)`进行安装.默认情况下它不会在全局安装任何东西.因此,这仅仅是一个依赖管理.

**声明依赖关系**

所需要做的就是创建一个`composer.json`文件,在其中描述项目的依赖关系.

**系统要求**

运行Composer需要PHP 5.3.2+以上版本.一些敏感的PHP设置和编译标志也是必须的,但对于任何不兼容项安装程序都会抛出警告.从包的来源直接安装还需要git,svn或hg.Composer支持多平台.

#### 安装

**下载地址**

[https://getcomposer.org/download/](https://getcomposer.org/download/)

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === 'a5c698ffe4b8e849a443b120cd5ba38043260d5c4023dbf93e1558871f1f07f58274fc6f4c93bcfd858c6bd0775cd8d1') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

**中国镜像**

[https://pkg.phpcomposer.com/](https://pkg.phpcomposer.com/)

> 执行composer update会根据composer.json相关依赖更新安装
>
> 执行composer install会根据composer.lock相关依赖安装



