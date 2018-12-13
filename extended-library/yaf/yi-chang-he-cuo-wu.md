# 异常和错误

Yaf实现了一套错误和异常捕获机制 , 主要是对常见的错误处理和异常捕获方法做了一个简单抽象 , 方便应用组织自己的错误统一处理逻辑 .

Yaf自身出错时候 , 根据配置可以分别采用抛异常或者触发错误的方式来通知错误 . 在appliation.dispatcher.throwException\(配置文件, 或者通过Yaf\_Dispatcher::throwException\(true\)\)打开的情况下 , Yaf会抛异常 , 否则则会触发错误 .

那么对应的 , 就有俩套错误处理方式可供应用选用 .

#### 异常使用

在application.dispatcher.catchException\(配置文件 , 或者可通过Yaf\_Dispatcher::catchException\(true\)\)开启的情况下 , 当Yaf遇到未捕获异常的时候 , 就会把运行权限 , 交给当前模块的Error Controller的Error Action动作 , 而异常或作为请求的一个参数 , 传递给Error Action.

在Error Action中可以通过`$request->getRequest()->getParam("exception")`获取当前发生的异常 . 也可以通过`$request->getException()`来获取当前发生的异常 , 而如果Error Action定义了一个名为$exception的参数的话 , 也可以直接通过这个参数获取当前发生的异常 .

**Error Controller**

```php
<?php

# 当有未捕获的异常,则控制流会流到这里
class ErrorController extends Yaf_Controller_Abstract
{
   public function errorAction($exception)
   {
      assert($exception === $exception->getCode());
      $this->getView()->assign("code", $exception->getCode());
      $this->getView()->assign("message", $exception->getMessage());
   }   
}
```

有了这样的最终异常处理逻辑 , 应用就可以在出错的时候直接抛出异常 , 在统一异常处理逻辑中 , 根据各种不同的异常逻辑 , 处理错误 , 记录日志 .

```php
<?php

# 当有未捕获的异常,则控制流会流到这里
class ErrorController extends Yaf_Controller_Abstract
{
    /**
     * 通过$request->getException()获取到发生的异常
     */
    public function errorAction($exception)
    {
        switch($exception->getCode()) {
            case YAF_ERR_LOADFAILD:
            case YAF_ERR_LOADFAILD_MODULE:
            case YAF_ERR_LOADFAILD_CONTROLLER:
            case YAF_ERR_LOADFAILD_ACTION:
                # 404
                header("Not Found");
                break;
            case CUSTOM_ERROR_CODE:
            # 自定义的异常
            ....
            break;
    }
}
```

更好的方式 :

```php
<?php

# 当有未捕获的异常, 则控制流会流到这里
class ErrorController extends Yaf_Controller_Abstract
{
    /**
     * 此时可通过$request->getException()获取到发生的异常
     */
    public function errorAction()
    {
        $exception = $this->getRequest()->getException();
        try {
            throw $exception;
        } catch (Yaf_Exception_LoadFailed $e) {
            //加载失败
        } catch (Yaf_Exception $e) {
            //其他错误
        }
    }
}
```



