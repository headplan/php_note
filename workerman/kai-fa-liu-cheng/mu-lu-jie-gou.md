# 目录结构

```
./
├── Autoloader.php                    # 自动加载类
├── Connection/                      /# Socket连接相关
│   ├── AsyncTcpConnection.php        # 异步TCP连接类
│   ├── AsyncUdpConnection.php        # 异步UDP连接类
│   ├── ConnectionInterface.php       # Socket连接接口
│   ├── TcpConnection.php             # TCP连接类
│   └── UdpConnection.php             # UDP连接类
├── Events/                          /# 网络事件库
│   ├── Ev.php                        # Libev网络事件库
│   ├── Event.php                     # Event网络事件库
│   ├── EventInterface.php            # 网络事件库接口
│   ├── Libevent.php                  # Libevent网络事件库
│   ├── React/                       /# Workerman3.3.6增加的一个react-event-loop的适配,也就是说ReatPHP支持的组件.意味着支持
│   │   ├── Base.php                  # 异步Mysql客户端
│   │   ├── ExtEventLoop.php          # 异步Redis客户端  
│   │   ├── ExtLibEventLoop.php       # 异步HTTP,异步文件,异步RabbitMQ等等.
│   │   └── StreamSelectLoop.php
│   ├── Select.php                    # Select网络事件库
│   └── Swoole.php                    # Swoole网络事件库,支持Swoole的事件轮询   
├── Lib/                             /# 常用类库
│   ├── Constants.php                 # 常量定义
│   └── Timer.php                     # 定时器
├── MIT-LICENSE.txt
├── Protocols/                       /# 相关协议
│   ├── Frame.php                     # Frame协议实现
│   ├── Http/                        /# HTTP协议相关 
│   │   └── mime.types                # MIME类型
│   ├── Http.php                      # HTTP协议实现
│   ├── ProtocolInterface.php         # 协议接口类
│   ├── Text.php                      # TEXT协议实现
│   ├── Websocket.php                 # WebSocket协议实现 
│   └── Ws.php                        # Ws协议,Workerman作为客户端通过ws协议发起websocket连接
├── README.md
├── WebServer.php                     # WebServer
├── Worker.php                        # Worker
└── composer.json
```



