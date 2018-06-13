# 目录结构

```
./
├── Autoloader.php                    # 自动加载类
├── Connection/                       # Socket连接相关
│   ├── AsyncTcpConnection.php        # 异步TCP连接类
│   ├── AsyncUdpConnection.php        # 异步UDP连接类
│   ├── ConnectionInterface.php       # Socket连接接口
│   ├── TcpConnection.php             # TCP连接类
│   └── UdpConnection.php             # UDP连接类
├── Events/                            # 网络事件库
│   ├── Ev.php
│   ├── Event.php
│   ├── EventInterface.php
│   ├── Libevent.php
│   ├── React/
│   │   ├── Base.php
│   │   ├── ExtEventLoop.php
│   │   ├── ExtLibEventLoop.php
│   │   └── StreamSelectLoop.php
│   ├── Select.php
│   └── Swoole.php
├── Lib/                                # 常用类库
│   ├── Constants.php
│   └── Timer.php
├── MIT-LICENSE.txt
├── Protocols/                          # 相关协议
│   ├── Frame.php
│   ├── Http/
│   │   └── mime.types
│   ├── Http.php
│   ├── ProtocolInterface.php
│   ├── Text.php
│   ├── Websocket.php
│   └── Ws.php
├── README.md
├── WebServer.php
├── Worker.php
└── composer.json
```



