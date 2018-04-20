# HttpFoundation组件

### Request

Request类的API , 可以让我们获得全部请求信息

```
// the URI being requested (e.g. /about) minus any query parameters
// 
$request->getPathInfo();

// retrieve GET and POST variables respectively
// 分别取出GET和POST变量
$request->query->get('foo');
$request->request->get('bar', 'default value if bar does not exist');

// retrieve SERVER variables
// 取出SERVER变量
$request->server->get('HTTP_HOST');

// retrieves an instance of UploadedFile identified by foo
// 通过foo取出一个UploadedFile实例
$request->files->get('foo');

// retrieve a COOKIE value
// 取出一个COOKIE值
$request->cookies->get('PHPSESSID');

// retrieve an HTTP request header, with normalized, lowercase keys
// 通过取出一个HTTP请求头
$request->headers->get('host');
$request->headers->get('content_type');

$request->getMethod();    // GET, POST, PUT, DELETE, HEAD
// an array of languages the client accepts
// 客户端所能接受的语言之数组
$request->getLanguages();
```

也可以模拟一个请求

```
$request = Request::create('/index.php?name=Fabien');
```

### Response

使用`Response`, 可以轻松调整响应

```
$response = new Response();

$response->setContent('Hello world!');
$response->setStatusCode(200);
$response->headers->set('Content-Type', 'text/html');

// configure the HTTP cache headers 配置HTTP缓存头
$response->setMaxAge(10);
```



