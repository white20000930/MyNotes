# 开始之前

- 采用Raw_version版本学习
- 4月3开始

## 配置`MySQL`

参考：https://blog.csdn.net/yingLGG/article/details/121400284

安装`mysql`

```bash
sudo apt-get install mysql-server
```

进行初始化配置

```bash
sudo mysql_secure_installation
```

具体如何初始化配置，参考：https://zhuanlan.zhihu.com/p/610793026

> 启动server
>
> ./server port
>
> 浏览器端
>
> ip:port

可能还需要安装`apt-get install libmysqlclient-dev`，这个过程可能会遇到错误，根据错误去网上找解决方案即可解决。

然后，实际上是这样的输入：

在terminal中输入`./server 9006`

在浏览器中输入`localhost:9006`

其中，9006可以替换为任意四位数字

就可以啦

## TCP/IP 网络模型

网络模型有：OSI七层参考模型和TCP/IP四层参考模型、五层参考模型。

[TCP/IP 网络模型有哪几层？](https://xiaolincoding.com/network/1_base/tcp_ip_model.html#%E5%BA%94%E7%94%A8%E5%B1%82)

| 层 Layer          | 协议                           | 头部                 | 数据      | 作用 & 特点                                    |
| ----------------- | ------------------------------ | -------------------- | --------- | ---------------------------------------------- |
| 应用层Application | `HTTP`、FTP、Telnet、DNS、SMTP |                      | 消息/报文 | 网络服务与用户的一个接口                       |
| 传输层 Transport  | `TCP`（常用）、UDP             | TCP头部：端口(应用)  | 段        | 为应用层提供网络支持的；不负责在设备间传送数据 |
| 网络层  Internet  | IP 协议                        | IP头部：IP地址(设备) | 包        | 将数据从一个设备传输到另一个设备               |
| 网络接口层Link    |                                | MAC头部              | 帧        | 为网络层提供「链路级别」传输的服务             |

<img src="tcpip参考模型.drawio.png" alt="img" style="zoom:50%;" />

<img src="封装.png" alt="img" style="zoom:67%;" />

- 网络接口层的传输单位是帧（frame）

- IP 层的传输单位是包（packet）

- TCP 层的传输单位是段（segment）

- HTTP 的传输单位则是消息或报文（message）

但这些名词并没有什么本质的区分，可以统称为数据包。

###应用层

- 应用层是工作在操作系统中的用户态，传输层及以下则工作在内核态。

- 应用层的数据包会传给传输层

#### HTTP协议

[一篇文章搞懂http协议(超详细）](https://zhuanlan.zhihu.com/p/676502895)

Hyper Text Transfer Protocol（超文本传输协议）

- 是用于 从WWW服务器 传输超文本 到 本地浏览器 的传送协议

- 是一个应用层协议，由请求和响应构成，是一个标准的客户端-服务器模型
  - 是 TCP/IP 协议栈中的应用层协议，位于协议栈的最高层

- 是一个无状态的协议
  - 不会在服务器端存储与特定客户端交互有关的信息
  - 每一次请求都是独立的，服务器不记住之前的请求。
  - 客户端识别使用 cookie 或其他机制（如会话 ID）进行。（不太理解cookie，后面再查）

请求和响应：客户端发出的消息为请求`Request`；服务端发出的应答消息位响应`Response`

![img](v2-46a1c6f91e2a6125a6bf0cf00b30d2bc_720w.webp)

HTTP 是一种应用层的协议，通过[TCP](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/zh-CN/docs/Glossary/TCP)，或者是[TLS](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/zh-CN/docs/Glossary/TLS)——一种加密过的 TCP 连接——来发送，理论上来说可以借助任何可靠的传输协议。

#### HTTP无状态但有会话

问题：用户没有办法在同一个网站中进行连贯的交互。

方案：利用标头的扩展性，HTTP Cookie  被加进了协议工作流程，每个请求之间可创建会话，让每个请求都能共享相同的上下文信息或相同的状态。

### 传输层

- 传输层数据包>MSS（TCP最大报文段长度）时，要将数据包分块，在TCP中，每个分块位一个`TCP段`（TCP Segment）

  <img src="TCP段.png" alt="img" style="zoom: 67%;" />

- 设备作为接收方时，传输层要把数据包传给应用，但是一台设备上可能有多个应用在接受或传输数据，故需要一个编号将应用区分开，这个编号即`端口`。传输层的报文中会携带端口号

- TCP头部用于指定通信的源端端口，目的端口，管理tcp连接等

### 网络层

#### IP 报文

IP 协议会将传输层的报文作为数据部分，再加上 IP 包头组装成 IP 报文， IP 报文大小超过 MTU（以太网中一般为 1500 字节）就会**再次进行分片**，得到一个即将发送到网络的 IP 报文。

<img src="12.jpg" alt="img" style="zoom:80%;" />

#### 寻址

编号：用IP地址给设备编号

- 对于 IPv4 协议， IP 地址共 32 位，分成了四段（比如，192.168.100.1），每段是 8 位。

- 但这样寻址麻烦，因此需要将IP地址分成两种意义

  - 网络号，负责标识该 IP 地址是属于哪个「子网」的；
  - 主机号，负责标识同一「子网」下的不同主机；

- 需要配合子网掩码才能算出 IP 地址 的网络号和主机号

  - 比如 10.100.122.0/24，后面的`/24`表示就是 `255.255.255.0` 子网掩码
  - 255.255.255.0 二进制是「11111111-11111111-11111111-00000000」， 24 个1，为了简化子网掩码的表示，用/24代替255.255.255.0
  - 将 10.100.122.2 和 255.255.255.0 进行**按位与运算**，就可以得到网络号

  <img src="16.jpg" alt="img" style="zoom:67%;" />

  - 将 255.255.255.0 取反后与IP地址进行进行**按位与运算**，就可以得到主机号。
  - **相当于：掩码=1的位为网络位，掩码=0的位为主机位**

#### 路由

除了寻址能力， IP 协议还有另一个重要的能力就是**路由**。

当数据包到达一个网络节点，就需要通过路由算法决定下一步走哪条路径。路由器寻址工作中，就是要找到目标地址的子网，找到后进而把数据包转发给对应的网络内。

IP 协议的寻址作用是告诉我们去往下一个目的地该朝哪个方向走，路由则是根据「下一个目的地」选择路径。寻址更像在导航，路由更像在操作方向盘

#### TCP/IP和HTTP、TCP、IP

什么是TCP/IP协议？

- TCP/IP协议不仅仅指的是TCP 和IP两个协议
- 而是指一个由FTP、SMTP、TCP、UDP、IP等协议构成的协议簇， 只是因为在TCP/IP协议中TCP协议和IP协议最具代表性，所以被称为TCP/IP协议

TCP/IP和HTTP的关系：

> 我们在传输数据时，可以只使用(传输层)TCP/IP协议，但是那样的话，如果没有应用层，便无法识别数据内容。如果想要使传输的数据有意义，则必须使用到应用层协议。
> WEB使用HTTP协议作应用层协议，以封装HTTP文本信息，然后使用TCP/IP做传输层协议将它发到网络上。

### 网络接口层

<img src="网络接口层.png" alt="img" style="zoom:67%;" />

**网络接口层**（*Link Layer*）在 IP 头部的前面加上 MAC 头部，并封装成数据帧（Data frame）发送到网络上。

MAC 头部是以太网使用的头部，它包含了接收方和发送方的 MAC 地址等信息，我们可以通过 ARP 协议获取对方的 MAC 地址。

网络接口层主要为网络层提供「链路级别」传输的服务，负责在以太网、WiFi 这样的底层网络上发送原始数据包，工作在网卡这个层次，使用 MAC 地址来标识网络上的设备

##什么是Web Server

[小白视角：一文读懂社长的TinyWebServer(Raw_Version)](https://huixxi.github.io/2020/06/02/%E5%B0%8F%E7%99%BD%E8%A7%86%E8%A7%92%EF%BC%9A%E4%B8%80%E6%96%87%E8%AF%BB%E6%87%82%E7%A4%BE%E9%95%BF%E7%9A%84TinyWebServer/#more)

服务器软件（程序） or 运行这个服务器软件的硬件（计算机）

主要功能：通过HTTP协议与客户端（通常是浏览器Browser）进行通信，来接收、存储、处理，来自客户端的HTTP请求，并对请求作出HTTP响应，返回给客户端其请求的内容（文件、网页等），或Error信息。

![image-20240404150223896](image-20240404150223896.png)

## 用户如何与Web服务器通信

1. 在浏览器键入“域名”或“IP地址:端口号”
2. （浏览器先将域名解析成IP地址）
3. 浏览器根据IP地址向对应的Web服务器发送HTTP请求
   1. 通过TCP协议三次握手建立与目标Web服务器的连接
   2. HTTP协议生成针对目标Web服务器的HTTP请求报文
   3. 通过TCP、IP等协议发送到目标Web服务器上

## Socket 是什么

[Socket到底是什么？](https://blog.csdn.net/github_34606293/article/details/78230456)

Socket也称作“套接字

- socket是对TCP/IP协议的封装，它的出现只是使得程序员更方便地使用TCP/IP协议栈而已。

- socket本身并不是协议，它是应用层与TCP/IP协议族通信的中间软件抽象层，是一组调用接口

<img src="SouthEast.jpeg" alt="img" style="zoom: 80%;" />

>TCP/IP只是一个协议栈，就像操作系统的运行机制一样，必须要具体实现，同时还要提供对外的操作接口。 
>这个就像操作系统会提供标准的编程接口，比如win32编程接口一样。 
>**TCP/IP也要提供可供程序员做网络开发所用的接口，这就是Socket编程接口**。

### socket服务器端演示程序

[Linux下的socket演示程序](https://c.biancheng.net/view/2128.html)

```c++
    #include <stdio.h>
    #include <string.h>
    #include <stdlib.h>
    #include <unistd.h>
    #include <arpa/inet.h>
    #include <sys/socket.h>
    #include <netinet/in.h>
    int main(){
        //创建套接字
        int serv_sock = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
        //将套接字和IP、端口绑定
        struct sockaddr_in serv_addr;
        memset(&serv_addr, 0, sizeof(serv_addr));  //每个字节都用0填充
        serv_addr.sin_family = AF_INET;  //使用IPv4地址
        serv_addr.sin_addr.s_addr = inet_addr("127.0.0.1");  //具体的IP地址
        serv_addr.sin_port = htons(1234);  //端口
        bind(serv_sock, (struct sockaddr*)&serv_addr, sizeof(serv_addr));
        //进入监听状态，等待用户发起请求
        listen(serv_sock, 20);
        //接收客户端请求
        struct sockaddr_in clnt_addr;
        socklen_t clnt_addr_size = sizeof(clnt_addr);
        int clnt_sock = accept(serv_sock, (struct sockaddr*)&clnt_addr, &clnt_addr_size);
        //向客户端发送数据
        char str[] = "http://c.biancheng.net/socket/";
        write(clnt_sock, str, sizeof(str));
       
        //关闭套接字
        close(clnt_sock);
        close(serv_sock);
        return 0;
    }
```

#### socket()函数

```C++
int serv_sock = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
```

- `int socket(int domain, int type, int protocol);`
  - 用于创建一个`socket`描述符（创建套接字），它唯一标识一个`socket`
  - **`socket` 描述符通常是 int 类型**。这是因为 socket 描述符本质上是操作系统分配给进程以标识特定网络连接或端点的整数。
- `domain`，协议域（协议族），决定了`socket`的地址类型，在通信中必须采用对应的地址
  - <u>`AF_INET`：决定了要用  IPv4地址（32位）与端口号（16位）的组合  作为地址</u>
  - `AF_UNIX`：决定了要用一个  绝对路径名  作为地址
- `type`，`socket`类型
  - <u>可靠性： 如果需要确保数据完整性和按序传输，则应使用 `SOCK_STREAM`。</u>
  - 实时性： 如果需要低延迟和无序数据传输，则应使用 `SOCK_DGRAM`。
  - 协议支持： 某些协议（如 TCP）需要特定的 `socket` 类型（`SOCK_STREAM`）。
  - 自定义需求： 对于需要对底层协议进行更精细控制的应用程序，可以使用 `SOCK_RAW` 或 `SOCK_PACKET`。
- `protocol`，制定协议
  - 如`IPPROTO_TCP`、`IPPTOTO_UDP`、`IPPROTO_SCTP`、`IPPROTO_TIPC`等

#### sockaddr_in结构体

```c++
struct sockaddr_in {
	__uint8_t sin_len;
	sa_family_t sin_family;
	in_port_t sin_port;
	struct in_addr sin_addr;
	char sin_zero[8];
};
```

- `sin_family`指代协议族，在`socket`编程中只能是`AF_INET`
- `sin_port`存储端口号（使用网络字节顺序）
- `sin_addr`存储IP地址，使用in_addr这个数据结构
- `sin_zero`是为了让`sockaddr`与`sockaddr_in`两个数据结构保持大小相同而保留的空字节

`sockaddr`和`sockaddr_in`结构体包含的数据都是一样的，它们在使用上有区别

- 程序员不应操作`sockaddr`，`sockaddr`是给操作系统用的。
- 程序员应使用`sockaddr_in`来表示地址，`sockaddr_in`区分了地址和端口，使用更方便。
- 一般用法：程序员把类型、ip地址、端口填充`sockaddr_in`结构体，然后强制转换成`sockaddr`，作为参数传递给系统调用函数

#### bind() 函数

```c++
//将套接字和IP、端口绑定
        struct sockaddr_in serv_addr;
        memset(&serv_addr, 0, sizeof(serv_addr));  //每个字节都用0填充
        serv_addr.sin_family = AF_INET;  //使用IPv4地址
        serv_addr.sin_addr.s_addr = inet_addr("127.0.0.1");  //具体的IP地址
        serv_addr.sin_port = htons(1234);  //端口
        bind(serv_sock, (struct sockaddr*)&serv_addr, sizeof(serv_addr));
```

bind()把用于通信的地址和端口绑定到 socket 上。

`int bind(int sockfd, const struct sockaddr *addr,socklen_t addrlen);`

- `sockfd`，需要绑定的socket
- `addr`，存放服务端用于通信的地址和端口
- `addrlen`，`addr`结构体大小

#### listen()函数

      //进入监听状态，等待用户发起请求
        listen(serv_sock, 20);

没啥好说的

#### accept函数

      //接收客户端请求
        struct sockaddr_in clnt_addr;
        socklen_t clnt_addr_size = sizeof(clnt_addr);
        int clnt_sock = accept(serv_sock, (struct sockaddr*)&clnt_addr, &clnt_addr_size);

`listen`监听客户端来的链接，`accept`将客户端的信息绑定到一个`socket`上，也就是给客户端创建一个`socket`，通过返回值返回给我们客户端的`socket`。

在 `accept()` 函数中，`clnt_addr` 结构体用作输出参数，用于接收客户端的地址信息。当 `accept()` 函数返回时，`clnt_addr` 结构体将包含连接客户端的 IP 地址和端口号。

#### socket和sockaddr_in的区别

- socket 描述符是一个整数，由操作系统分配给进程，以标识特定的网络连接或端点。
- sockaddr_in 是一个数据结构，用于表示 IPv4 地址和端口号。

#### Linux 下 socket 服务器端程序总结：

1. 创建一个 TCP 套接字。
2. 将套接字绑定到指定的 IP 地址和端口。
3. 进入监听状态，等待客户端连接。
4. 接受客户端连接，并创建一个新的套接字来处理该连接。
5. 向客户端发送数据。
6. 关闭客户端套接字和服务器套接字。

### socket客户端演示程序

```C++
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>
int main(){
    //创建套接字
    int sock = socket(AF_INET, SOCK_STREAM, 0);
    //向服务器（特定的IP和端口）发起请求
    struct sockaddr_in serv_addr;
    memset(&serv_addr, 0, sizeof(serv_addr));  //每个字节都用0填充
    serv_addr.sin_family = AF_INET;  //使用IPv4地址
    serv_addr.sin_addr.s_addr = inet_addr("127.0.0.1");  //具体的IP地址
    serv_addr.sin_port = htons(1234);  //端口
    connect(sock, (struct sockaddr*)&serv_addr, sizeof(serv_addr));

    //读取服务器传回的数据
    char buffer[40];
    read(sock, buffer, sizeof(buffer)-1);

    printf("Message form server: %s\n", buffer);

    //关闭套接字
    close(sock);
    return 0;
}
```

##### connect() 函数

`connect()` 函数用来建立连接。参数和bind相同

##### Linux 下 socket 客户端程序总结：

1. 创建一个 TCP 套接字。
2. 向服务器（特定的 IP 地址和端口）发起连接请求。
3. 连接成功后，从服务器读取数据。
4. 打印服务器发送的数据。
5. 关闭套接字。

#### 服务器和客户端数据传递过程

1. 客户端使用 `connect()` 函数向服务器发起连接请求。
2. 服务器使用 `accept()` 函数接受客户端连接，并创建一个新的套接字来处理该连接。
3. 服务器使用 `write()` 函数向客户端发送数据。
4. 客户端使用 `read()` 函数从服务器读取数据。

当客户端使用 `connect()` 函数向服务器发起连接请求时，如果服务器套接字处于监听状态，它会将该连接请求放入监听队列中。

服务器使用 `accept()` 函数从监听队列中接受客户端连接。如果监听队列中没有待处理的连接请求，`accept()` 函数会阻塞，直到有新的连接请求到达。

#### 重要的头文件

`<sys/socket.h>`： 提供创建和管理套接字的函数，如 `socket()`、`bind()`、`listen()` 和 `accept()`。

`<unistd.h>`： 提供与操作系统交互的函数，如 `read()`、`write()` 和 `close()`。

`<arpa/inet.h>`： 提供与互联网地址和协议相关的函数，如 `inet_addr()`、`inet_ntoa()` 和 `htons()`。



# TinyWebserver 框架

![框架图](687474703a2f2f7777312e73696e61696d672e636e2f6c617267652f303035544a3263376c79316765306a3161747135686a33306736306c6d3077342e6a7067.jpeg)

# 线程同步机制封装类

## RAII

RAII全称是“Resource Acquisition is Initialization”，直译过来是“资源获取即初始化”.

RAII的核心思想是将资源或者状态与对象的生命周期绑定，通过C++的语言机制，实现资源和状态的安全管理,智能指针是RAII最好的例子

## 信号量 Semaphore

是一种同步机制，用于协调对共享资源的访问，特别是在多线程或多进程环境中。

是一种特殊的变量，它**只能取自然数值**并且只支持两种操作：等待(P)和信号(V)

假设有信号量SV，对其的P、V操作如下：

- P，如果SV的值大于0，则将其减一；若SV的值为0，则挂起执行

- V，如果有其他进行因为等待SV而挂起，则唤醒；若没有，则将SV值加一

信号量的取值可以是任何自然数，最常用的，最简单的信号量是二进制信号量，只有0和1两个值.

> **原子操作**
>
> 是指一个不可中断的操作，它要么完整地执行，要么根本不执行。这意味着在原子操作执行期间，其他线程或进程无法访问或修改正在操作的数据。
>
> 在多线程或多进程环境中，原子操作非常重要，因为它可以防止数据竞争和不一致性。如果没有原子操作，多个线程或进程可能会同时试图修改共享数据，这可能导致数据损坏或不正确的结果。

## 互斥量 Mutex

互斥锁,也成互斥量,可以保护关键代码段,以确保独占式访问.

当进入关键代码段,获得互斥锁将其加锁

离开关键代码段,唤醒等待该互斥锁的线程.

## 条件变量 Condition Variable

条件变量提供了一种线程间的通知机制,当某个共享数据达到某个值时,唤醒等待这个共享数据的线程.









多线程同步，确保任一时刻只能有一个线程能进入关键代码段.

> - 信号量
> - 互斥锁
> - 条件变量

















































# 同步/异步写日志

对于一个服务器而言，不论是在调试中还是在运行中，都需要通过打日志的方式来记录程序的运行情况。

**同步写日志** ：

意味着在继续执行之前，日志消息将立即写入磁盘或其他持久性存储。这确保了即使系统发生故障，日志消息也不会丢失。

但是，同步写日志可能会降低应用程序的性能，因为在日志消息写入磁盘之前，应用程序必须等待。

**异步写日志** ：

意味着日志消息不会立即写入磁盘，而是存储在内存缓冲区中。应用程序可以继续执行，而日志消息将在后台写入磁盘。

这可以提高应用程序的性能，但如果系统发生故障，可能会丢失存储在缓冲区中的日志消息。



#











































# Something new

## 统计代码行数



```shell
find  *.c | xargs wc -l | sort -n
```

