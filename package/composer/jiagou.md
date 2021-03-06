# 架构

### JSON schema

使用validate命令就可以验证json有效性.

JSON格式化文档:http:\/\/json-schema.org\/

composer的格式化:https:\/\/github.com\/composer\/composer\/blob\/master\/res\/composer-schema.json

### Root包

"Root包"是指由`composer.json`定义的在你项目根目录的包.这是composer.json定义你项目所需的主要条件.\(简单的说,你自己的项目就是一个Root包\)

某些字段仅适用于"Root包"上下文.config字段就是其中一个例子.只有"Root包"可以定义.在依赖包中定义的config字段将被忽略,这使得config字段只有"Root包"可用\(root-only\).

克隆了其中的一个依赖包,直接在其上开始工作,那么它就变成了"Root包".一个资源包是不是"Root包",取决于它的上下文.

例:如果你的项目依赖monolog库,那么你的项目就是"Root包".但是,如果你从GitHub上克隆了monolog为它修复bug,那么此时monolog就是"Root包".

### 属性

* name - 包名

  * 包的名称,它包括供应商名称和项目名称.例,

    * monolog\/monolog

    * igorw\/event-source




* **对于要发布的包,它是必写的.**

* description - 描述

  * 包的简短描述,一般只有一行.

  * **对于要发布的包,它是必写的.**



* version - 版本

  * **版本不是必须的,并且建议忽略.**

  * 它应该符合'X.Y.Z'或者'vX.Y.Z'的形式,`-dev`、`-patch`、`-alpha`、`-beta` 或 `-RC` 这些后缀是可选的.在后缀之后也可以再跟上一个数字.例如:

    * 1.0.0-alpha3
    * 1.0.0-beta2
    * 1.0.0-RC5

  * 注意 - Packagist使用VCS仓库,因此`version`定义的版本号必须是真实准确的.



* type - 安装类型

  * library - 默认类型,仅将文件复制到`vendor`目录
  * project - 表示当前包是一个项目,而不是一个库.使用IDE创建新的工作区时,可以为其提供一个项目列表的初始化.
  * metapackage - 当一个空的包,包含依赖并且需要触发依赖的安装,不会对系统写入额外的文件.因此不需要source.
  * composer-plugin - 这是一个composer插件包,它有一个自定义安装类型,可以为其它包提供一个installer.详细查看自定义安装类型.
  * 一般仅在需要自定义安装逻辑时才使用,建议忽略,因为默认为library.

* keywords - 关键字

  * 与当前包相关的关键词数组.用于搜索过滤用.可选.

* homepage - 项目主页\(URL地址\)

  * **可选**

* time - 版本发布时间

  * 格式:必须符合 `YYYY-MM-DD` 或 `YYYY-MM-DD HH:MM:SS` 格式
  * **可选**

* license - 许可协议

  * 包的许可协议,字符串或字符串数组.
  * 许可协议标识符参考:https:\/\/spdx.org\/licenses\/
  * 几种书写格式

    * "MIT"
    * \["MIT","GPL-3.0+"\] 和 "\(MIT or GPL-3.0+\)"表示多个许可协议供选择
    * "\(MIT and GPL-3,0+\)"表示结合使用

  * **建议使用**



* authors - 作者

  * 对象数组

    * name - 姓名
    * email - 邮箱
    * homepage - 主页
    * role - 担任角色

  * **建议使用**



* support - 支持

  * 项目支持的相关信息对象
  * 对象必须包含一下信息

    * email - 项目支持email地址
    * issue - 跟踪问题的URL地址
    * forum - 论坛地址
    * wiki - wiki地址
    * irc - IRC聊天频道地址
    * source - 网址浏览或下载资源

  * **可选**



* Package links - 包名到版本的映射对象

  * require - 必须的软件包,除非列出这些都依赖都被满足,否则不会完成安装.
  * require-dev\(root only\) - 这个列表是为了开发或测试等目的,额外的依赖.ROOT包默认会安装.不过也开始用--no-dev跳过\(install或update时\).
  * conflict - 此列表中的包与当前包的版本冲突,不允许同时被安装.版本范围可以这样写`<1.0|>=1.1`
  * replace - 这个列表中的包将被当前包取代,用于声明需要替换的包.
  * provide - 用于说明本包实现了某个包的接口.

* suggest - 用于说明建议安装的包,格式和require一样,只是后面的约束变成了描述.

* autoload - 支持四种自动加载方式

  * PSR-4\(推荐\):

    * 支持将命名空间映射到路径
    * 命名空间结尾的\不可省略
    * 执行install或update时,加载信息写入vendor\/composer\/**autoload\_psr4.php**文件
    * 解析指定路径下的所有命名空间,将命名空间设置为空串
    * 对应关系:name2\space\Foo类对应路径path2\/space\/Foo.php,**name2不出现在路径中**
    * 类名下划线无特殊含义

  * PSR-0:

    * 支持将命名空间映射到路径
    * 命名空间结尾的\不可省略
    * 执行install或update时,加载信息写入vendor\/composer\/**autoload\_namespace.php**文件
    * 解析指定路径下的所有命名空间,将命名空间设置为空串
    * 对应关系:name2\space\Foo类对应路径path2\/name2\/space\/Foo.php
    * 类名下划线会被转换为目录分隔符\/

  * Classmap:支持将数组中的路径下的文件进行自动加载.

    * 如果增加新文件,需要执行dump-autoload命令重新生成映射文件
    * 生成的映射文件vendor\/composer\/autoload\_classmap.php

  * Files:支持数组中的文件进行自动加载.

    * 文件的路径相对于项目的根目录
    * 需要将所有文件都写进配置



* autoload-dev\(root only\) - 这和require-dev类似,用于开发和测试目的设置的自动加载

* include-path - 一个追加到include\_path中的列表

* target-dir - 定义当前包安装的目标文件夹\(弃用\).仅为了更好的兼容psr-0的自动加载方式.

* minimum-stability \(root-only\) - 通过稳定性过滤包的默认行为.会对每个包的所有版本进行稳定性检查,低于设定的最低稳定性的版本,将在解决依赖关系时被忽略.如果有特殊需求可以在require或require-dev中设定&添加.

  * 稳定性标示:
    * dev
    * alpha
    * beta
    * RC
    * stable\(稳定\)



* prefer-stable \(root-only\) - 当此选项被激活时\(true\),Composer将优先使用更稳定的包版本.

* repositories \(root-only\) - 使用自定义的包资源库.

* config \(root-only\)

* scripts \(root-only\)

* extra

* bin

* archive


