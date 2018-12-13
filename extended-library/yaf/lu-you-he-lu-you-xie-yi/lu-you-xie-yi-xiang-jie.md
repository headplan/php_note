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

Yaf\_Route\_Map协议是一种简单的路由协议 , 它将REQUEST\_URI中以'/'分割的节 , 组合在一起 , 形成一个分层的控制器或者动作的路由结果 . Yaf\_Route\_Map的构造函数接受**俩个参数** , **第一个参数表示路由结果是作为动作的路由结果** , **还是控制器的路由结果** . 默认的是动作路由结果 . **第二个参数是一个字符串** , **表示一个分隔符** , 如果设置了这个分隔符 , 那么在REQUEST\_URI中 , 分隔符之前的作为路由信息载体 , 而之后的作为请求参数 .

```
<?php
/**
 * 对于请求request_uri为"/ap/foo/bar"
 * base_uri为"/ap"
 * 则最后参加路由的request_uri为"/foo/bar"
 * 然后,通过对URL分段,得到如下分节
 * foo,bar
 * 组合在一起以后,得到路由结果foo_bar
 * 然后根据在构造Yaf_Route_Map的时候,是否指明了控制器优先,
 * 如果没有,则把结果当做是动作的路由结果
 * 否则,则认为是控制器的路由结果
 * 默认的,控制器优先为FALSE
 */
```

#### Yaf\_Route\_Rewrite

Yaf\_Route\_Rewrite是一个强大的路由协议 , 它能满足我们绝大部分的路由需求 :

```php
<?php
# 创建一个路由协议实例
$route = new Yaf\Route_Rewrite(
    'product/:ident',
    [
        'controller' => 'products',
        'action' => 'view'
    ]
);
# 使用路由器装载路由协议
$router->addRoute('product', $route);
```

在这个例子中 , 试图匹配Url指定到一个单一的产品 , 就像`http://domain.com/product/choclolat-bar` . 为了实现这点 , 在路由协议中传递了2个变量到路由协议Yaf\_Route\_Rewrite的构造函数其中. 第一个变量\('product/:indent'\)就是匹配的路径 , 第二个变量\(array变量\)是路由到的动作控制器 ; 路径使用一个特别的标识来告诉路由协议如何匹配到路径中的每一个段 , 这个标识有两种 , 可以帮助我们创建我们的路由协议 , 如下所示 :

* `:`  
  **冒号**`:` - 指定了一个段 , 这个段包含一个变量用于传递到我们动作控制器中的变量 , 我们要设置好事先的变量名 , 比如在上面我们的变量名就是'ident' . 因此 , 倘若我们访问`http://domian.com/product/chocoloate-bar`将会创建一个变量名为ident并且其值是'chocoloate-bar'的变量 , 然后就可以在我们的动作控制器`ProductsController/viewAction`下获取到它的值  
  `$this->getRequest()->getParam('ident');`

* `*`  
  **星号**`*` - 被用做一个通配符 , 意思就是在Url中它后面的所有段都将作为一个通配数据被存储 . 例如 , 如果我们有路径`path/product/:ident/*`\(就是路由协议中设置的第一个变量\) ,  并且我们访问的Url为`http://domain.com/product/chocolate-bar/test/value1/another/value2` , 那么所有的在`chocolate-bar`后面的段都将被做成变量名/值对 , 因此这样会给我们下面的结果 :

  ```
  ident = chocolate-bar
  test = value1
  another = value2
  ```

这种路由配置就有点类似Laravel中的路由配置了 .

#### Yaf\_Route\_Regex



