---
layout: post
title: 网络协议
categories: 计算机网络
description: 网络协议初探
keywords: 网络协议 
---

网络协议学习笔记

极客时间网站下网络协议课程的学习笔记，开篇有说`网络协议就是值得你学习，而且是到 40 岁之后依然有价值的知识`。

### 1、 网络协议初识

#### 1.1 协议三要素

1. 语法：就是一段内容要符合一定的规则和格式。
2. 语义：就是一段内容要代表某种意义。
3. 顺序：就是先干啥，后干啥。


#### 1.2 网络五层模型

从下往上的：物理层、链路层、网络层、传输层、应用层，他们的协议大致如下：

> 物理层：网络路线
> 
> 链路层：ARP VLAN STP
> 
> 网络层：ICMP IP OSPF BGP IPSec GPE
> 
> 传输层：UDP TCP
> 
> 应用层：DHCP HTTP HTTPS RTMP P2P DNS GTP RPC

扩展：计算机七层模型OSI：应表会传网数物（应用层、表示层、会话层、传输层、网络层、数据链路层、物理层）


#### 1.3 知识点：

> 只要是在网络上跑的包，都是完整的。可以有下层没上层，绝对不可能有上层没下层， 
> 
> 数据包在内网中主要依靠mac地址投递 。ip 地址必须在有三层设备时才有用 ，而很多内网只有交换机，工作在2层的交换机是不能识别ip地址的。
> 
> 数据包上ip 地址的作用是在外网或internet 上投递用的，内网就不行了，必须要用mac。网络协议的分层和网络实体的分级管理是对应的。


### 2、 IP

IP 地址是一个网卡在网络世界的通讯地址，相当于我们现实世界的门牌号码。

IP地址分为五类，由于分类不科学，在当时设计的时候没想到会发展成现在的样子。

IP分公有IP和私有IP

查看IP的方式：windows: ipconfig;  linux:ifconfig / ip addr

```
  root@test:~# ip addr
  1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default 
      link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
      inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
      inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
  2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
      link/ether fa:16:3e:c7:79:75 brd ff:ff:ff:ff:ff:ff
      inet 10.100.122.2/24 brd 10.100.122.255 scope global eth0
        valid_lft forever preferred_lft forever
      inet6 fe80::f816:3eff:fec7:7975/64 scope link 
        valid_lft forever preferred_lft forever

```

自己配置IP地址,瞎配会导致包发不出去。

```
  // ipconfig
  sudo ifconfig eth1 10.0.0.1/24
  sudo ifconfig eth1 up

  // -------------------------- iproute2
  
  sudo ip addr add 10.0.0.1/24 dev eth1
  sudo ip link set up eth1

```

#### 2.1 CIDR

由于IP地址分类不科学，有了一个折中的方式叫做：`无类型域间选路，简称CIDR`，这种方式打破了原来设计的几类地址的做法，将32位IP地址一分为二，前面是网络号，后面是主机号。

例如：`10.100.122.2/24`，斜杠后面有个数字`24`，这个斜杠就是`CIDR`,24的意思是：32位中，前24位是网络号，后8位是主机号。


#### 2.2 MAC地址

MAC地址是一个网卡的物理地址，一般为一组12位的16进制数。其中前6位代表网卡的生产厂商。后面的位数是设备号。MAC地址全球唯一，网卡生产后会携带。

MAC地址本身的缺陷：因为MAC地址是硬件提供商写在网卡中的，MAC地址虽然唯一但是不能表明用户在整个互联网中的位置，除非维护一个超级大MAC地址对应表，那寻址效率肯定爆炸。但是IP地址解决了这个问题，因为IP地址是网络提供商给你的，所以你在哪里整个网络都是知道的，获取MAC地址是通过ARP协议来完成的。

mac地址全球唯一，它是固化在网卡里的。网卡毕竟是个硬件，需要软件支持，既操作系统识别。重点来了，操作系统识别出来的mac地址是可以更改的，它只不过是一个字符串。我们常说的修改mac指的是修改电脑中记录的既注册表中的记录。

#### 2.3 网络设备的状态标识

< BROADCAST,MULTICAST,UP,LOWER_UP > 是干什么的？

net_device flags，网络设备的状态标识。
UP 表示网卡处于启动的状态；
BROADCAST 表示这个网卡有广播地址，可以发送广播包；
MULTICAST 表示网卡可以发送多播包；
LOWER_UP 表示 L1 是启动的，也即网线插着呢。

#### 2.4 知识点


> ip是地址，有定位功能，MAC是身份证，无定位功能。

> CIDR可以用来判断是不是本地人

> IP分公有IP和私有IP。

> 不同系统的配置文件格式不同，但是无非就是 CIDR、子网掩码、广播地址和网关地址。


### 3、 动态主机配置协议（DHCP）

网管配置多台电脑的IP地址的时候，不可能一台一台手配，就需要使用到DHCP协议，只需要配置一段共享的IP地址，每一台新机器接入都通过DHCP协议，然后在共享的IP地址里面申请一个IP，就自动配置好了。这个IP地址是有时间限制的，属于租一个IP。

#### 3.1 解析DHCP的工作方式

当有新机器进入DHCP协议的时候，需要先`报道`，报道操作称为`DHCP Discover`。

新来的机器使用IP地址(初始为0.0.0.0)发送了一个广播包，目的IP地址为`255.255.255.255`，（广播包封装了，UDP是无连接的传输协议，UDP封装了BOOTP），在广播包里新机器会显示出自己的MAC地址，只能通过MAC地址，我们才知道这个机器是不是一个新机器，新机器需要给他分配一个IP地址（可能有多个，多个的话一般选择最先到达的那个），这个过程称为`DHCP Offer`

新来的机器收到IP地址之后，需要发送一条广播，告诉DHCP Server，我是谁(MAC地址)，我的IP地址是多少(接收到的IP地址)。

DHCP收到新机器的确认IP广播，然后返回给客户机一个`DECP ACK`消息包，表明已经接受并将这一 IP 地址的合法租用信息和其他的配置信息都放入该广播包，发给客户机。

新来的机器收到配置后，配置完成，发送广播，通知所有机器。

客户机在IP的租期过去一半的时候，会发送一个`DHCP request`消息包,DHCP会返回一个`DHCP ACK`消息包，客户机接到服务器的消息包后，会根据包里新的租期以及他更新的TCP/IP参数更新自己的配置，续租完成。

