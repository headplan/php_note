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
# 设置文档信息
$pdf->SetCreator('创建者');
$pdf->SetAuthor('作者');
$pdf->SetTitle('标题');
$pdf->SetSubject('副标题');
$pdf->SetKeywords('关键字');

# 设置页眉/页脚信息
# 第一个参数图片
# 第二个参数距离左侧距离
# 第三个参数标题
# 第四个参数副标题
# 第五个和第六个参数是颜色
$pdf->SetHeaderData('', 30, 'Medbanks.cn', 'Medbanks', array(0,64,255), array(0,64,128));
$pdf->SetFooterData(array(0,64,0), array(0,64,128));
# 设置页眉和页脚字体
$pdf->SetHeaderFont(array('stsongstdlight', '', '10'));  
$pdf->SetFooterFont(array('helvetica', '', '8'));
# 设置页眉和页脚间距
$pdf->SetHeaderMargin(5);
$pdf->SetFooterMargin(10);
# 删除预定义的打印 页眉/页脚
$pdf->SetPrintHeader(false);
$pdf->SetPrintFooter(false);

# 设置默认等宽字体
# PDF_FONT_MONOSPACED:courier
$pdf->SetDefaultMonospacedFont(PDF_FONT_MONOSPACED);

# 设置间距
# PDF_MARGIN_LEFT:左
# PDF_MARGIN_TOP:上
# PDF_MARGIN_RIGHT:右
# Keepmargins为true时,覆盖默认的PDF边幅
$pdf->SetMargins(PDF_MARGIN_LEFT, PDF_MARGIN_TOP, PDF_MARGIN_RIGHT);

# 设置单元格边距
Left:左边距
Top:顶部边距
Right:右边距
Bottom:底部边距
$pdf->setCellPaddings(0, 0, 0, 0);

# GetX获得当前的横坐标,GetY获得当前的纵坐标
$pdf->GetX();
$pdf->GetY();

# 移动坐标
# SetX移动横坐标
# SetY横坐标自动移动到左边距的距离,然后移动纵坐标
# SetXY移动横坐标跟纵坐标
# X:横坐标,可设为$pdf->GetX()+数字
# Y:纵坐标,可设为$pdf->GetY()+数字
# Rtloff:true,左上角会一直作为坐标轴的原点
# Resetx:true,重设横坐标
$pdf->SetX($x, $rtloff=false);
$pdf->SetY($y, $resetx=true, $rtloff=false);
$pdf->SetXY($x, $y, $rtloff=false)

# 设置线条风格
# Width：设置线条粗细
# Cap：设置线条的两端形状
# Join：设置线条连接的形状
# Dash：设置虚线模式
# Color：设置线条颜色，一般设置为黑色，如：array(0, 0, 0)
$pdf->SetLineStyle(array('width' => 0.2, 'cap' => 'butt', 'join' => 'miter', 'dash' => '0', 'color' => array(0, 0,0)));

# 画一条线
# x1:线条起点x坐标
# y1:线条起点y坐标
# x2:线条终点x坐标
# y2:线条终点y坐标
# style:SetLineStyle的效果一样
$pdf->Line($x1, $y1, $x2, $y2, $style=array());

# 执行一个换行符,横坐标自动移动到左边距的距离,纵坐标换到下一行
# H:设置下行跟上一行的距离,默认的话,高度为最后一个单元格的高度
# Cell:true,添加左或右或上的间距到横坐标
$pdf->Ln($h='', $cell=false);

# 设置自动分页符
$pdf->SetAutoPageBreak(TRUE, PDF_MARGIN_BOTTOM);

# 设置图像比例因子
$pdf->setImageScale(PDF_IMAGE_SCALE_RATIO);

# 设置一些语言相关的字符串
$pdf->setLanguageArray("xx");

# 设置字体
# 字体类型,如helvetica(Helvetica)黑体,times(Times-Roman)罗马字体
# 风格,B粗体,I斜体,underline下划线等
# 字体大小
$pdf->SetFont('times', 'I', 20);

# 增加一个页面
# Orientation:orientation属性用来设置文档打印格式.Landscape为横式打印,Portrait为纵向打印.
# Format:设置此页面的打印格式
# Keepmargins:true,以当前的边幅代替默认边幅来重写页面边幅
# Tocpage:true,所添加的页面将被用来显示内容表
$pdf->AddPage();
```



