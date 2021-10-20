---
layout: post
title: Linux_Kali
categories: Linux
description: Linux
keywords: Linux
---

Linux kali的基本使用

### 1、 Linux-kali

Kali Linux是基于Debian的Linux发行版， 设计用于数字取证操作系统。每一季度更新一次。由Offensive Security Ltd维护和资助。最先由Offensive Security的Mati Aharoni和Devon Kearns通过重写BackTrack来完成，BackTrack是他们之前写的用于取证的Linux发行版 。

Kali Linux预装了许多渗透测试软件，包括nmap 、Wireshark 、John the Ripper，以及Aircrack-ng.[2] 用户可通过硬盘、live CD或live USB运行Kali Linux。Kali Linux既有32位和64位的镜像。可用于x86 指令集。同时还有基于ARM架构的镜像，可用于树莓派和三星的ARM Chromebook

下载：[**https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/**](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)

账号和密码都是kali

### 2、配置

#### 2.1 安装虚拟机open-vm-tools-desktop模块

```cmd
sudo apt-get install open-vm-tools-desktop
```

#### 2.2 设置中文

```base
sudo apt-get install ttf-wqy-microhei ttf-wqy-zenhei xfonts-wqy
sudo dpkg-reconfigure locales

看到有*的空格取消
拉到最下面找到 zh_CN.UTF-8 选择字符 zh_CN.UTF-8
重启 reboot

```

#### 2.3 安装python3的pip

```base
sudo apt-get install python3-pip
```

有可能会安装失败

解决pip3 超时下载或失败

```base
# python3的pip3默认源太慢，所以我们为了提升使用效果，通常选择国内源。
# 其实方法很简单，脚本如下：
mkdir -p ~/.pip
vim ~/.pip/pip.conf
# 然后将下列的内容写入~/.pip/pip.conf即可。
[global]
index-url = http://pypi.douban.com/simple
[install]
trusted-host=pypi.douban.com
```

然后执行

```base
sudo apt-get update
pip3 -V
```

如果还不行就需要换源

#### 2.4 更新源

先执行这两步

```base
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
sudo vim /etc/apt/sources.list

```

然后在打开的vim窗口里按`i`键，光标移动到最后复制下面内容，把阿里云的下面的两个的注释打开，然后按`ESC`，vim窗口下面的`insert`消失，输入`:wq`保存，然后重新下载pip3

```base
#阿里云
#deb http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
#deb-src http://mirrors.aliyun.com/kali kali-rolling main non-free contrib

#清华大学
#deb http://mirrors.tuna.tsinghua.edu.cn/kali kali-rolling main contrib non-free
#deb-src https://mirrors.tuna.tsinghua.edu.cn/kali kali-rolling main contrib non-free
 
#浙大
#deb http://mirrors.zju.edu.cn/kali kali-rolling main contrib non-free
#deb-src http://mirrors.zju.edu.cn/kali kali-rolling main contrib non-free

```

保存完之后的一些其他操作

```base
apt-get update 更新系统
apt-get upgrade 升级已安装的所有软件包
apt-get dist-upgrade 升级软件 会自动处理依赖包

vim 编辑器
sudo 使用特权 root权限
:wq 保存

```

### 3、常用命令

1. `id`：查看当前用户id
2. `passwd`：修改密码
3. `passwd root`：修改root用户密码
4. `date`：显示系统日期
5. `sudo`：后面加命令就可以以管理员权限执行
6. `apt-get update`：更新软件列表，会访问源列表里的每个网址，并读取软件列表，然后保存在本地电脑，在软件包管理器里看到的软件列表都是通过update命令更新的
7. `apt-get upgrade`：更新软件，这个命令会把下载的软件列表里的软件与本地安装的软件对比，如果反向本地安装的版本低，就会提示更新
8. `apt-get dist-upgrade`：更新软件，`upgrade`在更新的时候如果发现有其他依赖受到影响就不会更新，而`dist-upgrade`会去尝试更新，所以`dist-upgrade`是会有点风险的
9. `clear`：清屏
10. `reboot`：重启
11. `apt-get install xxxxx`：安装某个依赖包
12. `apt-get autoremove -purge 软件名`：删除包及其依赖的软件包+配置文件
13. `apt-get install 模块名`：安装模块
14. `firefox`：火狐浏览器打开
15. `shutdown -h now`：立刻关闭系统
16. `init 0`：关闭系统
17. `su -l`：切换登录用户，比如切换到root用户`sudo su -l root`就切换了，想退出的话输入`logout`
18. `logout`：注销

### 4、系统信息

1. `arch`：显示机器的处理器架构
2. `uname -m`：显示机器的处理器架构，同上
3. `uname -r`：显示正在使用的内核版本
4. `cat /proc/version`：显示内核版本
5. `cat /proc/net/dev`：显示网络适配器及统计
6. `cat /proc/cpuinfo`：显示cpu info的信息
7. `cat /proc/mounts`：显示已加载的文件系统
8. `man + 某个命令`：可以查看这个命令的帮助文档，比如`man ls`或者`ls --help`

### 5、文本或文件夹操作

#### 5.1 文件

1. 读取文件：
   1. `cat 文件名`比如`cat /etc/passwd`
   2. `less /etc/passwd`
   3. `more /etc/passwd`
2. 解压和压缩
   1. 创建一个文件`touch t.txt`压缩 `gzip t.txt`
   2. 解压：`gunzip t.txt`
   3. 压缩一个文件夹`mkdir test`,压缩`tar -zcvf test.tar.gz test`
   4. 解压：`tar -zxvf test.tar.gz test`
   5. zip压缩：`zip -q -r test.zip test`
   6. zip解压：`unzip test.zip`
3. 删除
   1. `rm -f 文件`：删除文件
   2. `rm 文件夹名`：删除文件夹
   3. `rm -rf 文件/文件夹`：删除文件夹及其目录下的文件
   4. `rm -d`：清空目录

#### 5.2 文件夹

1. `cd`：移动目录，比如`cd ..` 退回上一级
2. `mkdir test`：创建一个test文件夹
3. `mkdir -p`test/t/t/t：递归创建文件夹
4. `touch t.txt`：创建一个t.txt文件
5. `cp`：复制命令，如`cp t.txt t1.txt`
6. `mv`：移动命令，如`mv t1.txt t2.txt`，相当于给t1改名
7. `mv -r`：如果文件夹里面有文件，使用`-r`参数
8. `ls`：显示文件和文件夹名
9. `ls -a`：显示所有文件和文件夹 包括隐藏
10. `ls -l`：显示文件和目录的详细资料
11. `pwd`：显示当前工作路径

文件和文件夹的一些命令是互通的。

#### 5.3 其他

1. `which+软件名`：可以查出软件的目录在哪

2. `./+文件名或apt-get+文件名`：安装文件

### 6、vi编辑器

vi编辑器是系统自带的，它有三种状态

1. 命令模式`command mode`：控制屏幕光标的移动，字符、字或行的删除，移动复制某区段及进入Insert mode下，或者到 last line mode。
2. 插入模式`insert mode`：只有在Insert mode下，才可以做文字输入，按「ESC」键可回到命令行模式。
3. 底行模式`last line mode`：将文件保存或退出vi，也可以设置编辑环境，如寻找字符串，列出行号等

vi编辑器的使用

1. 打开一个文件

   1. `vi filename`：打开或新建文件，并将光标置于第一行首.`vi t.txt`
   2. `vi +n filename`：打开文件，并将光标置于第n行首
   3. `vi +filename`：打开文件，并将光标置于最后一行首

2. 退出，如果不在命令模式下，按`esc`退到命令模式下

   1. `:wq`：退出并保存
   2. `:q!`：退出并忽略更改
   3. `:e!`：放弃所有修改，并打开源文件
   4. `:w`：保存修改

3. 编辑，前提在命令模式下

   1. `i`：按`i`键进入编辑模式，在当前位置前插入
   2. `I`：在当前行首插入
   3. `a`：在当前位置后插入
   4. `A`：在当前行尾插入
   5. `o`：在当前行之后插入一行
   6. `O`：在当前行之前插入一行

4. 查找 ，在命令模式下

   1. `/test`：查找test，按n查找下一个。按N查找上一个
   2. `?test`：查找test，反向查找

   查找很长的词，可以用光标移动到该词上，按`*`或`#`进行搜索，`*`相当于`/`，`#`相当于`?`

5. 删除，在命令模式下

   1. `dd`：删除当前行
   2. `2dd`：删除2行

6. 拷贝，在命令模式下，拷贝有两个操作。一个是复制一个是粘贴

   1. `yy`：拷贝当前行
   2. `nyy`：拷贝当前行后开始的n行，比如`2yy`拷贝当前行及下一行
   3. `p`：在当前光标后粘贴

7. 区块拷贝，拷贝一部分

   1. 将光标移动到要复制的文本开始的地方，按`v`进入可视模式
   2. 将光标移动到要复制文本结束的if，按`y`复制，此时会自动定位到开始的位置，并退出可视模式
   3. 将光标移动到文本结束的地方，按`p`粘贴

8. 行数操作，在命令模式

   1. `set nm 或 set number`：显示行号
   2. `:n`：比如`:222`跳到第222行

9. 撤销操作，`u`，在命令模式下按`u`可以执行撤销

vi和vim的区别不太大，在vim里面写的代码会高亮。

### 7、文件搜索

1. `find / -name file1`：从`/`开始进入根文件系统搜索文件和目录
2. `find / -user user1`：搜索用户user1的文件和目录
3. `find /home/user1 -name *.bin`在目录`/home/user1`：中搜索带有`.bin`结尾的文件
4. `find /usr/bin -type f -atime +100`：搜索在过去100天内未被使用的执行文件
5. `find /usr/bin -type f -mtime -10`：搜索在10天内未被创建或修改过的文件
6. `find .-name'.php' -mmin -30`：查找最近30分钟内修改的当前目录下的'.php'文件
7. `find .-name '.php' -mtime 0`：查找最近24小时修改的当前目录下的.php文件
8. `find .-name '*.inc' -mtime 0 -ls`：查找最近24小时修改的当前目录下的.php文件，并列出详细信息
9. `find . -type -mtime 1`：查找当前目录下，最近24-48小时内修改过的常规文件
10. `find . -type -mtime +1`：查找当前目录下，最近一天前修改过的常规文件

上面的一些参数的具体含义如下

1. `-atime`：文件访问时间
2. `-ctime`：文件创建时间
3. `-mtime`：文件修改时间
4. `-mtime n`：n为数字，意思为在n天之前的‘一天之内’被更改过内容的文件
5. `-mtime +n`：列出在n天之前，不含n天本身被更改过内容的文件
6. `-mtime -n`：列出在n天之内，含n天本身，被更改过内容的文件名
7. `-type f`：代表一个普通格式的文件 二进制文件
8. `-type d`：代表文件夹

其他一些搜索操作

1. `find / -name *.rpm -exec chmod 755 '{}'`：搜索以‘.rpm’结尾的文件并定义其权限
2. `find / -xdev -name *.rpm`：搜索以‘.rpm’结尾的文件，忽略光驱、捷盘等可移动设备
3. `locate *.ps`：寻找以‘.ps’结尾的文件
4. `whereis halt`：显示一个二进制文件、源码或man的位置
5. `which halt`：显示一个二进制文件或可执行文件的完整路径
6. `grep kali /etc/passwd grep`：关键词搜索
7. `find / -name 文件名 2>/dev/null`：搜索某个文件/文件夹 屏蔽出错信息

### 8、用户和群组

创建和修改用户组,都需要用到`sudo`管理员权限

1. `groupadd group_name`：创建一个新用户组
2. `groupdel group_name`：删除一个用户组
3. `cat /etc/group | grep group_name`
4. `usermod -a -G m k1`：`-G m k1`把用户k1附加到m组，`-a`是保留原来的组

查看用户id/组

```base
<!-- m1 是用户名 -->
id m1
```

创建用户的目录

1. `useradd test`：创建一个用户叫test
2. `passwd test`：设置它的密码为 test

创建以及分配目录

```bash
useradd -r -m -s /bin/bash test
```

参数的意思：

- `-r`：建立系统账号
- `-m`：自动建立用户的登入目录
- `-s /bin/bash`：指定用户登入后所使用的shell

切换用户：

```bash
su -root：切换到root用户
su 用户名：切换到普通用户
```


### 9、系统管理

1. `top`：查看正在执行的程序信息
2. `ps -ef`：查看所有进程信息
3. `ps -ef | grep tomcat`：查找指定进程
4. `kali -9 id`：杀死进程，id是进程id
5. `env`：环境变量
6. `echo $PATH`：查看系统变量
7. `hostname`：查看主机名
8. `history`：查看历史命令

### 10、查看磁盘信息

1. `df`：默认不带单位，挂载点，总容量、已用、可用
2. `df -h`：友好显示(带单位)，很多命令的友好显示都是`-h`

### 11、查看内存信息

1. `free`：默认以KB为单位，总容量、已用、可用、缓存大小
2. `free -m`：以MB为单位
3. `free -h`：上面两种都不带单位，只显示数值，这个会带单位

### 12、查看环境变量

1. `echo $JAVA_HOME`：查看Java环境变量
2. `echo $PATH | grep jdk`：查看jdk

### 13、开机自启

很多程序都需要开机自启，比如tomcat\mysql\nginx等

在`etc/rc.d/rc.local`文件中加入开启要自动执行的程序

```bash
vim /etc/rc.d/rc.local

#挂载一个windows网络共享
mount -t smbfs -o username=user,password=pass //WinClient/share /mnt/share 
```

### 14、修改网卡配置

```bash
vi /etc/network/interfaces

auto eth0 #指定网卡，根据ifconfig结果修改
iface eth0 inet static #启动静态ip
address 192.168.0.66  # 设置静态ip
netmask 255.255.255.0  # 子网掩码
gateway 192.168.0.1   # 指定网关

# 重启网络
service networking resteat
# 或者
systemctl restart networking

# 设置临时ip
ifconfig eth0 192.168.0.33
```

### 15、修改网卡DNS服务器

```bash
vi /etc/resovl.conf
nameserver 114.114.114.114

# 重启网卡 有时候重启才生效
service networking restart
```

### 16、服务相关

```bash
# 启动apache2服务器
/etc/init.d/apache2 start

# 打开
/etc/init.d/apache2 start
# 重启
/etc/init.d/apache2 stop

# 启动网卡
/etc/init.d/network start

# 开启路由转发
sudo su root
sudo echo 1 > /proc/sys/net/ipv4/ip_forward
```
### 17、常用网络命令

网络和监控命令类似于这些： hostname, ping, ifconfig, iwconfig, netstat, nslookup, traceroute, finger, telnet, ethtool 用于查看 linux 服务器 ip 地址，管理服务器网络配置，通过 telnet 和 ethernet 建立与 linux 之间的网络链接，查看 linux 的服务器信息等。

1. hostname：查看主机名
2. ping：用于测试网络是不是通
3. ifconfig：网卡信息
4. iwconfig：用于配置或获取无线网络设备信息
5. netstat：命令用于显示各种网络相关信息，如网络连接，路由表，接口状态 (Interface Statistics)，masquerade 连接，多播成员 (Multicast Memberships) 等等。ps：LISTEN和LISTENING的状态只有用-a或者-l才能看到
   1. -a (all)：显示所有选项，默认不显示listen相关
   2. -t(tcp)：仅显示tcp相关选项
   3. -u(udp)：仅显示udp相关
   4. -n：拒绝显示别名，能显示数字的全部转化为数字
   5. -l：仅列出有在listen的服务状态
   6. -p：显示建立相关链接的程序名
   7. -r：显示路由信息，路由表
   8. -e：显示扩展信息，例如uid
   9. -s：按各个协议进行统计
   10. -c：每隔一个固定时间，执行该netstat命令

常用命令：

1. netstat -ano：查看本机所有开放端口
2. netstat -an：查看当前所有已连接端口
3. netstat -nlpt：查看当前所有的tcp端口
4. netstat -nlpu：查看当前所有的udp端口
5. netstat -nlpt | grep 80：查看所有的80端口使用情况
6. netstat -Intup：查看所有的tcp和udp端口



`nslookup`：查询dns记录

```bash
nslookup -qt=type domain [dns-server]
```

其中`type`可以是以下类型

```txt
A 地址记录
AAAA 地址记录
AFSDB Andrew文件系统数据库服务器记录
ATMA ATM地址记录
CNAME 别名记录
HINFO 硬件配置记录，包括CPU、操作系统信息
ISDN 域名对应的ISDN号码
MB 存放指定邮箱的服务器
MG 邮件组记录
MINFO 邮件组和邮箱的信息记录
MR 改名的邮箱记录
MX 邮件服务器记录
NS 名字服务器记录
PTR 反向记录
RP 负责人记录
RT 路由穿透记录
SRV TCP服务器信息记录
TXT 域名对应的文本信息
X25 域名对应的X.25地址记录
```

curl：是一个利用URL语法在命令行下工作的文件传输工具

```bash
# 不带参数 就是发get请求
curl https://www.example.com

# 其他参数看：http://www.ruanyifeng.com/blog/2019/09/curl-reference.html
```

`wget`：是Linux中的一个下载文件的工具

```
wget [选项]... [URL]...
```

`traceroute`：是用来检测发出数据包的主机到目标主机之间所经过的网关数量的工具，traceroute的原理是试图以最小的TTL（存活时间）发出探测包来跟踪数据包到达目标主机所经过的网关，然后监听一个来自网关ICMP的应答。发送数据包的大小默认为38个字节。

有以下的参数：

```txt
-d 使用Socket层级的排错功能。
-f 设置第一个检测数据包的存活数值TTL的大小。
-F 设置勿离断位。
-g 设置来源路由网关，最多可设置8个。
-i 使用指定的网络界面送出数据包。
-I 使用ICMP回应取代UDP资料信息。
-m 设置检测数据包的最大存活数值TTL的大小。
-n 直接使用IP地址而非主机名称。
-p 设置UDP传输协议的通信端口。
-r 忽略普通的Routing Table，直接将数据包送到远端主机上。
-s 设置本地主机送出数据包的IP地址。
-t 设置检测数据包的TOS数值。
-v 详细显示指令的执行过程。
-w 设置等待远端主机回报的时间。

-x 开启或关闭数据包的正确性检验。
```

`telnet`：用于远端登入，多数用于检测某个端口是否开放

```bash
telnet url 80
```

`Finger`：Finger是一种用户信息分享服务。它工作在TCP 79端口，可以用来公开用户的特定信息。Nmap的finger的脚本可以向Finger服务器发送请求，查询并获取用户的相关信息，如登录名、用户名、TTY类型、登录时间等。

`ethtool`：是用于查询及设置网卡参数的命令

### 18、防火墙iptables

```bash
# 查看防火墙规则
iptalbes -L
# 清除规则
iptables -F
# centOS关闭防火墙 
sudo /etc/init.d/iptables stop
sudo service iptables stop

# kali关闭需要安装下面的
# 安装：apt-get install ufw
#关闭：ufw disable # To disable the firewall
#开启：ufw enable # To enable the firewall
```

