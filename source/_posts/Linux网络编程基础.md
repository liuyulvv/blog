---
title: Linux网络编程基础
date: 2021-07-12 19:07:34
categories: [Linux]
tags: [Linux,C++]
description: Linux网络编程基础
---

### socket地址API

#### 主机字节序和网络字节序

一个int至少占用4个字节，这4个字节在内存中排列的顺序将影响它被CPU加载成的数的值，这就是字节序问题。

字节序分为大端字节序和小端字节序：

- 大端字节序：整数的高位字节存储在内存的低地址处，低位字节存储在内存的高地址处。
- 小端字节序：整数的高位字节存储在内存的高地址处，低位字节存储在内存的低地址处。

现代PC大多采用小端字节序，因此小端字节序又被称为主机字节序。当格式化的数据在两台使用不同字节序的主机之间直接传递时，接收端必然错误会错误的解释数据，解决的方法是：发送端总是把要发送的数据转换成大端字节序后发送，接收端根据自身采用的字节序决定是否对数据进行转换（小端机转换，大端机不转换），因此大端字节序也成为网络字节序。

Linux提供了以下4个函数完成主机字节序和网络字节序的转换：

```cpp
#include <netinet/in.h>

// h: host, n: net
// l: long, s: short
unsigned long int htonl(unsigned long int hostlong);
unsigned short int htons(unsigned short int hostshort);
unsigned long int ntohl(unsigned long int netlong);
unsigned short int ntohs(unsigned short int netshort);
```

#### 通用socket地址

socket网络编程接口中表示socket地址的是结构体`sockaddr`，其定义如下：

```cpp
#include <bits/socket.h>

struct sockaddr
{
    sa_family_t sa_family;
    char sa_data[14];
};
```

`sa_family_t`是地址族类型，地址族类型通常与协议族类型对应，常见的协议族和对应的地址族如下：

|协议族|地址族|描述|
|---|---|---|
|PF_UNIX|AF_UNIX|UNIX本地域协议族|
|PF_INET|AF_INET|TCP/IPv4协议族|
|PF_INET6|AF_INET6|TCP/IPv6协议族|

`PF_*`和`AF_*`都定义于`bits/socket.h`头文件中，两者具有完全相同的值。

sa_data用于存放socket地址值，不同协议族的地址值具有不同的含义和长度：

|协议族|地址含义和长度|
|----|----|
|PF_UNIX|文件的路径名，长度可达108字节|
|PF_INET|16bit的端口号和32bit的IPv4地址，共6字节|
|PF_INET6|16bit的端口号，32bit流标识，128bit的IPv6地址，32bit范围ID，共26字节|

由于14字节的sa_data根本无法完全容纳多数协议族的地址值，因此Linux定义了新的通用socket地址结构体：

```cpp
#include <bits/socket.h>

struct sockaddr_storage
{
    sa_family_t sa_family;
    unsigned long int __ss_align; // 对齐内存
    char __ss_padding[128 - sizeof(__ss_align)];
};
```

#### 专用socket地址

上面两个通用socket地址结构体显然很不好用，所以Linux为各个协议族提供了专门的socket地址结构体。

```cpp
#include <sys/un.h>

struct sockaddr_un
{
    sa_family_t sin_family; // 地址族： AF_UNIX
    char sun_path[108];     // 文件路径名
};

// IPv4地址结构体
struct in_addr
{
    u_int32_t s_addr;
};

struct sockaddr_in
{
    sa_family_t sin_family; // 地址族： AF_INET
    u_int16_t sin_port;     // 端口号，网络字节序
    in_addr sin_addr;       // IPv4地址结构体
};

// IPv6地址结构体
struct in6_addr
{
    unsigned char sa_addr[16]; // IPv6地址，网络字节序
};

struct sockaddr_in6
{
    sa_family_t sin6_family; // 地址族： AF_INET6
    u_int16_t sin6_port;     // 端口号，网络字节序
    u_int32_t sin6_flowinfo; // 流信息，应设置为0
    in6_addr sin6_addr;      // IPv6地址结构体
    u_int32_t sin6_scope_id; // scope ID,尚处于实验阶段
};
```

所有的专用socket地址和sockaddr_storage类型的变量在实际使用时需要转化为通用socket类型sockaddr（强制转换即可），因为所有socket编程接口使用的地址参数的类型都是sockaddr。

#### IP地址转换函数

通常人们习惯用可读性好的字符串来表示IP地址，比如用点分十进制字符串表示IPv4地址，以及用十六进制字符串表示IPv6地址。但编程中我们需要先把它们转化为整数（二进制数）才能使用。以下3个函数可用于将点分十进制字符串表示的IPv4地址和用网络字节序整数表示的IPv4地址之间的转换。

```cpp
#include <arpa/inet.h>

in_addr_t inet_addr(const char *strptr);     // 点分十进制字符串IPv4地址转换为网络字节序IPv4地址，失败返回INADDR_NONE
int inet_aton(const char *cp, in_addr *inp); // 同上，转换结果存储至inp，成功返回1，失败返回0
char *inet_ntoa(in_addr in);                 // 网络字节序IPv4地址转换为点分十进制，该函数返回值指向一个静态变量
```

以下2个函数也能完成和前面3个函数同样的功能，并且同时适用于IPv4地址和IPv6地址：

```cpp
#include <arpa/inet.h>

int inet_pton(int af, const char *src, void *dst);                        // af指定地址族: AF_INET 或者 AF_INET6。成功时返回1，失败则返回0并设置errno
const char *inet_ntop(int af, const void *src, char *dst, socklen_t cnt); // cnt指定目标存储单元的大小。inet_ntop成功时返回目标存储单元的地址，失败则返回nullptr并设置errno
```

```cpp
//cnt指定目标存储单元的大小
#include <netinet/in.h>

#define INET_ADDRSTRLEN 16;
#define INET6_ADDRSTRLEN 46;
```

### 创建socket

socket是一个可读、可写、可控制、可关闭的文件描述符，下面的socket系统调用可创建一个socket：

```cpp
#include <sys/types.h>
#include <sys/socket.h>

int socket(int domain, int type, int protocol); // 成功时返回一个socket文件描述符，失败则返回-1并设置errno
```

domain参数告诉系统使用哪个底层协议族。对于TCP/IP协议族而言，设置为PF_INET或PF_INET6，对于UNIX本地域协议族而言，设置为PF_UNIX。

type参数指定服务类型，主要有SOCK_STREAM（流服务）和SOCK_DGRAM（数据报）服务，对于TCP/IP协议族而言，SOCKET_STREAM表示传输层使用TCP协议，SOCKET_DGRAM表示传输层使用UDP协议。自Linux内核版本2.6.27，type参数可以接受上述服务类型与下面两个重要的标志相或的值：SOCK_NONBLOCK和SOCK_CLOEXEC，分别表示将新创的socket设为非阻塞的，以及用fork调用创建子进程时在子进程中关闭该socket。

protocol参数是在前两个参数构成的协议集合下，在选择一个具体的协议，几乎在所有情况下，都应该把它设置为0，表示使用默认协议。

### 命名socket

创建socket时，我们给它指定了地址族，但是并未指定使用该地址族中的哪个具体socket地址，将一个socket与socket地址绑定称为给socket命名。在服务器程序中，我们通常要命名socket，因为只有命名后客户端才能知道如何连接它，客户端则通常不需要命名socket，而是采用匿名方式，即使用操作系统自动分配的socket地址。命名socket的系统调用是bind，其定义如下：

```cpp
#include <sys/types.h>
#include <sys/socket.h>

int bind(int sockfd, const sockaddr *my_addr, socklen_t addrlen); // 成功返回0，失败返回-1并设置errno
```

bind将my_addr所指的socket地址分配给未命名的sockfd文件描述符，addrlen参数指出该socket地址的长度。bind两种常见的errno时EACCES和EADDRINUSE，它们的含义分别是：

- EACCES：被绑定的地址是受保护的地址，仅超级用户能够访问。
- EADDRINUSE：被绑定的地址正在使用中。

### 监听socket

socket被命名之后，还不能马上接受客户连接，我们需要使用如下系统调用来创建一个监听队列以存放待处理的客户连接：

```cpp
#include <sys/socket.h>

int listen(int sockfd, int backlog); // 成功返回0，失败返回-1并设置errno
```

sockfd参数指定被监听的socket，backlog参数提示内核监听队列的最大长度。监听队列的长度如果超过backlog，服务器将不受理新的客户连接，客户端也将收到ECONNREFUSED错误。

### 接受连接

下面的系统从listen监听队列中接受一个连接：

未完待续。