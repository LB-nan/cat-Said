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
