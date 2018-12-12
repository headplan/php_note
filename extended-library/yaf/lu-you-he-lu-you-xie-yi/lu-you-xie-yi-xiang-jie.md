# 路由协议详解

#### 默认路由协议

默认的路由协议**Yaf\_Route\_Static** , 就是分析请求中的**request\_uri** , 在去除掉**base\_uri**以后 , 获取到真正的负载路由信息的**request\_uri**片段 , 具体的策略是 , 根据"/"对**request\_uri**分段 , 依次得到Module,Controller,Action在得到Module以后 , 还需要根据Yaf\_Application::$modules来判断Module是否是合法的Module , 如果不是 , 则认为Module并没有体现在**request\_uri**中 , 而把原Module当做Controller , 原Controller当做Action :

```php
<?php
# 对于请求request_uri为"/ap/foo/bar/dummy/1"
# base_uri为"/ap"
# 则最后参加路由的request_uri为"/foo/bar/dummy/1"
# 然后,通过对URL分段,得到如下分节
# foo,bar,dummy,1
# 然后判断foo是不是一个合法的Module,如果不是,则认为结果如下:
[
    'module'    => '默认模块',
    'controller => 'foo',
    'action'    => 'bar',
    'params'    => [
        'dummy' => 1,
    ]
];

# 而如果在配置文件中定义了ap.modules="Index,Foo",
# 则此处就会认为foo是一个合法模块,则结果如下
[
    'module'     => 'foo',
    'controller' => 'bar',
    'action'     => 'dummy',
    'params'     => [
        1 => NULL,
    ]
];
```

> 当只有一段路由信息的时候 , 比如对于上面的例子 , 请求的URI为/ap/foo , 则默认路由和下面要提到的`Yaf_Route_Supervar`会首先判断ap.action\_prefer , 如果为真 , 则把foo当做Action , 否则当做Controller .

#### Yaf\_Route\_Simple

**Yaf\_Route\_Simple**是基于请求中的query string来做路由的 , 在初始化一个**Yaf\_Route\_Simple**路由协议的时候 , 需要给出3个参数 , 这3个参数分别代表在**query string**中Module, Controller, Action的变量名 :

```php
<?php
# 指定三个变量名
$route = new Yaf\Route_Simple("m", "c", "a");
$route->addRoute('name', $route);

# 对于如下请求:"http://domain.com/index.php?c=index&a=test"
[
    'module' => '默认模块',
    'controller' => 'index',
    'action' => 'test',
]
```

> 只有在query string中不包含任何3个参数之一的情况下 , Yaf\_Route\_Simple才会返回失败 , 将路由权交给下一个路由协议 .

#### Yaf\_Route\_Supervar

Yaf\_Route\_Supervar和Yaf\_Route\_Simple相似 , 都是在query string中获取路由信息 , 不同的是 , 它获取的是一个类似包含整个路由信息的request\_uri

```php
<?php
# 指定supervar变量名
$route = new Yaf\Route_Supervar("r");
$route->addRoute('name', $route);
# 对于如下请求:"http://domain.com/index.php?r=/a/b/c
[
    'module'     => 'a',
    'controller' => 'b',
    'action'     => 'c',
]
```

> 在query string中不包含supervar变量的时候 , Yaf\_Route\_Supervar会返回失败 , 将路由权交给下一个路由协议 .

#### Yaf\_Route\_Map

Yaf\_Route\_Map协议是一种简单的路由协议 , 它将REQUEST\_URI中以'/'分割的节 , 组合在一起 , 形成一个分层的控制器或者动作的路由结果 . Yaf\_Route\_Map的构造函数接受俩个参数 , 第一个参数表示路由结果是作为动作的路由结果 , 还是控制器的路由结果 . 默认的是动作路由结果 . 第二个参数是一个字符串 , 表示一个分隔符 , 如果设置了这个分隔符 , 那么在REQUEST\_URI中 , 分隔符之前的作为路由信息载体 , 而之后的作为请求参数 .

