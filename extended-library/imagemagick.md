# ImageMagick

ImageMagick是一套功能强大、稳定而且开源的工具集和开发包 , 可以用来读、写和处理超过89种基本格式的图片文件 , 包括流行的TIFF、JPEG、GIF、 PNG、PDF以及PhotoCD等格式 . 利用ImageMagick , 可以根据**web应用程序**的需要动态生成图片 , 还可以对一个\(或一组\)图片进行改变大小、旋转、锐化、减色或增加特效等操作 , 并将操作的结果以相同格式或其它格式保存 , 对图片的操作 , 即可以通过命令行进行 , 也可以用C/C++、Perl、Java、PHP、Python或Ruby编程来完成 . 同时ImageMagick提供了一个高质量的2D工具包 , 部分支持SVG . ImageMagic的主要精力集中在性能 , 减少bug以及提供稳定的API和ABI上 .

**相关网站**

[https://imagemagick.org/](https://imagemagick.org/) - 官网

[https://www.w3cschool.cn/imagemagick\_use/](https://www.w3cschool.cn/imagemagick_use/) - 使用手册

[http://pecl.php.net/package/imagick](http://pecl.php.net/package/imagick) - PHP扩展

[http://php.net/manual/en/book.imagick.php](https://www.php.net/manual/zh/book.imagick.php) - PHP手册

#### 安装

**二进制安装**

```
$ rpm -Uvh ImageMagick-7.0.9-27.x86_64.rpm
$ rpm -Uvh ImageMagick-libs-7.0.9-27.x86_64.rpm
```

缺少依赖可以从[https://fedoraproject.org/wiki/EPEL/zh-cn](https://fedoraproject.org/wiki/EPEL/zh-cn)安装 .

```
cd $HOME
tar xvzf ImageMagick.tar.gz
```

可以复制到这里 , 设置环境变量

```
/usr/local/bin/
```

然后就可以运行了 .

```
magick logo: logo.gif
convert --version
identify -version
```

**编译安装**

```
tar xvzf ImageMagick.tar.gz
$ cd ImageMagick-7.0.9 
$ ./configure 
$ make && make install
```

可能还需要动态链接一下 , 如果报错的话

```
$ convert --version
convert: error while loading shared libraries: libMagickCore-7.Q16HDRI.so.6: cannot open shared object file: No such file or directory
```

```
ldconfig /usr/local/lib
```

安装可能还会需要一些扩展 , 根据提示安装即可 , 根据官网提供的手册安装 .

[https://imagemagick.org/script/download.php](https://imagemagick.org/script/download.php)

[https://imagemagick.org/script/install-source.php](https://imagemagick.org/script/install-source.php)

#### 安装PHP扩展Imagick.so

下载解压 : [http://pecl.php.net/package/imagick](#)

然后进入文件夹 , 执行phpize , 再编译安装就可以了

```
make && make install
```

安装完了后要修改php的配置文件 , 添加上imagick.so , 重启php-fpm , 不再冗述 .

---

#### 使用ImageMagick读取转换pdf文件为png图片

这是最近遇到的问题 , 开始时一直无法读取文件

```
$im->readImage($pdf);
```

后来直接使用ImageMagick命令行读取才发现报错

    $ convert ./06442c5680b395e56e6281717f8f96ee.pdf %d.png
    convert: FailedToExecuteCommand `'gs' -sstdout=%stderr -dQUIET -dSAFER -dBATCH -dNOPAUSE -dNOPROMPT -dMaxBitmap=500000000 -dAlignToPixels=0 -dGridFitTT=2 '-sDEVICE=pam' -dTextAlphaBits=4 -dGraphicsAlphaBits=4 '-r72x72' -dUseCIEColor  '-sOutputFile=/tmp/magick-2889502xw9VvEBehq%d' '-f/tmp/magick-28895MAEMdPGaWDJA' '-f/tmp/magick-28895SfJzbPNc2acL'' (1) @ error/pdf.c/InvokePDFDelegate/291.
    convert: no images defined `%d.png' @ error/convert.c/ConvertImageCommand/3275.

这里的gs , 就是GhostScript . 安装之后 , 就能读取到PDF文件了 .

#### 安装GhostScript

**相关地址**

官网 : [https://www.ghostscript.com/](https://www.ghostscript.com/)

文档 : [https://www.ghostscript.com/documentation.html](https://www.ghostscript.com/documentation.html)

下载地址 : [https://www.ghostscript.com/documentation.html](https://www.ghostscript.com/documentation.html)

![](/assets/ghostscript.png)

下载[Ghostscript AGPL Release](https://github.com/ArtifexSoftware/ghostpdl-downloads/releases/download/gs950/ghostscript-9.50-linux-x86_64.tgz)版本 , 注意匹配系统 . 

下载到服务器 , 解压Copy一份到/usr/bin , 全局可用 . 到此 , 解决了PDF读取的问题 . 

关于PHP扩展Imagick相关api , 可以查看php官方手册 . 

