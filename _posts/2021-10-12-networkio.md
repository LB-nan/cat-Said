---
layout: post
title: Python 网络IO模型
categories: Python
description: python IO
keywords: python IO
---

python 网络IO模型相关

### 1、IO模型

只讨论网络IO，大概有五种

1. blocking IO：阻塞IO
2. nonblocking IO：非阻塞IO
3. IO Multiplexing：IO多路复用
4. signal driven IO：信号驱动IO（不常用）
5. asynchronous IO：异步IO

IO模型发送数据的过程有两个阶段

1. 等待数据准备
2. 将数据从内核拷贝到进程中

常见的网络阻塞状态：

1. accept
2. recv
3. recvfrom

### 2、阻塞IO

所有套接字默认的都是阻塞的，以recvfrom系统调用为例子，它要等到有数据报到达且被复制到应用进程的缓冲区中或者发生了错误才返回。若没有数据到达那么将一直会阻塞。

一个基本的客户端服务端阻塞IO

```py
#服务端

from socket import *

server = socket(AF_INET, SOCK_STREAM)
server.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
server.bind(('127.0.0.1', 8080))

server.listen(5)

while True:
  conn, client = server.accept()

  while True:
    try:
      msg = conn.recv(1024)
      if len(msg) == 0:
        break

      conn.send(msg.upper())

    except Exception:
      break

  conn.close()


# 客户端

from socket import *

client = socket(AF_INET, SOCK_STREAM)
client.connect(('127.0.0.1', 8080))

while True:
  msg = input('请输入》》》').strip()
  if len(msg) == 0:
    continue
  client.send(msg.encode('utf-8'))

  data = client.recv(1024)
  print(data.decode('utf-8'))

```

上面的代码运行效率极其低下，解决办法是开启多进程和多线程，但是多进程和多线程并没有解决IO阻塞问题，该等的地方还在等，只不过开了多个线程，他们彼此互不干扰。

### 3、非阻塞IO

在阻塞IO模型下，客户端去找服务端要数据，服务端再没有数据发送的时候，客户端会阻塞住，等待服务端返回数据，在非阻塞IO模型下，客户端找服务端要数据，服务端如果没有就立刻返回一个错误信息，客户端一直轮询请求，如果有就返回一个正确信息，然后发送数据，这样客户端就没有了等待阶段，就没了IO阻塞第一阶段的影响，有数据就继续第二个阶段。

实现代码：

```py
# 服务端
import  socket

server = socket.socket()
server.bind(('127.0.0.1', 8080))
server.listen(5)

# 将所有的网络阻塞变为非阻塞
server.setblocking(False)

r_list = []
del_list = []
while True:
    try:
        conn,addr = server.accept()
        r_list.append(conn)
    except BlockingIOError as e:
        # 执行其他操作
        for conn in r_list:
            try:
                data = conn.recv(1024)

                if len(data) == 0: 
                    conn.close()
                    del_list.append(conn)
                    continue
                conn.send(data.upper())
                
            except BlockingIOError as e:
                continue
            except ConnectionResetError as e:
                conn.close()
                del_list.append(conn)
        
        # 回收无用的链接
        for conn in del_list:
            r_list.remove(conn)
        del_list.clear()


# 客户端

from socket import *

client = socket(AF_INET, SOCK_STREAM)
client.connect(('127.0.0.1', 8080))

while True:
  msg = input('请输入》》》').strip()
  if len(msg) == 0:
    continue
  client.send(msg.encode('utf-8'))

  data = client.recv(1024)
  print(data.decode('utf-8'))

```

非阻塞IO模型会长时间占用CPU并且不干活，持续轮询，极度消耗cpu资源。

### 4、IO多路复用

操作系统提供的监管机制能够帮忙监管socket对象和conn对象，并且可以监管多个，只要有对象触发了就立刻返回可执行对象。

IO的两个阶段的第一个阶段在非阻塞IO模型下是由写代码监管，使用多路复用是交给操作系统监管，虽然还是有这个阶段，但是不需要写代码了。

当IO对象只有一个的时候，IO多路复用的效率甚至是没有阻塞IO高的，但是IO多路复用可以一次性监管很多对象，并且监管机制是操作系统本身有的，使用的话需要导入对应的模块`select`

- select模块可以监管`socket/conn对象`

IO多路复用是针对非阻塞IO的一个优化，具体实现

```py
# 服务端
import socket, select

server = socket.socket()
server.bind(('127.0.0.1', 8081))
server.listen(5)
# 将所有的网络阻塞变为非阻塞
server.setblocking(False)

read_list = [server]

# res = select.select(read_list, [], [])
# print(res)  # ([<socket.socket fd=3, family=AddressFamily.AF_INET, type=SocketKind.SOCK_STREAM, proto=0, laddr=('127.0.0.1', 8081)>], [], [])
while True:
    # 该方法返回三个对象，解构出三个变量，只用了第一个，我们只监听第一个
    r_list, w_list, x_list = select.select(read_list, [], [])

    for i in r_list:
        if i is server:
            conn, addr = i.accept()
            read_list.append(conn)
        else:
            res = i.recv(1024)
            if len(res) == 0:
                i.close()
                read_list.remove(i)
                continue
            i.send(b'dddddd')

# 客户端

from socket import *

client = socket(AF_INET, SOCK_STREAM)
client.connect(('127.0.0.1', 8081))

while True:
  msg = input('请输入》》》').strip()
  if len(msg) == 0:
    continue
  client.send(msg.encode('utf-8'))

  data = client.recv(1024)
  print(data.decode('utf-8'))

```

实现多路复用监管机制有很多

1. select机制：Windows Linux都有
2. poll机制：只有Linux有，并且poll比select监管的数量更多，这俩的内部原理都是for循环，当监管的量巨大，然后头部新增了一个监管，就会等之前的都遍历完才可以重新监管，会有时间差
3. epoll机制：只在Linux有，它给每一个监管对象都绑定了一个回调机制，一旦有相应，回调机制立刻发起提醒

不同的平台有不同的监管机制，有兼容问题，可以通过`selectors`模块解决。它可以根据平台的不同自动选择对应的监管机制。

### 5、异步IO

`Asynchronous I/O`对比上面几个是效率最高的一个。

本身python是没法做到异步IO的，需要使用其他模块或者框架

- 模块：asyncio模块
- 框架：sanic tronado twisted，都是异步框架，异步框架的优点就是速度快。
