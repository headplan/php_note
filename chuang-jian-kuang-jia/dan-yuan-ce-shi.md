# 单元测试

> 引入PHPUnit . 更多内容查看扩展类库中的PHPUnit专题

创建PHPUnit的配置文件`phpunit.xml`

* `<phpunit>`元素的属性用于配置 PHPUnit 的核心功能
* 带有一个或多个`<testsuite>`子元素的`<testsuites>`元素用于将测试套件及测试用例组合出新的测试套件 . 
* `<groups>`元素及其`<include>`、`<exclude>`、`<group>`子元素用于从带有`@group`标注 . 
* `<filter>`元素及其子元素用于配置代码覆盖率报告所使用的白名单
* `<logging>`元素及其`<log>`子元素用于配置测试执行期间的日志记录
* `<listeners>`元素及其`<listener>`子元素用于在测试执行期间附加额外的测试监听器
* `<php>`元素及其子元素用于配置 PHP 设置、常量以及全局变量 . 同时也可用于向`include_path`前部置入内容 . 

```
<?xml version="1.0" encoding="UTF-8"?>
<phpunit
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:noNamespaceSchemaLocation="http://schema.phpunit.de/5.1/phpunit.xsd"
    backupGlobals="false"
    backupStaticAttributes="false"
    bootstrap="bootstrap/autoload.php"
    colors="true"
    convertErrorsToExceptions="true"
    convertNoticesToExceptions="true"
    convertWarningsToExceptions="true"
    processIsolation="false"
    stopOnFailure="false"
>
    <testsuites>
        <testsuite name="Test Suite">
            <directory>./tests</directory>
        </testsuite>
    </testsuites>

    <filter>
        <whitelist processUncoveredFilesFromWhitelist="true">
            <directory suffix=".php">./app</directory>
            <directory suffix=".php">./src</directory>
        </whitelist>
    </filter>
</phpunit>
```

配置文件对一些PHPunit选项进行了必要的默认定义 , 自动加载器被用于启动测试 , 而测试文件将被存放在`/tests/`目录下 . 



