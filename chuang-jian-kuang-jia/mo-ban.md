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



