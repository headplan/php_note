# php-enum应用

> https:\/\/github.com\/myclabs\/php-enum

该类库灵感来自SqlEnum,主要是因为SqlEnum没有集成到PHP,要单独安装.

使用枚举类常量有以下优点:

* 类型提示
* 丰富枚举方法:格式化,解析等
* 扩展要添加新值的枚举
* 可得到所有值的列表

这个类不是为了替换常量,仅仅是为了当其具有一定意义时使用.

**安装**

```
composer require myclabs/php-enum
```

**使用**

```
use Myclabs\Enum\Enum;

class Action extends Enum
{
    const VIEW = 'view';
    const EDIT = 'edit';
}
$action = new Action(Action::VIEW); # or
$action2 = Action::VIEW();
```

自动执行静态方法以提供快速访问的枚举值.

使用类常量的一个优势是能够提示枚举值的类型.

```
function setAction(Action $action)
{
    // ...
}
```

**文档**

* `__construct()` 构造函数初始化检查值是否存在于枚举列表中
* `__toString()` 可以直接输出枚举值字符串
* `getValue()` 返回枚举当前值
* `getKey()` 返回枚举值的键
* `equals()` 测试枚举实例是否相等,返回布尔值

静态方法:

* `toArray()` 返回枚举关联数组
* `keys()` 返回枚举类中所有常量的名称
* `values()` 返回枚举类中所有常量的值
* `isValid()` 检查值是否在枚举集中
* `isValidKey()` 检查键是否在枚举集中
* `search()` 返回搜索值的键

**静态方法说明**

```
class Action extends Enum
{
    const VIEW = 'view';
    const EDIT = 'edit';
}

// Static method:
$action = Action::VIEW();
$action = Action::EDIT();
```

静态方法是用`__callStatic()`和反射实现的.当然也可以在类中自定义

```
class Action extends Enum
{
    const VIEW = 'view';

    /**
     * @return Action
     */
    public static function VIEW() {
        return new Action(self::VIEW);
    }
}
```

我想还是使用下面的格式比较方便

```
/**
 * @method static Action VIEW()
 * @method static Action EDIT()
 */
class Action extends Enum
{
    const VIEW = 'view';
    const EDIT = 'edit';
}
```

