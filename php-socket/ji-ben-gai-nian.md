# 基本概念

#### 网络中进程之间的通信

本地的进程间通信\(IPC\)有很多种方式 , 但可以总结为下面4类 :

* 消息传递（管道、FIFO、消息队列）
* 同步（互斥量、条件变量、读写锁、文件和写记录锁、信号量）
* 共享内存（匿名的和具名的）
* 远程过程调用（Solaris门和Sun RPC）

说到通信 , 首要解决的问题是如何唯一标识一个进程 , 否则通信无从谈起 . 在本地可以通过进程PID来唯一标识一个进程 , 但是在网络中这是行不通的 . 其实**TCP/IP协议**族已经帮我们解决了这个问题 .

**网络层**的“ip地址”可以唯一标识网络中的主机 , 而**传输层**的“协议+端口”可以唯一标识主机中的应用程序\(进程\) . 这样利用三元组\(ip地址 , 协议 , 端口\)就可以标识网络的进程了 . 网络中的进程通信就可以利用这个标志与其它进程进行交互 .

使用TCP/IP协议的应用程序通常采用应用编程接口 : UNIX  BSD的套接字\(socket\)和UNIX System V的TLI\(已经被淘汰\) , 来实现网络进程之间的通信 . 就目前而言 , 几乎所有的应用程序都是采用socket .

#### 什么是Socket

通过前文已经了解到 , 网络中的进程是通过socket来通信的 . socket起源于Unix , 而Unix/Linux基本哲学之一就是“一切皆文件” , 都可以用“打开open–&gt;读写write/read–&gt;关闭close”模式来操作 . 所以可以把Socket理解为一种特殊的文件 , 一些socket函数就是对其进行的操作\(读/写IO、打开、关闭\) .

#### Socket的基本操作

既然socket是“open—write/read—close”模式的一种实现 , 这里参考PHP中提供的一些Socket函数 , 来进一步了解通信流程 .

**socket\_create**

创建一个套接字\(通讯节点\) .

```php
resource socket_create ( int $domain , int $type , int $protocol )
```

创建并返回一个套接字 , 也称作一个通讯节点 . 一个典型的网络连接由 2 个套接字构成 , 一个运行在客户端 , 另一个运行在服务器端 .

前面说了文件 , 可以把他理解为普通文件的打开操作返回一个文件描述字 , 这里就是创建一个socket的描述符 , 后续的操作都是把他作为参数来进行读写操作 . 和打开文件时一样 , 创建socket的时候 , 也可以指定不同的参数创建不同的socket描述符 , socket\_create的三个参数分别为 :

* **domain** : 即协议域 , 又称为协议族\(family\) . 常用的协议族有 , AF\_INET、AF\_INET6、AF\_LOCAL（或称AF\_UNIX，Unix域socket）、AF\_ROUTE等等 . 协议族决定了socket的地址类型 , 在通信中必须采用对应的地址 , 如AF\_INET决定了要用ipv4地址\(32位的\)与端口号\(16位的\)的组合、AF\_UNIX决定了要用一个绝对路径名作为地址 . 这里即可以理解为将哪种协议用在套接字上 . 

| `AF_INET` | IPv4 网络协议。TCP 和 UDP 都可使用此协议。 |
| :--- | :--- |
| `AF_INET6` | IPv6 网络协议。TCP 和 UDP 都可使用此协议。 |
| `AF_UNIX` | 本地通讯协议。具有高性能和低成本的 IPC（进程间通讯）。 |

* **type** : 指定socket类型 . 

| `SOCK_STREAM` | 提供一个顺序化的、可靠的、全双工的、基于连接的字节流。支持数据传送流量控制机制。TCP 协议即基于这种流式套接字。 |
| :--- | :--- |
| `SOCK_DGRAM` | 提供数据报文的支持。\(无连接，不可靠、固定最大长度\).UDP协议即基于这种数据报文套接字。 |
| `SOCK_SEQPACKET` | 提供一个顺序化的、可靠的、全双工的、面向连接的、固定最大长度的数据通信；数据端通过接收每一个数据段来读取整个数据包。 |
| `SOCK_RAW` | 提供读取原始的网络协议。这种特殊的套接字可用于手工构建任意类型的协议。一般使用这个套接字来实现 ICMP 请求（例如 ping）。 |
| `SOCK_RDM` | 提供一个可靠的数据层，但不保证到达顺序。一般的操作系统都未实现此功能。 |

* **protocol** : 故名思意 , 就是指定协议 , 当然是设置指定`domain`套接字下的具体协议 . 在PHP中可以使用getprotobynumber\(\)函数获取与协议名称关联的协议编号 , 即获取系统中/etc/protocol中的信息 , 相关的还有一个getprotobyname\(\) , 获取名字 , 如果你有自定义的协议名 , 也可以写在/etc/protocol里 . 如果协议是TCP或UDP , 可以直接用常量`SOL_TCP`和`SOL_UDP`

| icmp | Internet Control Message Protocol 主要用于网关和主机报告错误的数据通信。例如“ping”命令（在目前大部分的操作系统中）就是使用 ICMP 协议实现的。 |
| :--- | :--- |
| udp | User Datagram Protocol 是一个无连接的、不可靠的、具有固定最大长度的报文协议。由于这些特性，UDP 协议拥有最小的协议开销。 |
| tcp | Transmission Control Protocol 是一个可靠的、基于连接的、面向数据流的全双工协议。TCP 能够保障所有的数据包是按照其发送顺序而接收的。如果任意数据包在通讯时丢失，TCP 将自动重发数据包直到目标主机应答已接收。因为可靠性和性能的原因，TCP 在数据传输层使用 8bit 字节边界。因此，TCP 应用程序必须允许传送部分报文的可能。 |

socket\_create\(\) 正确时返回一个套接字 , 失败时返回 FALSE . 要读取错误代码 , 可以调用 socket\_last\_error\(\) . 这个错误代码可以通过 socket\_strerror\(\) 读取文字的错误说明 .

> 需要注意的是 , 上面的参数并不是可以随意组合的 . 如果使用一个无效的 domain 或 type , socket\_create\(\) 会使用 AF\_INET 和 SOCK\_STREAM 替代无效参数 , 同时会发出 E\_WARNING 警告信息 .

当我们成功创建一个socket时 , 返回一个PHP的资源ID , 其实它是一个socket描述字 , 描述它存在于协议族\(address family , AFXXX\)空间中 , 但没有一个具体的地址 . 如果想要给它赋值一个地址 , 就必须调用socket\_bind\(\)函数 .

**socket\_bind**

给套接字绑定名字 .

```php
bool socket_bind ( resource $socket , string $address [, int $port = 0 ] )
```

绑定 address 到 socket . 当然 , 该操作必须是在使用 socket\_connect\(\) 或者 socket\_listen\(\) 建立一个连接之前 .

* **socket** - 用socket\_create\(\)创建的一个有效的套接字资源 , 就是前面的提到的 .
* **address** 
  * 如果套接字是 AF\_INET 族 , 那么 address 必须是一个四点分法的 IP 地址\(例如 127.0.0.1\)
  * 如果套接字是 AF\_UNIX 族 , 那么 address 是 Unix 套接字一部分\(例如 /tmp/my.sock\)
* **port** - 可选参数 , 参数`port`仅仅用于`AF_INET`套接字连接的时候 , 并且指定连接中需要监听的端口号

返回值是bool , 有错误时和前面一样 , 用socket\_last\_error\(\)和socket\_strerror\(\)接错误 .

正如上面所说**socket\_bind\(\)**函数就是把一个地址族中的特定地址赋给socket . 例如对应AF\_INET、AF\_INET6就是把一个ipv4或ipv6地址和端口号组合赋给socket .

通常服务器在启动的时候都会绑定一个众所周知的地址\(如ip地址+端口号\) , 用于提供服务 , 客户就可以通过它来接连服务器 ; 而客户端就不用指定 , 有系统自动分配一个端口号和自身的ip地址组合 . 这就是为什么通常服务器端在listen之前会调用bind\(\) , 而客户端就不会调用 , 而是在connect\(\)时由系统随机生成一个 .

**listen和connect**

如果作为一个服务器 , 在调用`socket_create()`、`socket_bind()`之后就会调用`socket_listen()`来监听这个socket , 如果客户端这时调用`socket_connect()`发出连接请求 , 服务器端就会接收到这个请求 .

**socket\_listen** - 监听套接字上的连接 .

```php
bool socket_listen ( resource $socket [, int $backlog = 0 ] )
```

使用 socket\_create \(\) 创建套接字插座并绑定到带有 socket\_bind \(\) 的名称后 , 然后就需要监听了 . socket\_create\(\)函数创建的socket默认是一个主动类型的 , listen函数将socket变为被动类型的 , 即等待客户的连接请求 .

socket\_listen \(\) 仅适用于类型 SOCK\_STREAM 或 SOCK\_SEQPACKET 的套接字 .

* **socket** - 用socket\_create\(\)创建的一个有效的套接字资源 , 就是前面的提到的 .
* **backlog** - 可选参数 , 相应socket可以排队的最大连接个数 . 如果连接请求到满队时 , 客户端可能会收到ECONNREFUSED错误提示 , 或者 , 如果基础协议支持重新传输 , 则请求可能会被忽略 , 以便重试 . 这里第二个参数其实就是设置一下队列的长度 , 如果是tcp协议 , 当队列满了的时候 , 不会发送rst给客户端 , 客户端会重新发送SYN , 以便能进入这个队列 . 如果三次握手完成了 , 就会将完成三次握手的请求取出来 , 放入另一个队列中 , 这样队列就空出一个位置 , 其他重发SYN的请求就可以进入队列中 .

> 这个最大值 , 其实取决于底层平台 , Linux上默认设置为SOMAXCONN参数的值 , 默认为128  :
>
> ```
> cat /proc/sys/net/core/somaxconn
> ```
>
> 当然 , 对于一个经常处理新连接的高负载 web服务环境来说 , 128有点太小了 . 而且这个监听队列大一些的话 , 对防止拒绝服务 DoS 攻击也会有所帮助 .
>
> 不过现在这个参数可以由SYN Cookie部分接管了 , 开启 :
>
> ```
> echo 1 > /proc/sys/net/ipv4/tcp_syncookies
> ```
>
> 注意 , 即使开启该机制并不意味着所有的连接都是用SYN cookies机制来完成连接的建立 , 只有在半连接队列已满的情况下才会触发SYN cookies机制 . 由于SYN cookies机制严重违背TCP协议 , 不允许使用TCP扩展 , 可能对某些服务造成严重的性能影响\(如SMTP转发\) .
>
> 对于没有收到攻击的高负载服务器 , 不用开启也可以 , 可以通过修改tcp\_max\_syn\_backlog、tcp\_synack\_retries和tcp\_abort\_on\_overflow系统参数来调节 .

函数的成功时返回 TRUE , 或者在失败时返回 FALSE . 错误还用前面提到的函数接 .

**socket\_connect** - 开启一个socket连接 .

```php
bool socket_connect ( resource $socket , string $address [, int $port = 0 ] )
```

* **socket** - 创建的soket套接字
* **address** - 如果参数 socket 是 AF\_INET ， 那么参数 address 则可以是一个点分四组表示法（例如 127.0.0.1 ） 的 IPv4 地址； 如果支持 IPv6 并且 socket 是 AF\_INET6，那么 address 也可以是有效的 IPv6 地址（例如 ::1）；如果套接字类型为 AF\_UNIX ，那么 address 也可以是一个Unix 套接字。
* **port** - 参数 port 仅仅用于 AF\_INET 和 AF\_INET6 套接字连接的时候，并且是在此情况下是需要强制说明连接对应的远程服务器上的端口号。

函数的成功时返回 TRUE , 或者在失败时返回 FALSE . 错误还用前面提到的函数接 .

---

**socket\_accept** - 接受套接字上的连接 .

TCP服务器端依次调用socket\_create\(\)、socket\_bind\(\)、socket\_listen\(\)之后 , 就会监听指定的socket地址了 . TCP客户端依次调用socket\_create\(\)、socket\_connect\(\)之后会向TCP服务器发送了一个连接请求 . TCP服务器监听到这个请求之后 , 就会调用socket\_accept\(\)函数去接收请求 , 这样连接就建立好了 . 之后就可以开始网络I/O操作了 , 即类同于普通文件的读写I/O操作 .

这里如果套接字上有多个连接排队 , 则会使用第一个连接 .  如果没有挂起的连接 , 则socket\_accept将阻塞 , 直到出现连接 . 如果使用socket\_set\_blocking或socket\_set\_nonblock将套接字设为非阻塞 , 则将返回FALSE .

```php
resource socket_accept ( resource $socket )
```

我们看到socket\_accept也是返回一个资源 , 但它一般不会用于接收新来的连接请求 , 它表示一个已连接的资源 .  而是使用原始监听的socket去保持打开并重新使用 .  也就是说一个服务器通常通常仅仅只创建一个监听socket描述字，它在该服务器的生命周期内一直存在 . 内核为每个由服务器进程接受的客户连接创建了一个已连接socket描述字 , 当服务器完成了对某个客户的服务 , 相应的已连接socket描述字就被关闭 .

当服务端和客户端建立好连接以后 , 就可以调用网络I/O进行读写操作了 , 即实现了网咯中不同进程之间的通信 .

**read和write**

这里只简单的看一下read和write

**socket\_read** - 从套接字读取最大长度字节数 .

```php
string socket_read ( resource $socket , int $length [, int $type = PHP_BINARY_READ ] )
```

* **socket** - socket\_create \(\) 或 socket\_accept \(\) 创建的有效套接字资源
* **length** - 读取的最大字节数由长度参数指定 . 还可以使用\r , \n或\0表示结束读取 , 取决于后面的type参数 . 
* **type**
  * PHP\_BINARY\_READ - 默认使用系统recv\(\)函数 . 读取二进制安全数据 . 
  * PHP\_NORMAL\_READ - 读取停止在\n或\r转义字符 . 

返回成功读取的数据 , 否则返回false , 远程连接关闭了也会false . 没有可以读的数据 , 返回空字符串 .

**socket\_write** - 向Socket写入 .

```php
int socket_write ( resource $socket , string $buffer [, int $length = 0 ] )
```

从给定的$buffer缓冲区写入socket .

**socket** - 同上

**buffer** - 要写入的缓冲区

**length** - 可选参数 , 可以指定写入套接字的可选字节长度 . 如果此长度大于缓冲区长度 , 则会将其无提示地截断为缓冲区的长度 .

成功则写入还返回数据 , 失败返回false , 错误用前面的两个函数获取 .

这里要注意的是 , 返回0意味着没有写入字节 , 但也是完全有效的 . 所以判断是否false时 , 要用全等号去判断 , "===" .

还要注意的是 , socket\_write函数不一定会写入给定缓冲区中的所有字节 , 因为这取决于network buffers等等 , 也就是说虽然缓冲区够用 , 但还是有数量限制 , 所以要注意下 , 以免无意中忘记传输其余数据 .

---

**socket\_close** - 关闭套接字资源 .

```php
void socket_close ( resource $socket )
```

socket\_close\(\) 会关闭掉给定的 socket 资源 . 这个函数只针对套接字资源有效 , 不能用在其他类型的资源类型上 .

> 查看本地代码 , 模拟简单的服务器与客户端的交互 .

---

#### Socket中TCP的三次握手建立连接

tcp建立连接要进行“三次握手” , 即交换三个分组 . 大致流程如下 :

* 客户端向服务器发送一个SYN J
* 服务器向客户端响应一个SYN K , 并对SYN J进行确认ACK J+1
* 客户端再向服务器发一个确认ACK K+1

![](/assets/socket三次握手.png)

当客户端调用connect时 , 触发了连接请求 , 向服务器发送了SYN J包 , 这时connect进入阻塞状态 ;

服务器监听到连接请求 , 即收到SYN J包 , 调用accept函数接收请求向客户端发送SYN K , ACK J+1 , 这时accept进入阻塞状态 ; 

客户端收到服务器的SYN K , ACK J+1之后 , 这时connect返回 , 并对SYN K进行确认 ; 

服务器收到ACK K+1时 , accept返回 , 至此三次握手完毕 , 连接建立 . 

> 总结：客户端的connect在三次握手的第二个次返回，而服务器端的accept在三次握手的第三次返回。

#### Socket中TCP的四次握手释放连接

Socket中的四次握手释放连接的过程 , 也称为四次挥手 : 

![](/assets/tcp四次挥手.png)

某个应用进程首先调用close主动关闭连接 , 这时TCP发送一个FIN M；

