# 模板

现在 , 我们的框架写死了模板的运行方式 , 如果要写更多的逻辑 , 也只能放在模板中 , 这就需要我们添加一个新层\(a new layer\) .

把模板代码从逻辑中分离出来 , 添加控制器层 , 基于客户端请求所传递的信息 , 生成一个响应 .

重构模板渲染部分 , 抽出模板渲染到一个函数中 :

```
try {
    $request->attributes->add($matcher->match($request->getPathInfo()));
    $response = call_user_func('render_template', $request);
} catch (Routing\Exception\ResourceNotFoundException $e) {
    $response = new Response('Not Found', 404);
} catch (Exception $e) {
    $response = new Response('An error occurred', 500);
}
```

`Request`类的`attrubutes`属性添加的是“和请求有关、但不直接同HTTP请求数据相关”的附加信息 .

创建render\_template函数 , 也就是一个通用的控制器 . 在没有特殊逻辑时渲染模板 , 保持模板同先前一样 , request attributes在模板被渲染之前已经被提取出来 :

```
function render_template($request)
{
    extract($request->attributes->all(), EXTR_SKIP);
    ob_start();
    include sprintf(__DIR__.'/../src/views/%s.php', $_route);

    return new Response(ob_get_clean());
}
```

继续重构 , 作为一个约定 , 对于每个路由 , 把其关联的控制器配置在controller这个路由属性中  . 使路由和控制器进行关联 , 然后用通用控制器 , 也就是render\_template函数来渲染模板 . 

```
$routes->add('hello', new Routing\Route('/hello/{name}', [
    'name' => 'World',
    '_controller' => function ($request) {
        return render_template($request);
    }
]));
```

现在 , 一个路由可以与任何控制器进行关联 , 可以在模板渲染\(执行通用函数\)之前添加参数 , 在后面改变Response对象 . 

```
$routes->add('hello', new Routing\Route('/hello/{name}', [
    'name' => 'World',
    '_controller' => function ($request) {
        $request->attributes->set('foo', 'bar');
        $response = render_template($request);
        $response->headers->set('Content-Type', 'text/plain');
        return $response
    }
]));
```

现在 , 我们可以更好的控制请求与响应 , 它们都被转到了路由中的\_controller中 , 我们再来添加一个控制器\(不渲染页面\)  . 

**查看commit -m"leap\_year"**





