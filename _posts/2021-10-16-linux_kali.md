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
