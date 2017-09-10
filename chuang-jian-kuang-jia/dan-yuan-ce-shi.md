# 单元测试

> 引入PHPUnit .

创建PHPUnit的配置文件`phpunit.xml`

* `<phpunit>`元素的属性用于配置 PHPUnit 的核心功能
* 带有一个或多个`<testsuite>`子元素的`<testsuites>`元素用于将测试套件及测试用例组合出新的测试套件 . 
* `<groups>`元素及其`<include>`、`<exclude>`、`<group>`子元素用于从带有`@group`标注 . 
* `<filter>`元素及其子元素用于配置代码覆盖率报告所使用的白名单
* `<logging>`元素及其`<log>`子元素用于配置测试执行期间的日志记录
* `<listeners>`元素及其`<listener>`子元素用于在测试执行期间附加额外的测试监听器
* `<php>`元素及其子元素用于配置 PHP 设置、常量以及全局变量 . 同时也可用于向`include_path`前部置入内容 . 



