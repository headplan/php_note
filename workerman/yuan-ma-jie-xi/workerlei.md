# Worker类

#### 构造函数

```php
public function __construct($socket_name = '', $context_option = array())
```

初始化一个Worker容器实例 , 可以设置容器的一些属性和回调接口 , 完成特定功能 .

```php
/**
 * Construct.
 *
 * @param string $socket_name
 * @param array  $context_option
 */
public function __construct($socket_name = '', $context_option = array())
{
    // Save all worker instances.
    $this->workerId                    = spl_object_hash($this);
    static::$_workers[$this->workerId] = $this;
    static::$_pidMap[$this->workerId]  = array();

    // Get autoload root path.
    $backtrace                = debug_backtrace();
    $this->_autoloadRootPath = dirname($backtrace[0]['file']);

    // Context for socket.
    if ($socket_name) {
        $this->_socketName = $socket_name;
        if (!isset($context_option['socket']['backlog'])) {
            $context_option['socket']['backlog'] = static::DEFAULT_BACKLOG;
        }
        $this->_context = stream_context_create($context_option);
    }
}
```

初始化参数均为可选参数 .

$socket\_name 不填写表示不监听任何端口 . 填写则会执行socket监听 . 



#### 属性

#### 回调属性

#### 接口



