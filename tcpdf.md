# TCPDF

TCPDF具有以下特性:

1、支持页面页脚;

2、支持HTML标签代码;

3、支持jpg/png/gif/svg图形图像;

4、支持表格;

5、支持中文字符;

6、自动分页,自动页码等.

官网:[http://www.tcpdf.org](http://www.tcpdf.org/index.php)

Github:[https://github.com/tecnickcom/TCPDF](https://github.com/tecnickcom/TCPDF)

**使用**

1、require\_once导入tcpdf.php文件及相关配置信息;

2、实例化TCPDF;

3、设置PDF文档的格式,包括文档信息、页眉、页尾、字体、外间距、图片边框、分页等;

4、导入PDF文档的内容,可以是单行或多行简单字符串,也可以HTML格式的字符串等;

5、输出PDF文档.

```php
require_once('tcpdf.php');
# 初始化参数
# PDF_PAGE_ORIENTATION:设置文档打印格式(Portrait纵向和Landscape横向)'P'和'L'
# PDF_UNIT:设置页面单位.pt,点为单位.mm,毫米为单位.cm,厘米为单位.in,英尺为单位
# PDF_PAGE_FORMAT:设置打印格式,一般设置为A4.
# Unicode:为true,输入的文本为Unicode字符文本
# Encoding:设置编码格式,默认为utf-8
# Diskcache:为true,通过使用文件系统的临时缓存数据减少RAM的内存使用
$pdf = new TCPDF('P', 'mm', 'A4', true, 'UTF-8', false);

```



