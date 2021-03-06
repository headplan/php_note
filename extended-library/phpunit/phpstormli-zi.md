# PHPStorm例子

> 代码示例在Learning\_Testing中的phpstorm分支

**PHPUnit安装**

```
composer init
composer require phpunit/phpunit
```

**新建学生类Student**

```
namespace PHPStormDemo;
 
$this->id = $id;
$this->name = $name;
$this->email = $email;
```

**新建课程类Course**

```
namespace PHPStormDemo; 

$this->id = $id;
$this->name = $name;
$this->studentsLimit = $studentsLimit;
$this->beginTime = $beginTime;
$this->endTime = $endTime;
$this->students = []
```

#### **编写PHPUnit测试**

在创建测试类之前 , 这里先简单的了解一下PHPUnit .

我们编写的测试类 , 都要继承PHPUnit\_Framework\_TestCase类 , 新版本的自动加载方式\(PSR4\)可以直接写TestCase . 这样我们就可以直接在我们的单元测试方法中直接使用Assertions断言了 , 这里的测试单元方法的命名需要以test开头 , 后面接什么无所谓 , 一般会写我们要测试的方法的名字\(驼峰\) .

**Assertions\(断言\)**

断言为PHPUnit的主要功能 , 用来验证单元的执行结果是不是预期值 .

* assertTrue\(\) - 判断实际值是否为true
* assertEquals\(\) - 判断预期值和实际值是否相等 . 预期值是abc , 实际值是abx , 因为两个值不相等 , 所以这一个断言失败 , 会显示The string is not equal with orz的字串 . 
* assertCount\(\) - 判断预期数组大小 . 
* assertContains\(\) - 预期数组中有一个PHP字串的元素存在

这里的后面三个函数都是第一个参数是预期值 , 第二个参数是实际值 .

**Producer\(生产者\)与Consumer\(消费者\)**

单元测试之间可以存在依赖关系 , 她们通过在消费者单元中的`@depends 方法名`产生依赖 , 消费者通过参数接收生产者返回的值 . 并且如果生产者测试失败 , 依赖其的消费者也会被忽略执行 .

**Fixtures\(装置\)**

Fixture能协助建立测试时需要用到的测试环境 , 对象的建立等 . 在测试完后 , 把测试环境 , 对象析构掉 , 还原到初始化前的状态 .

主要通过setUp\(\)与tearDown\(\)分别来初始化测试与还原到初始化前的状态 .

**Data Providers\(数据提供者\)**

数据提供者 , 能提供多次的测试数据进行多次的测试 . 使用数据提供者 , 能让测试更简洁 . 因为可以将测试的断言与测试数据分开写 .

使用数据提供者的方式和依赖差不多 , 标注是`@dataProvider`返回数据也是从依赖方法的参数引入 , 支持数组形式 .

**新建课程测试类CourseTest**

```php
class CourseTest extends TestCase
{
    # 先声明两个成员你属性,分别用来存储我们的学生和课程对象
    private $course;
    private $student;

    # 创建Fixtures(装置),准备初始化对象
    public function setUp()
    {
        $courseId = 1;
        $courseName = '计算机';
        $courseStudentsLimit = 25;
        $courseBeginTime = '2017-11-01';
        $courseEndTime = '2017-11-15';
        $this->course = new Course($courseId, $courseName, $courseStudentsLimit, $courseBeginTime, $courseEndTime);

        $studentId = 1;
        $studentName = '小明';
        $studentEmail = '110@qq.com';
        $this->student = new Student($studentId, $studentName, $studentEmail);
    }

    public function tearDown()
    {
        $this->course = null;
        $this->student = null;
    }

    # 现在我们可以写测试用例了.
    # 首先是测试参加培训报名的SignUp方法的测试用例

    public function testSignUp()
    {
        # 参加课程培训
        $this->course->signUp($this->student);

        # 断言会参加课程的人数为1
        $this->assertEquals(1, $this->course->getStudentsCount());
        # 断言报名是否成功,也就是这个学生是否存在于课程类中记录报名的成员属性中
        $this->assertContains($this->student, $this->course->students);

        # 这里return的目的是为后面取消报名提供值
        return [$this->course, $this->student];
    }

    /**
     * @param $object
     * @depends testSignUp
     */
    public function testSingOut($object)
    {
        # 这里的$object就是前面测试报名成功时的课程和学生对象的状态
        # 所以这里可以直接测试取消报名
        $course = $object[0];
        $student = $object[1];

        # 取消报名
        $course->signOut($student);

        # 断言报名的学生已经等于0了
        $this->assertEquals(0, $course->getStudentsCount());
        # 断言学生对象已经不存在于课程报名记录属性中了
        $this->assertNotContains($student, $course->students);
    }

    /**
     * @param $courseId
     * @param $courseName
     * @param $courseStudentsLimit
     * @param $courseBeginTime
     * @param $courseEndTime
     * @dataProvider courseDataProvider
     */
    public function testStudentsLimit($courseId, $courseName, $courseStudentsLimit, $courseBeginTime, $courseEndTime)
    {
        # 这里测试每个课程报名的人数限制
        # 用到了@dataProvider标签,让断言和数据分开
        $course = new Course($courseId, $courseName, $courseStudentsLimit, $courseBeginTime, $courseEndTime);
        $students = 10;

        for ($student = 1; $student < $students; $student++) {
            $studentId = $student;
            $studentName = 'Student'.$studentId;
            $studentEmail = $studentName.'@qq.com';
            $_student = new Student($studentId, $studentName, $studentEmail);

            $signuped = $course->signUp($_student);

            if ($student > $courseStudentsLimit) {
                $this->assertFalse($signuped);
            } else {
                $this->assertTrue($signuped);
            }
        }
    }

    /**
     * @return array
     */
    public function courseDataProvider()
    {
        # 数据提供者
        $courseId = 1;
        $courseName = '计算机';
        $courseStudentsLimitNotFull = 5;
        $courseStudentsLimitFull = 20;
        $courseBeginTime = '2017-11-01';
        $courseEndTime = '2017-11-15';

        $data = [
            [
                $courseId,
                $courseName,
                $courseStudentsLimitNotFull,
                $courseBeginTime,
                $courseEndTime,
            ],
            [
                $courseId,
                $courseName,
                $courseStudentsLimitFull,
                $courseBeginTime,
                $courseEndTime,
            ],
        ];

        return $data;
    }

}
```

**异常测试**

异常的测试依然依靠@标签 , 在注释n内容中可以直接写异常类 , 异常msg和异常code , 单元测试方法触发异常会去判断异常参数是否等于给定的值 .

```php
<?php
# 先在原来的方法中抛出一个重复报名的异常
/**
 * @param \PHPStormDemo\Student $student
 * @throws \PHPStormDemo\CourseException
 */
public function exSignUp(Student $student): void
{
    if (array_key_exists($student->id, $this->students)) {
        throw new CourseException('Duplicated SignUp', CourseException::DUPLICATED_SIGNUP);
    }
}

# 简单的创建一个异常类,错误码为110,表示重复注册
<?php

namespace PHPStormDemo;

class CourseException extends \Exception
{
    const DUPLICATED_SIGNUP = 110;
}

# 测试用例,这里@标签后的内容就是前面抛出异常后的内容.还用到了setUp种的初始化数据
/**
 * @expectedException \PHPStormDemo\CourseException
 * @expectedExceptionMessage Duplicated SignUp
 * @expectedExceptionCode 110
 */
public function testDuplicatedSignUp()
{
    $this->course->signUp($this->student);
    $this->course->signUp($this->student);
}
```

#### 配置PHPUnit

在命令行运行PHPUnit时 , 可能需要加很多参数 , 例如--bootstrap引导的自动加载路径等 . 这些参数都是可配置的 , 使用phpunit.xml设置 . 例如 , 加入bootstrap和filter属性 :

```
<?xml version="1.0" encoding="UTF-8"?>
<phpunit bootstrap="../vendor/autoload.php" verbose="true">
    <testsuite>
        <directory suffix="Test.php">./tests</directory>
    </testsuite>

    <filter>
        <whitelist processUncoveredFilesFromWhitelist="true">
            <directory suffix=".php">./PHPStormDemo</directory>
        </whitelist>
    </filter>
</phpunit>
```

利用`--configuration`来指定之后 , 就可以直接运行干净的命令了 :

```
phpunit tests/CourseTest.php
```

---

#### 代码覆盖率\(Code Coverage\)

写好单元测试之后 , 该如何了解到哪些程序还没有经过测试 ? 目标程序被测试百分比有多少 ?

PHPUnit利用PHP CodeCoverage来计算程序代码覆盖率\(code coverage\) , 需要安裝 Xdebug .

新建一个文件夹coverage , 存放分析结果用的 , 然后直接运行命令即可 :

```
phpunit --coverage-html coverage/ tests/
```

或者在xml配置中配置

```
<?xml version="1.0" encoding="UTF-8"?>
<phpunit bootstrap="./vendor/autoload.php" verbose="true">
    <testsuite>
        <directory suffix="Test.php">./tests</directory>
    </testsuite>

    <filter>
        <whitelist processUncoveredFilesFromWhitelist="true">
            <directory suffix=".php">./PHPStormDemo</directory>
        </whitelist>
    </filter>

    <logging>
        <log type="coverage-html" target="./coverage" charset="UTF-8"/>
    </logging>
</phpunit>
```

然后运行命令

```
phpunit tests/CourseTest.php
```



