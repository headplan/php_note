# 扩展【Extensions】

这里先对shared和static静态模式 , 进一步描述 . 共享模式shared的意思是linux中.so文件的意思 , 也有Win系统中的.dll文件的意思 . 其理解的方式可以看做是一个exe应用程序 , 里面调用了一部分扩展函数 , 如果这些函数已经静态的安装在其中 . 那么这个程序就可以在任意其他系统上直接运行起来 . 如果这个程序调用了共享模式的扩展 , 那程序里值记录了调用这个扩展的一个路径或者地址 , 调用时候去寻找这个扩展 , 本机安装了 , 正常运行 , 但是换了其他机器 , 没有这个扩展 , 就无法运行了 .

为了方便记录 , 把扩展部分的内容分块描述 , 但顺序不变 .

```
Extensions(扩展):

  --with-EXTENSION=shared[,PATH]

    NOTE:并非所有扩展都能编译成共享方式.

    例子:如何把扩展编译成共享模式                   
    Example: --with-foobar=shared,/usr/local/foobar/

      o Builds the foobar extension as shared extension.
      o foobar package install prefix is /usr/local/foobar/
```

**扩展列表**

```
# ===== [libxml]
# ===== [OpenSSL]
# ===== [PREC]
# ===== [SQLite3]
# ===== [Zlib]
# ===== [BC Math]
# ===== [Bzip2]
# ===== [Calendar]
# ===== [ctype]
# ===== [cURL]
.10
# ===== [DBA]
# ===== [DOM]
# ===== [enchant]
# ===== [EXIF]
# ===== [fileinfo]
# ===== [Filter]
# ===== [FTP]
# ===== [GD imaging(gd)]
# ===== [GetText]
# ===== [GMP]
.20
# ===== [Mhash]
# ===== [hash]
# ===== [iconv]
# ===== [IMAP]
# ===== [Firebird/InterBase]
# ===== [Internationalization(intl)]
# ===== [International Components for Unicode(ICU)]
# ===== [json]
# ===== [LDAP]
# ===== [Multibyte String Functions(mbstring)]
.30
```

```
--disable-all          关闭默认为启用的所有扩展功能

# ===== [libxml]
--disable-libxml       禁用libxml支持
--with-libxml-dir=DIR  LIBXML:用于指定安装libxml库文件的位置
# 此扩展默认为启用,编译时可通过disable选项禁用
# Debian/Ubuntu 需安装 libxml2,libxml2-dev 依赖包
# Redhat/CentOS 需安装 libxml2,libxml2-devel 依赖包

# ===== [OpenSSL]
--with-openssl=DIR     启用openssl支持 (OpenSSL版本号必须大于等于 1.0.1)
--with-kerberos=DIR    OPENSSL:包含kerberos支持
--with-system-ciphers  OPENSSL:用系统自带的密码清单(cipher list)去替代硬编码(hard coded)
# PHP 7.1 所需的 openssl 版本是 >= 1.0.1
# PHP 7.0 所需的 openssl 版本是 >= 0.9.8
# PHP 5.6 所需的 openssl 版本是 >= 0.9.6
# PHP 5.5 所需的 openssl 版本是 >= 0.9.6
# PHP 5.4 所需的 openssl 版本是 >= 0.9.6
# PHP 5.3 所需的 openssl 版本是 >= 0.9.6

# ===== [PREC]
--with-pcre-regex=DIR  引用PCRE兼容的正则表达式库,目录是PCRE的安装目录
--with-pcre-jit        引用PCRE兼容的jit编译器(jit实时编译)

# ===== [SQLite3]
--without-sqlite3=DIR  不开启sqlite3支持. DIR是SQLite3的安装路径.

# ===== [Zlib]
--with-zlib=DIR        开启ZLIB支持(ZLIB版本号必须大于等于 1.0.9)
--with-zlib-dir=<DIR>  定义ZLIB的安装路径
# Debian/Ubuntu 需安装 zlib1g-dev 依赖包
# Redhat/CentOS 需安装 zlib-devel 依赖包

# ===== [BC Math]
--enable-bcmath        启用bcmatch函数支持(公元前风格精度数学)

# ===== [Bzip2]
--with-bz2=DIR         开启BZip2支持
# Debian/Ubuntu 需安装 libbz2-dev 依赖包
# Redhat/CentOS 需安装 bzip2-devel 依赖包

# ===== [Calendar]
--enable-calendar      启用日历转换支持

# ===== [ctype]
--disable-ctype        禁用ctype功能
# 此扩展默认为启用,编译时可禁用

# ===== [cURL]
--with-curl=DIR        启用cURL支持
# Debian/Ubuntu 需安装 libcurl4-gnutls-dev 依赖包
# Redhat/CentOS 需安装 curl-devel 依赖包
```

```
# ===== [DBA]
--enable-dba           构架捆绑模块的DBA.要建立扩展的共享模块使用--enable-dba=shared参数.
--with-qdbm=DIR        DBA: QDBM support
--with-gdbm=DIR        DBA: GDBM support
--with-ndbm=DIR        DBA: NDBM support
--with-db4=DIR         DBA: Oracle Berkeley DB 4.x or 5.x support
--with-db3=DIR         DBA: Oracle Berkeley DB 3.x support
--with-db2=DIR         DBA: Oracle Berkeley DB 2.x support
--with-db1=DIR         DBA: Oracle Berkeley DB 1.x support/emulation
--with-dbm=DIR         DBA: DBM support
--with-tcadb=DIR       DBA: Tokyo Cabinet abstract DB support
--without-cdb=DIR      DBA: CDB support (bundled)
--disable-inifile      DBA: INI support (bundled)
--disable-flatfile     DBA: FlatFile support (bundled)
# 该参数会默认自带3个参数
# −−with-cdb,−−enable-inifile,inifile-flatfile
# 若要禁止,则需通过参数−−without-cdb=DIR,−−disable-inifile,−−disable-flatfile实现

# ===== [DOM]
--disable-dom Disable DOM support
--with-libxml-dir=DIR DOM: libxml2 install prefix
# 此扩展默认为启用.编译时可通过−−disable-dom禁用.

# ===== [enchant]
--with-enchant=DIR Include enchant support.
GNU Aspell version 1.1.3 or higher required.
# Debian/Ubuntu 需安装 libenchant-dev,libpspell-dev 依赖包
# Redhat/CentOS 需安装 enchant-devel,aspell-devel 依赖包

# ===== [EXIF]
--enable-exif Enable EXIF (metadata from images) support

# ===== [fileinfo]
--disable-fileinfo Disable fileinfo support
# 此扩展默认为启用,fileinfo support.编译时可通过下列选项禁用:−−disable-fileinfo
# 注意:在内存比较小的机器上编译此扩展时可能会失败,因此内存加SWAP的容量小于480MB时就不要安装了.

# ===== [Filter]
--disable-filter Disable input filter support
--with-pcre-dir FILTER: pcre install prefix
# 此扩展默认为启用,input filter support.编译时可通过下列选项禁用:−−disable-filter
# 另如果要给此扩展指定PCRE安装目录的话,则还有第二个编译参数.

# ===== [FTP]
--enable-ftp Enable FTP support
--with-openssl-dir=DIR FTP: openssl install prefix
# 通过指定编译参数−−enable-ftp安装.Enable FTP support
# 安装该扩展还有个参数−−with-openssl-dir=DIR FTP: openssl install prefix,可不指定,则使用系统自带openssl库

# ===== [GD imaging(gd)]
--with-gd=DIR Include GD support. DIR is the GD library base
install directory BUNDLED
--with-webp-dir=DIR GD: Set the path to libwebp install prefix
--with-jpeg-dir=DIR GD: Set the path to libjpeg install prefix
--with-png-dir=DIR GD: Set the path to libpng install prefix
--with-zlib-dir=DIR GD: Set the path to libz install prefix
--with-xpm-dir=DIR GD: Set the path to libXpm install prefix
--with-freetype-dir=DIR GD: Set the path to FreeType 2 install prefix
--enable-gd-native-ttf GD: Enable TrueType string function
--enable-gd-jis-conv GD: Enable JIS-mapped Japanese font support
# 通过指定编译参数.这是一个打包式的依赖,需要依赖后面列出的安装包.
# −−with-webp-dir=DIR(PHP 7.0, 7.1 only)
# PHP5.4、PHP5.5、PHP5.6 则还有个−−with-vpx-dir=DIR
# Debian/Ubuntu 需安装 libwebp-dev, libjpeg-dev, libpng-dev, libxpm-dev, libfreetype6-dev, libvpx-dev 依赖包
# Redhat/CentOS 需安装 libwebp-devel, libjpeg-devel, libpng-devel, libXpm-devel, freetype-devel, libvpx-devel 依赖包

# ===== [GetText]
--with-gettext=DIR Include GNU gettext support
# 通过指定编译参数安装.
# Debian/Ubuntu 需安装 gettext 依赖包
# Redhat/CentOS 需安装 gettext,gettext-devel 依赖包

# ===== [GMP]
--with-gmp=DIR Include GNU MP support
# 通过指定编译参数安装.
# Debian/Ubuntu 需安装 libgmp-dev 依赖包
# Redhat/CentOS 需安装 gmp-devel 依赖包
```

```
# ===== [Mhash]
--with-mhash=DIR Include mhash support
# 启用Mhash.Mhash是基于离散数学原理的不可逆向的php加密方式扩展库
# 这里需要先给Linux安装mhash扩展http://mhash.sourceforge.net/
# tar zxvf mhash-0.9.9.tar.gz
# cd mhash-0.9.9
# ./configure
# make install
# 安装时-–with-mhash=/usr/local/include即可

# ===== [hash]
--disable-hash Disable hash support
# 此扩展默认为启用,编译时可通过此选项禁用

# ===== [iconv]
--without-iconv=DIR Exclude iconv support
# 此扩展默认为启用,编译时可通过此选项禁用

# ===== [IMAP]
--with-imap=DIR Include IMAP support. DIR is the c-client install prefix
--with-kerberos=DIR IMAP: Include Kerberos support. DIR is the Kerberos install prefix
--with-imap-ssl=DIR IMAP: Include SSL support. DIR is the OpenSSL install prefix
# 通过指定编译参数安装
# 注意:编译此选项时,PHP需指定libc-client.a所在目录

# ===== [Firebird/InterBase]
--with-interbase=DIR Include Firebird support. DIR is the Firebird base
install directory /opt/firebird
# InterBase是一种关系数据管理系统
# 这里的路径是Firebird的默认安装路径/opt/firebird

# ===== [Internationalization(intl)]
--enable-intl Enable internationalization support
# PHP intl是国际化扩展,是ICU库的一个包装器.所以在安装PHP intl扩展前要先安装ICU库.
# 安装icu
# cd /usr/local/src/
# wget http://download.icu-project.org/files/icu4c/56.1/icu4c-56_1-src.tgz
# tar -xzf icu4c-56_1-src.tgz
# cd icu/source
# ./configure -prefix=/usr/local/icu
# make && make install
# 安装intl
# cd /usr/local/src/lnmp/src/
# tar -xzf php-7.0.1.tar.gz
# cd php-7.0.1/ext/intl
# /usr/local/php/bin/phpize
# ./configure –with-php-config=/usr/local/php/bin/php-config –with-icu-dir=/usr/local/icu
# make && make install
# echo ‘extension = intl.so’ >> /usr/local/php/etc/php.ini
# service php-fpm restart

# ===== [International Components for Unicode(ICU)]
--with-icu-dir=DIR Specify where ICU libraries and headers can be found
# ICU是一套稳定成熟,功能强大,轻便易用和跨平台支持Unicode的开发包
# 需要先安装ICU组件

# ===== [json]
--disable-json Disable JavaScript Object Serialization support
# 此扩展默认为启用,编译时可通过此选项禁用

# ===== [LDAP]
--with-ldap=DIR Include LDAP support
--with-ldap-sasl=DIR LDAP: Include Cyrus SASL support
# LDAP是轻量目录访问协议
# Debian/Ubuntu 需安装 libldap-2.4-2, libldap2-dev 依赖包
# Redhat/CentOS 需安装 openldap, openldap-devel 依赖包

# ===== [Multibyte String Functions(mbstring)]
--enable-mbstring Enable multibyte string support
--disable-mbregex MBSTRING: Disable multibyte regex support
--disable-mbregex-backtrack
MBSTRING: Disable multibyte regex backtrack check
--with-libmbfl=DIR MBSTRING: Use external libmbfl. DIR is the libmbfl base
install directory BUNDLED
--with-onig=DIR MBSTRING: Use external oniguruma. DIR is the oniguruma install prefix.
If DIR is not set, the bundled oniguruma will be used
# 通过指定编译参数安装
# libmbfl对mbstring是必要的
# Debian/Ubuntu 需安装 libonig2,libonig-dev 依赖包
# Redhat/CentOS 需安装 oniguruma,oniguruma-devel 依赖包
```

```
# ===== [mcrypt]
--with-mcrypt=DIR Include mcrypt support
# 通过指定编译参数安装
# Debian/Ubuntu 需安装 libmcrypt-dev 依赖包
# Redhat/CentOS 需编译安装libmcrypt和mcrypt.如果安装了EPEL的话,则需安装libmcrypt-devel依赖包.

# ===== [mysqli]
--with-mysqli=FILE Include MySQLi support. FILE is the path
to mysql_config. If no value or mysqlnd is passed
as FILE, the MySQL native driver will be used
--enable-embedded-mysqli
MYSQLi: Enable embedded support
Note: Does not work with MySQL native driver!
--with-mysql-sock=SOCKPATH
MySQLi/PDO_MYSQL: Location of the MySQL unix socket pointer.
If unspecified, the default locations are searched
# 指定系统里安装的MYSQL的目录下的mysql_config文件路径

# ===== [OCI8]
--with-oci8=DIR Include Oracle Database OCI8 support. DIR defaults to $ORACLE_HOME.
Use --with-oci8=instantclient,/path/to/instant/client/lib
to use an Oracle Instant Client installation
# 系统里安装了Oracle数据库的话,则指定为$ORACLE_HOME
# 否则需安装Oracle Instant Client,指定为/path/to/instant/client/lib

# ===== [ODBCVER]
--with-odbcver=HEX Force support for the passed ODBC version. A hex number is expected, default 0x0350.
Use the special value of 0 to prevent an explicit ODBCVER to be defined.
# ODBCVER决定你使用的ODBC API,ODBCVER=0x0351表示使用的是3.5.1版本的ODBC,ODBCVER=0x0200表示使用ODBC 2

# ===== [ADABAS D]
--with-adabas=DIR Include Adabas D support /usr/local
# ADABAS D是一种新的关系型数据库

# ===== [SAP DB]
--with-sapdb=DIR Include SAP DB support /usr/local
# SAP DB 原来叫ADABAS,关系型数据库

# ===== [Solid]
--with-solid=DIR Include Solid support /usr/local/solid
# Solid高可用轻量级关系数据库

# ===== [IBM DB2]
--with-ibm-db2=DIR Include IBM DB2 support /home/db2inst1/sqllib
# IBM DB2关系型数据库

# ===== [ODBCRouter]
--with-ODBCRouter=DIR Include ODBCRouter.com support /usr
# ODBCRouter关系数据库数据交换组件

# ===== [Empress]
--with-empress=DIR Include Empress support \$EMPRESSPATH
(Empress Version >= 8.60 required)
--with-empress-bcs=DIR
Include Empress Local Access support \$EMPRESSPATH
(Empress Version >= 8.60 required)
# Empress嵌入式实时数据库
```

```
# ===== [Birdstep]
--with-birdstep=DIR Include Birdstep support /usr/local/birdstep
# Birdstep RDM Server数据库

# ===== [ODBC]
--with-custom-odbc=DIR Include user defined ODBC support. DIR is ODBC install base
directory /usr/local. Make sure to define CUSTOM_ODBC_LIBS and
have some odbc.h in your include dirs. f.e. you should define
following for Sybase SQL Anywhere 5.5.00 on QNX, prior to
running this configure script:
CPPFLAGS=\"-DODBC_QNX -DSQLANY_BUG\"
LDFLAGS=-lunix
CUSTOM_ODBC_LIBS=\"-ldblib -lodbc\"
# 自定义安装的ODBC数据库

# ===== [iODBC]
--with-iodbc=DIR Include iODBC support /usr/local
# 一个独立开发的数据库

# ===== [Easysoft OOB(esoob)]
--with-esoob=DIR Include Easysoft OOB support /usr/local/easysoft/oob/client
# 包括Easysoft OOB的支持.DIR是OOB的基本安装目录,默认为/usr/local/easysoft/oob/client

# ===== [unixODBC]
--with-unixODBC=DIR Include unixODBC support /usr/local
# unixodbc是一个来连接数据库的组件

# ===== [DBMaker]
--with-dbmaker=DIR Include DBMaker support
# DBMaker 为功能强大的关联式资料库管理系统
```

```
--disable-opcache Disable Zend OPcache support
--disable-opcache-file Disable file based caching
--disable-huge-code-pages
Disable copying PHP CODE pages into HUGE PAGES
--enable-pcntl Enable pcntl support (CLI/CGI only)
--disable-pdo Disable PHP Data Objects support
--with-pdo-dblib=DIR PDO: DBLIB-DB support. DIR is the FreeTDS home directory
--with-pdo-firebird=DIR PDO: Firebird support. DIR is the Firebird base
install directory /opt/firebird
--with-pdo-mysql=DIR PDO: MySQL support. DIR is the MySQL base directory
If no value or mysqlnd is passed as DIR, the
MySQL native driver will be used
--with-zlib-dir=DIR PDO_MySQL: Set the path to libz install prefix
--with-pdo-oci=DIR PDO: Oracle OCI support. DIR defaults to \$ORACLE_HOME.
Use --with-pdo-oci=instantclient,prefix,version
for an Oracle Instant Client SDK.
For example on Linux with 11.2 RPMs use:
--with-pdo-oci=instantclient,/usr,11.2
With 10.2 RPMs use:
--with-pdo-oci=instantclient,/usr,10.2.0.4
--with-pdo-odbc=flavour,dir
PDO: Support for 'flavour' ODBC driver.
include and lib dirs are looked for under 'dir'.

'flavour' can be one of: ibm-db2, iODBC, unixODBC, generic
If ',dir' part is omitted, default for the flavour
you have selected will be used. e.g.:

--with-pdo-odbc=unixODBC

will check for unixODBC under /usr/local. You may attempt
to use an otherwise unsupported driver using the \"generic\"
flavour. The syntax for generic ODBC support is:

--with-pdo-odbc=generic,dir,libname,ldflags,cflags

When built as 'shared' the extension filename is always pdo_odbc.so
```

```
  --with-pdo-pgsql=DIR    PDO: PostgreSQL support.  DIR is the PostgreSQL base
                          install directory or the path to pg_config
  --without-pdo-sqlite=DIR
                          PDO: sqlite 3 support.  DIR is the sqlite base
                          install directory BUNDLED
  --with-pgsql=DIR        Include PostgreSQL support.  DIR is the PostgreSQL
                          base install directory or the path to pg_config
  --disable-phar          Disable phar support
  --disable-posix         Disable POSIX-like functions
  --with-pspell=DIR       Include PSPELL support.
                          GNU Aspell version 0.50.0 or higher required
  --with-libedit=DIR      Include libedit readline replacement (CLI/CGI only)
  --with-readline=DIR     Include readline support (CLI/CGI only)
  --with-recode=DIR       Include recode support
  --disable-session       Disable session support
  --with-mm=DIR           SESSION: Include mm support for session storage
  --enable-shmop          Enable shmop support
  --disable-simplexml     Disable SimpleXML support
  --with-libxml-dir=DIR   SimpleXML: libxml2 install prefix
  --with-snmp=DIR         Include SNMP support
  --with-openssl-dir=DIR  SNMP: openssl install prefix
  --enable-soap           Enable SOAP support
  --with-libxml-dir=DIR   SOAP: libxml2 install prefix
  --enable-sockets        Enable sockets support
  --enable-sysvmsg        Enable sysvmsg support
  --enable-sysvsem        Enable System V semaphore support
  --enable-sysvshm        Enable the System V shared memory support
  --with-tidy=DIR         Include TIDY support
  --disable-tokenizer     Disable tokenizer support
  --enable-wddx           Enable WDDX support
  --with-libxml-dir=DIR   WDDX: libxml2 install prefix
  --with-libexpat-dir=DIR WDDX: libexpat dir for XMLRPC-EPI (deprecated)
  --disable-xml           Disable XML support
  --with-libxml-dir=DIR   XML: libxml2 install prefix
  --with-libexpat-dir=DIR XML: libexpat install prefix (deprecated)
  --disable-xmlreader     Disable XMLReader support
  --with-libxml-dir=DIR   XMLReader: libxml2 install prefix
  --with-xmlrpc=DIR       Include XMLRPC-EPI support
  --with-libxml-dir=DIR   XMLRPC-EPI: libxml2 install prefix
  --with-libexpat-dir=DIR XMLRPC-EPI: libexpat dir for XMLRPC-EPI (deprecated)
  --with-iconv-dir=DIR    XMLRPC-EPI: iconv dir for XMLRPC-EPI
  --disable-xmlwriter     Disable XMLWriter support
  --with-libxml-dir=DIR   XMLWriter: libxml2 install prefix
  --with-xsl=DIR          Include XSL support.  DIR is the libxslt base
                          install directory (libxslt >= 1.1.0 required)
  --enable-zip            Include Zip read/write support
  --with-zlib-dir=DIR     ZIP: Set the path to libz install prefix
  --with-pcre-dir         ZIP: pcre install prefix
  --with-libzip=DIR       ZIP: use libzip
  --enable-mysqlnd        Enable mysqlnd explicitly, will be done implicitly
                          when required by other extensions
  --disable-mysqlnd-compression-support
                          Disable support for the MySQL compressed protocol in mysqlnd
  --with-zlib-dir=DIR     mysqlnd: Set the path to libz install prefix
```

---

```
Extensions:

  --with-EXTENSION=shared[,PATH]

    NOTE: Not all extensions can be build as 'shared'.

    Example: --with-foobar=shared,/usr/local/foobar/

      o Builds the foobar extension as shared extension.
      o foobar package install prefix is /usr/local/foobar/


  --disable-all           Disable all extensions which are enabled by default

  --disable-libxml        Disable LIBXML support
  --with-libxml-dir=DIR   LIBXML: libxml2 install prefix
  --with-openssl=DIR      Include OpenSSL support (requires OpenSSL >= 1.0.1)
  --with-kerberos=DIR     OPENSSL: Include Kerberos support
  --with-system-ciphers   OPENSSL: Use system default cipher list instead of hardcoded value
  --with-pcre-regex=DIR   Include Perl Compatible Regular Expressions support.
                          DIR is the PCRE install prefix BUNDLED
  --with-pcre-jit         Enable PCRE JIT functionality
  --without-sqlite3=DIR   Do not include SQLite3 support. DIR is the prefix to
                          SQLite3 installation directory.
  --with-zlib=DIR         Include ZLIB support (requires zlib >= 1.0.9)
  --with-zlib-dir=<DIR>   Define the location of zlib install directory
  --enable-bcmath         Enable bc style precision math functions
  --with-bz2=DIR          Include BZip2 support
  --enable-calendar       Enable support for calendar conversion
  --disable-ctype         Disable ctype functions
  --with-curl=DIR         Include cURL support
  --enable-dba            Build DBA with bundled modules. To build shared DBA
                          extension use --enable-dba=shared
  --with-qdbm=DIR         DBA: QDBM support
  --with-gdbm=DIR         DBA: GDBM support
  --with-ndbm=DIR         DBA: NDBM support
  --with-db4=DIR          DBA: Oracle Berkeley DB 4.x or 5.x support
  --with-db3=DIR          DBA: Oracle Berkeley DB 3.x support
  --with-db2=DIR          DBA: Oracle Berkeley DB 2.x support
  --with-db1=DIR          DBA: Oracle Berkeley DB 1.x support/emulation
  --with-dbm=DIR          DBA: DBM support
  --with-tcadb=DIR        DBA: Tokyo Cabinet abstract DB support
  --without-cdb=DIR       DBA: CDB support (bundled)
  --disable-inifile       DBA: INI support (bundled)
  --disable-flatfile      DBA: FlatFile support (bundled)
  --disable-dom           Disable DOM support
  --with-libxml-dir=DIR   DOM: libxml2 install prefix
  --with-enchant=DIR      Include enchant support.
                          GNU Aspell version 1.1.3 or higher required.
  --enable-exif           Enable EXIF (metadata from images) support
  --disable-fileinfo      Disable fileinfo support
  --disable-filter        Disable input filter support
  --with-pcre-dir         FILTER: pcre install prefix
  --enable-ftp            Enable FTP support
  --with-openssl-dir=DIR  FTP: openssl install prefix
  --with-gd=DIR           Include GD support.  DIR is the GD library base
                          install directory BUNDLED
  --with-webp-dir=DIR      GD: Set the path to libwebp install prefix
  --with-jpeg-dir=DIR     GD: Set the path to libjpeg install prefix
  --with-png-dir=DIR      GD: Set the path to libpng install prefix
  --with-zlib-dir=DIR     GD: Set the path to libz install prefix
  --with-xpm-dir=DIR      GD: Set the path to libXpm install prefix
  --with-freetype-dir=DIR GD: Set the path to FreeType 2 install prefix
  --enable-gd-native-ttf  GD: Enable TrueType string function
  --enable-gd-jis-conv    GD: Enable JIS-mapped Japanese font support
  --with-gettext=DIR      Include GNU gettext support
  --with-gmp=DIR          Include GNU MP support
  --with-mhash=DIR        Include mhash support
  --disable-hash          Disable hash support
  --without-iconv=DIR     Exclude iconv support
  --with-imap=DIR         Include IMAP support. DIR is the c-client install prefix
  --with-kerberos=DIR     IMAP: Include Kerberos support. DIR is the Kerberos install prefix
  --with-imap-ssl=DIR     IMAP: Include SSL support. DIR is the OpenSSL install prefix
  --with-interbase=DIR    Include Firebird support.  DIR is the Firebird base
                          install directory /opt/firebird
  --enable-intl           Enable internationalization support
  --with-icu-dir=DIR      Specify where ICU libraries and headers can be found
  --disable-json          Disable JavaScript Object Serialization support
  --with-ldap=DIR         Include LDAP support
  --with-ldap-sasl=DIR    LDAP: Include Cyrus SASL support
  --enable-mbstring       Enable multibyte string support
  --disable-mbregex       MBSTRING: Disable multibyte regex support
  --disable-mbregex-backtrack
                          MBSTRING: Disable multibyte regex backtrack check
  --with-libmbfl=DIR      MBSTRING: Use external libmbfl.  DIR is the libmbfl base
                          install directory BUNDLED
  --with-onig=DIR         MBSTRING: Use external oniguruma. DIR is the oniguruma install prefix.
                          If DIR is not set, the bundled oniguruma will be used
  --with-mcrypt=DIR       Include mcrypt support
  --with-mysqli=FILE      Include MySQLi support.  FILE is the path
                          to mysql_config.  If no value or mysqlnd is passed
                          as FILE, the MySQL native driver will be used
  --enable-embedded-mysqli
                          MYSQLi: Enable embedded support
                          Note: Does not work with MySQL native driver!
  --with-mysql-sock=SOCKPATH
                          MySQLi/PDO_MYSQL: Location of the MySQL unix socket pointer.
                          If unspecified, the default locations are searched
  --with-oci8=DIR         Include Oracle Database OCI8 support. DIR defaults to $ORACLE_HOME.
                          Use --with-oci8=instantclient,/path/to/instant/client/lib
                          to use an Oracle Instant Client installation
  --with-odbcver=HEX      Force support for the passed ODBC version. A hex number is expected, default 0x0350.
                             Use the special value of 0 to prevent an explicit ODBCVER to be defined.
  --with-adabas=DIR       Include Adabas D support /usr/local
  --with-sapdb=DIR        Include SAP DB support /usr/local
  --with-solid=DIR        Include Solid support /usr/local/solid
  --with-ibm-db2=DIR      Include IBM DB2 support /home/db2inst1/sqllib
  --with-ODBCRouter=DIR   Include ODBCRouter.com support /usr
  --with-empress=DIR      Include Empress support \$EMPRESSPATH
                          (Empress Version >= 8.60 required)
  --with-empress-bcs=DIR
                          Include Empress Local Access support \$EMPRESSPATH
                          (Empress Version >= 8.60 required)
  --with-birdstep=DIR     Include Birdstep support /usr/local/birdstep
  --with-custom-odbc=DIR  Include user defined ODBC support. DIR is ODBC install base
                          directory /usr/local. Make sure to define CUSTOM_ODBC_LIBS and
                          have some odbc.h in your include dirs. f.e. you should define
                          following for Sybase SQL Anywhere 5.5.00 on QNX, prior to
                          running this configure script:
                            CPPFLAGS=\"-DODBC_QNX -DSQLANY_BUG\"
                            LDFLAGS=-lunix
                            CUSTOM_ODBC_LIBS=\"-ldblib -lodbc\"
  --with-iodbc=DIR        Include iODBC support /usr/local
  --with-esoob=DIR        Include Easysoft OOB support /usr/local/easysoft/oob/client
  --with-unixODBC=DIR     Include unixODBC support /usr/local
  --with-dbmaker=DIR      Include DBMaker support
  --disable-opcache       Disable Zend OPcache support
  --disable-opcache-file  Disable file based caching
  --disable-huge-code-pages
                          Disable copying PHP CODE pages into HUGE PAGES
  --enable-pcntl          Enable pcntl support (CLI/CGI only)
  --disable-pdo           Disable PHP Data Objects support
  --with-pdo-dblib=DIR    PDO: DBLIB-DB support.  DIR is the FreeTDS home directory
  --with-pdo-firebird=DIR PDO: Firebird support.  DIR is the Firebird base
                          install directory /opt/firebird
  --with-pdo-mysql=DIR    PDO: MySQL support. DIR is the MySQL base directory
                          If no value or mysqlnd is passed as DIR, the
                          MySQL native driver will be used
  --with-zlib-dir=DIR     PDO_MySQL: Set the path to libz install prefix
  --with-pdo-oci=DIR      PDO: Oracle OCI support. DIR defaults to \$ORACLE_HOME.
                          Use --with-pdo-oci=instantclient,prefix,version
                          for an Oracle Instant Client SDK.
                          For example on Linux with 11.2 RPMs use:
                            --with-pdo-oci=instantclient,/usr,11.2
                          With 10.2 RPMs use:
                            --with-pdo-oci=instantclient,/usr,10.2.0.4
  --with-pdo-odbc=flavour,dir
                          PDO: Support for 'flavour' ODBC driver.
              include and lib dirs are looked for under 'dir'.

              'flavour' can be one of:  ibm-db2, iODBC, unixODBC, generic
              If ',dir' part is omitted, default for the flavour
              you have selected will be used. e.g.:

                --with-pdo-odbc=unixODBC

              will check for unixODBC under /usr/local. You may attempt
              to use an otherwise unsupported driver using the \"generic\"
              flavour.  The syntax for generic ODBC support is:

                --with-pdo-odbc=generic,dir,libname,ldflags,cflags

              When built as 'shared' the extension filename is always pdo_odbc.so
  --with-pdo-pgsql=DIR    PDO: PostgreSQL support.  DIR is the PostgreSQL base
                          install directory or the path to pg_config
  --without-pdo-sqlite=DIR
                          PDO: sqlite 3 support.  DIR is the sqlite base
                          install directory BUNDLED
  --with-pgsql=DIR        Include PostgreSQL support.  DIR is the PostgreSQL
                          base install directory or the path to pg_config
  --disable-phar          Disable phar support
  --disable-posix         Disable POSIX-like functions
  --with-pspell=DIR       Include PSPELL support.
                          GNU Aspell version 0.50.0 or higher required
  --with-libedit=DIR      Include libedit readline replacement (CLI/CGI only)
  --with-readline=DIR     Include readline support (CLI/CGI only)
  --with-recode=DIR       Include recode support
  --disable-session       Disable session support
  --with-mm=DIR           SESSION: Include mm support for session storage
  --enable-shmop          Enable shmop support
  --disable-simplexml     Disable SimpleXML support
  --with-libxml-dir=DIR   SimpleXML: libxml2 install prefix
  --with-snmp=DIR         Include SNMP support
  --with-openssl-dir=DIR  SNMP: openssl install prefix
  --enable-soap           Enable SOAP support
  --with-libxml-dir=DIR   SOAP: libxml2 install prefix
  --enable-sockets        Enable sockets support
  --enable-sysvmsg        Enable sysvmsg support
  --enable-sysvsem        Enable System V semaphore support
  --enable-sysvshm        Enable the System V shared memory support
  --with-tidy=DIR         Include TIDY support
  --disable-tokenizer     Disable tokenizer support
  --enable-wddx           Enable WDDX support
  --with-libxml-dir=DIR   WDDX: libxml2 install prefix
  --with-libexpat-dir=DIR WDDX: libexpat dir for XMLRPC-EPI (deprecated)
  --disable-xml           Disable XML support
  --with-libxml-dir=DIR   XML: libxml2 install prefix
  --with-libexpat-dir=DIR XML: libexpat install prefix (deprecated)
  --disable-xmlreader     Disable XMLReader support
  --with-libxml-dir=DIR   XMLReader: libxml2 install prefix
  --with-xmlrpc=DIR       Include XMLRPC-EPI support
  --with-libxml-dir=DIR   XMLRPC-EPI: libxml2 install prefix
  --with-libexpat-dir=DIR XMLRPC-EPI: libexpat dir for XMLRPC-EPI (deprecated)
  --with-iconv-dir=DIR    XMLRPC-EPI: iconv dir for XMLRPC-EPI
  --disable-xmlwriter     Disable XMLWriter support
  --with-libxml-dir=DIR   XMLWriter: libxml2 install prefix
  --with-xsl=DIR          Include XSL support.  DIR is the libxslt base
                          install directory (libxslt >= 1.1.0 required)
  --enable-zip            Include Zip read/write support
  --with-zlib-dir=DIR     ZIP: Set the path to libz install prefix
  --with-pcre-dir         ZIP: pcre install prefix
  --with-libzip=DIR       ZIP: use libzip
  --enable-mysqlnd        Enable mysqlnd explicitly, will be done implicitly
                          when required by other extensions
  --disable-mysqlnd-compression-support
                          Disable support for the MySQL compressed protocol in mysqlnd
  --with-zlib-dir=DIR     mysqlnd: Set the path to libz install prefix
```



