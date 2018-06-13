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
│   ├── React/
│   │   ├── Base.php
│   │   ├── ExtEventLoop.php
│   │   ├── ExtLibEventLoop.php
│   │   └── StreamSelectLoop.php
│   ├── Select.php                      # Select网络事件库
│   └── Swoole.php                      # Swoole网络事件库,支持Swoole的事件轮询   
├── Lib/                               /# 常用类库
│   ├── Constants.php                   # 常量定义
│   └── Timer.php                       # 定时器
├── MIT-LICENSE.txt
├── Protocols/                         /# 相关协议
│   ├── Frame.php                       # Frame协议实现
│   ├── Http/                          /# HTTP协议相关 
│   │   └── mime.types                  # MIME类型
│   ├── Http.php                        # HTTP协议实现
│   ├── ProtocolInterface.php           # 协议接口类
│   ├── Text.php                        # TEXT协议实现
│   ├── Websocket.php                   # WebSocket协议实现 
│   └── Ws.php                          # 
├── README.md
├── WebServer.php                       # WebServer
├── Worker.php                          # Worker
└── composer.json
```



