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

啊



