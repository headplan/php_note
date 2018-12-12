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



