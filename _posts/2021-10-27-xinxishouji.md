---
layout: post
title: 信息收集一
categories: 网络安全
description: 信息收集
keywords: 信息收集
---

渗透的本质是信息收集。

### 1、免责声明

在阅读内容前请先阅读《**中华人民共和国网络安全法**》、《**中华人民共和国刑法**》等相关法律法规条款。

阅读浏览学习本部分安全内容的时候，请**遵守国家法律法规**！

**本博客的内容仅供学习和研究技术使用，禁止将本博客的文章里提及的技术用于非法用途，造成任何后果自行承担，本人概不负责。**

本篇博文内提及的操作系统、工具等应用程序只供学习交流，不提供相关文件包或应用程序。

### 2、法律条款

条款如下！

#### 2.1 中华人民共和国网络安全法（2017年6月1日起施行）

- 第二十二条 任何个人和组织不得从事入侵他人网络、干扰他人网络正常功能、窃取网络数据等危害网络安全的活动；不得提供从事入侵网络、干扰网络正常功能、窃取网
络数据等危害网络安全活动的工具和制作方法；不得为他人实施危害网络安全的活动提供技术支持、广告推广、支付结算等帮助。
- 第三十八条 任何个人和组织不得窃取或者以其他非法方式获取公民个人信息，不得出售或者非法向他人提供公民个人信息。
- 第六十三条 违反本法规定，给他人造成损害的，依法承担民事责任。
- 第六十四条 违反本法规定，构成犯罪的，依法追究刑事责任。

#### 2.2 中华人民共和国刑法（285.286）

- 第二百八十五条 违反国家规定，侵入国家事务、国防建设、尖端科学技术领域的计算机信息系统的，处三年以下有期徒刑或者拘役。
- 第二百八十六条 违反国家规定，对计算机信息系统功能进行删除、修改、增加、干扰，造成计算机信息系统不能正常运行，后果严重的，处五年以下有期徒刑或者拘役；后果特别严重的，处五年以上有期徒刑。违反国家规定，对计算机信息系统中存储、处理或者传输的数据和应用程序进行删除、修改、增加的操作，后果严重的，依照前款的规定处罚。
中华人民共和国刑法修正案7（第九条）
- 在刑法第二百八十五条中增加两款作为第二款、第三款：“违反国家规定，侵入前款规定以外的计算机信息系统或者采用其他技术手段，获取该计算机信息系统中存储、处理或者传输的数据，或者对该计算机信息系统实施非法控制，情节严重的，处三年以下有期徒刑或者拘役，并处或者单处罚金；情节特别严重的，处三年以上七年以下有期徒刑，并处罚金。
“提供专门用于侵入、非法控制计算机信息系统的程序、工具，或者明知他人实施侵入、非法控制计算机信息系统的违法犯罪行为而为其提供程序、工具，情节严重的，依照前款的规定处罚。

### 3、简介

渗透的本质是信息收集，信息收集也叫做资产收集。

信息收集是渗透测试的前期主要工作，是非常重要的环节，收集足够多的信息才能方便接下来的测试，信息收集主要是收集网站的域名信息、子域名信息、目标网站信息、目标网站真实IP、敏感/目录文件、开放端口和中间件信息等等。通过各种渠道和手段尽可能收集到多的关于这个站点的信息，有助于我们更多的去找到渗透点，突破口。

### 4、信息收集的分类

1. 服务器的相关信息，如真实ip、系统类型、版本、开放端口、WAF等
2. 网站指纹识别，包括CMD,CDN,证书等，dns记录
3. whois信息：姓名、备案、邮箱、电话反查（邮箱丢社工库，社工准备等）
4. 子域名收集、旁站、c段收集
5. Google hacking针对化搜索，word、excel、pdf文件、中间件版本、弱口令扫描等
6. 扫描网站目录结构、爆后台、网站banner、测试文件、备份等敏感文件泄露等
7. 传输协议，通用漏洞，exp，GitHub源码等

### 5、常见的方法

1. whois查询：域名在注册的时候 需要填入个人或者企业信息 如果没有设置隐藏属性可以查询出来 通过备案号 查询个人或者企业信息 也可以whois反查注册人 邮箱 电话 机构 反查更多得域名和需要得信息。
2. 收集子域名：域名分为根域名和子域名
3. 端口扫描：服务器需要开放服务，就必须开启端口，常见的端口是tcp 和udp两种类型 ，范围 0-65535 通过扫得到的端口，访问服务 规划下一步渗透。
4. 查找真实ip：企业的网站，为了提高访问速度，或者避免黑客攻击，用了cdn服务，用了cdn之后真实服务器ip会被隐藏
5. 探测旁站及C段：旁站:一个服务器上有多个网站 通过ip查询服务器上的网站；c段:查找同一个段 服务器上的网站。可以找到同样网站的类型和服务器，也可以获取同段服务器进行下一步渗透。
6. 网络空间搜索引擎：通过这些引擎查找网站或者服务器的信息，进行下一步渗透
7. 扫描敏感目录/文件：通过扫描目录和文件，大致了解网站的结构，获取突破点，比如后台，文件备份，上传点
8. 指纹识别：获取网站的版本，属于那些cms管理系统，查找漏洞exp，下载cms进行代码审计。

![信息收集](/images/blog/img/4.png)

#### 5.1 大型项目的收集

比如某个大型集团

1. 查询公司集团旗下的网站
2. 备案号、备案名、查询公司相关的域名、子域名、域名下的网站、cms识别、判断网站类型（原创开发、二次开发、使用现场网站管理系统，oa版本系统）
3. ip的分布、服务器信息，包括端口和开发的服务器
4. 线上系统、业务相关系统、企业邮箱、oa版本(系统、生产环境、测试环境、边界、设备、vpm)
5. 集团有没有防火墙、防火墙的类型、wifi进入公司内部收集资产
6. 社工客户、销售、发链接、脉脉、招聘、HR、qq钉钉、微信等
7. 网络空间的搜索、GitHub 谷歌hacking、fofa sohdan、 钟馗之眼

### 6、在线whois查询

通过whois来对域名信息进行查询，可以查到注册商、注册人、邮箱、DNS解析服务器、注册人联系电话等，因为有些网站信息查得到，有些网站信息查不到，所以推荐以下信息比较全的查询网站，直接输入目标站点即可查询到相关信息

1. 站长之家域名WHOIS信息查询地址 `http://whois.chinaz.com/`
2. 爱站网域名WHOIS信息查询地址 `https://whois.aizhan.com/`
3. 腾讯云域名WHOIS信息查询地址 `https://whois.cloud.tencent.com/`
4. 美橙互联域名WHOIS信息查询地址 `https://whois.cndns.com/`
5. 爱名网域名WHOIS信息查询地址 `https://www.22.cn/domain/`
6. 易名网域名WHOIS信息查询地址 `https://whois.ename.net/`
7. 中国万网域名WHOIS信息查询地址 `https://whois.aliyun.com/`
8. 西部数码域名WHOIS信息查询地址 `https://whois.west.cn/`
9. 新网域名WHOIS信息查询地址 `http://whois.xinnet.com/domain/whois/index.jsp`
10. 纳网域名WHOIS信息查询地址 `http://whois.nawang.cn/`
11. 中资源域名WHOIS信息查询地址 `https://www.zzy.cn/domain/whois.html`
12. 三五互联域名WHOIS信息查询地址 `https://cp.35.com/chinese/whois.php`
13. 新网互联域名WHOIS信息查询地址 `http://www.dns.com.cn/show/domain/whois/index.do`
14. 国外WHOIS信息查询地址 `https://who.is/`

### 7、在线网站备案查询

网站备案信息是根据国家法律法规规定，由网站所有者向国家有关部门申请的备案，如果需要查询企业备案信息（单位名称、备案编号、网站负责人、电子邮箱、联系电话、法人等），推荐以下网站查询

1. 天眼查 `https://www.tianyancha.com/`
2. ICP备案查询网`http://www.beianbeian.com/`
3. 爱站备案查询`https://icp.aizhan.com/`
4. 域名助手备案信息查询 `http://cha.fute.com/index`

### 8、收集域名

收集子域名可以扩大测试范围，同一域名下的二级域名都属于目标范围。

子域名中的常见资产类型一般包括办公系统，邮箱系统，论坛，商城，其他管理系统，网站管理后台也有可能出现子域名中。

首先找到目标站点，在官网中可能会找到相关资产（多为办公系统，邮箱系统等），关注一下页面底部，也许有管理后台等收获。

查找目标域名信息的方法有：

1. FOFA title="公司名称"
2. 百度 intitle=公司名称
3. Google intitle=公司名称
4. 站长之家，直接搜索名称或者网站域名即可查看相关信息：`http://tool.chinaz.com/`
5. 钟馗之眼 site=域名即可

### 9、域名的类型

分别有：

1. A记录：address记录，用来指定主机名或域名对应ip地址记录，用户可以将该域名下的网站服务器指向到资金的web server上，同时也可以设置域名的二级域名
2. CNAME：别名记录也称为规范名字，这种记录允许将多个名字映射到同一台计算机上。通常用于同时提供WWW和MAIL服务的计算机。例如，有一台计算机名为“host.mydomain.com”（A记录）。它同时提供WWW和MAIL服务，为了便于用户访问服务。可以为该计算机设置两个别名（CNAME）：WWW和MAIL。这两个别名的全称就是`www.mydomain.com`和`mail.mydomain.com`。实际上他们都指向“host.mydomain.com”。同样的方法可以用于当您拥有多个域名需要指向同一服务器IP，此时您就可以将一个域名做A记录指向服务器IP然后将其他的域名做别名到之前做A记录的域名上，那么当您的服务器IP地址变更时您就可以不必麻烦的一个一个域名更改指向了 只需要更改做A记录的那个域名其他做别名的那些域名的指向也将自动更改到新的IP地址上了。
   1. 检测CNAME记录的方法
   2. 进入命令状态，打开cmd
   3. 输入`nslookup -q=cname 域名`
3. MX记录：是邮件交换记录，它指向一个邮件服务器，用于电子邮件系统发邮件时根据 收信人的地址后缀来定位邮件服务器。例如，当Internet上的某用户要发一封信给 `user@mydomain.com` 时，该用户的邮件系统通过DNS查找mydomain.com这个域名的MX记录，如果MX记录存在， 用户计算机就将邮件发送到MX记录所指定的邮件服务器上。
4. TXT记录：TXT记录一般指为某个主机名或域名设置的说明, 检测TXT记录`nslookup -q=txt 域名`
5. NS记录：ns记录全称为Name Server 是一种域名服务器记录，用来明确当前你的域名是由哪个DNS服务器来进行解析的。

找到官网后，再收集子域名，下面推荐几种子域名收集的方法，直接输入domain即可查询

### 10、查询子域名

几个在线查询的地址：

1. `https://phpinfo.me/domain/`
2. `https://www.t1h2ua.cn/tools/`
3. DNS侦测：`https://dnsdumpster.com/`
4. IP138`https://site.ip138.com/moonsec.com/domain.htm`
5. FOFA搜索引擎搜索子域名：`https://fofa.so/`，语法`domain="baidu.com"`。
6. hackertarget查询子域名：`https://hackertarget.com/find-dns-host-records/`注意：通过该方法查询子域名可以得到一个目标大概的ip段，接下来可以通过ip来收集信息
7. 360测绘空间`https://quake.360.cn/`,语法`domain:"*.freebuf.com"`

以上方法无需爆破，查询速度快，需要快速收集资产时可以优先使用。

使用工具查询：

1. layer子域名挖掘机

2. subDomainsBrute：python3版本环境

   1. 打开cmd，`pip install aiodns`
   2. `cd 工具包所在的目录`，然后运行`subDomainsBrute.py 域名`
   3. `subDomainsBrute.py freebuf.com`
   4. 运行过找到DNS，然后持续检索，找到的内容会保存到当前目录下的`freebuf.com.txt`，域名的txt文件夹
   5. 指定一个保存的文件：`subDomainsBrute.py freebuf.com --full -o freebuf2.txt`

3. sublist3r：最好是在Windows里面使用python 2.7版本。方法为爆破子域名，由于字典比较强大，所以效率较高。

   1. 使用git下载，下载完需要安装一个工具

   2. 安装`requirements.txt`文件夹内的依赖，之间读取然后安装：`sudo pip install -r requirements.txt`

      ```txt
      -h ：帮助
      -d ：指定主域名枚举子域名
      -b ：调用subbrute暴力枚举子域名
      -p ：指定tpc端口扫描子域名
      -v ：显示实时详细信息结果
      -t ：指定线程
      -e ：指定搜索引擎
      -o ：将结果保存到文本
      -n ：输出不带颜色 
      ```

   3. 默认参数扫描子域名：`python sublist3r.py -d baidu.com`

   4. 使用暴力枚举子域名：`python sublist3r -b -d baidu.com`

4. OneForALL：kali下的工具

   1. 安装：`pip3 install --user -r requirements.txt -i https://mirrors.aliyun.com/pypi/simple/`

   2. 默认使用命令：`python3 oneforall.py --target baidu.com run`，结果会保存在results里面

   3. 爆破子域名：

      ```bash
      # 一些其他参数的方式
      brute.py --target domain.com --word True run
      brute.py --targets ./domains.txt --word True run
      brute.py --target domain.com --word True --concurrent 2000 run
      brute.py --target domain.com --word True --wordlist subnames.txt run
      brute.py --target domain.com --word True --recursive True --depth 2 run
      brute.py --target d.com --fuzz True --place m.*.d.com --rule '[a-z]' run
      brute.py --target d.com --fuzz True --place m.*.d.com --fuzzlist subnames.txt run
      
      ```

5. Wydomain：在windows python2.7里面使用，kali可能有问题。

   ```bash
   # 依赖安装
   $ sudo pip install -r requirements.txt
   
   # 子域名字典穷举结果保存在result/aliyun.com 目录下的dnsburte.json文件
   $ python dnsburte.py -d aliyun.com -f dnspod.csv -o aliyun.log
   
   # 各个api查询的结果保存在result/aliyun.com目录中对应的json文件中
   $ python wydomain.py -d aliyun.com
   ```

6. FuzzDomain：一个工具，打开输入域名，就开始就好了，结果导出到文件夹内。

### 11、端口扫描

当确定了目标大概的ip段后，可以先对ip的开放端口进行探测，一些特定服务可能开起在默认端口上，探测开放端口有利于快速收集目标资产，找到目标网站的其他功能站点。

#### 11.1 masscan端口扫描

打开kali，找一个自己想扫的ip地址

```bash
sudo masscan 192.168.0.200 -p 1-65535 --rate=1000
```

#### 11.2 nmap扫描端口和探测端口信息

打开kali，进行使用，常用参数如:

1. `nmap -sV 192.168.0.200`
2. `nmap -sT 192.168.0.200`
3. `nmap -Pn -A -sC 192.168.0.200`
4. `nmap -sU -sT -p 0-65535 192.168.0.200`

用于扫描目标主机服务版本号与开放的端口

一些常用参数：

1. `-sU`：扫描UDP
2. `-sT`：扫描TCP
3. `-sV`：探测开放端口以确定服务/版本信息
4. `-A`：查看端口指纹
5. `-sL`：列表扫描 - 简单列出要扫描的目标

如需扫描多个ip或ip段，可以将他们保存到一个txt文件中，然后导入

```bash
nmap -iL ip.txt
```

Nmap为端口探测最常用的方法，操作方便，输出结果非常直观

#### 11.3 在线端口检测

`http://coolaf.com/tool/port`

### 12、渗透端口

21,22,23,1433,152,3306,3389,5432,5900,50070,50030,50000,27017,27018,11211,9200,9300,7001,7002,6379,5984,873,443,8000-9090,80-89,80,10000,8888,8649,8083,8080,8089,9090,7778,7001,7002,6082,5984,4440,3312,3311,3128,2601,2604,2222,2082,2083,389,88,512,513,514,1025,111,1521,445,135,139,53

### 13、渗透常见端口及对应服务

#### 13.1 web类(web漏洞/敏感目录)

第三方通用组件漏洞struts thinkphp  jboss  ganglia  zabbix

1. 80
2. 80-89
3. 8000-9090

#### 13.2 数据库类(扫描弱口令)

1. 1433 MSSQL
2. 1521 Oracle
3. 3306 MySQL
4. 5432 PostgreSQL

#### 13.3 特殊服务类(未授权/命令执行类/漏洞)

1. 443 SSL心脏滴血
2. 873 Rsync未授权
3. 5984 CouchDB `http://xxx:5984/_utils/`
4. 6379 redis未授权
5. 7001,7002 WebLogic默认弱口令，反序列
6. 9200,9300 elasticsearch 参考WooYun: 多玩某服务器ElasticSearch命令执行漏洞
7. 11211 memcache未授权访问
8. 27017,27018 Mongodb未授权访问
9. 50000 SAP命令执行
10. 50070,50030 hadoop默认端口未授权访问

#### 13.4 常用端口类(扫描弱口令/端口爆破)

1. 21 ftp
2. 22 SSH
3. 23 Telnet
4. 2601,2604 zebra路由，默认密码zebra
5. 3389 远程桌面

#### 13.5 端口合计详情

```txt
21 ftp 
22 SSH 
23 Telnet 
80 web 
80-89 web 
161 SNMP 
389 LDAP 
443 SSL心脏滴血以及一些web漏洞测试 
445 SMB 
 
512,513,514 Rexec 
873 Rsync未授权 
1025,111 NFS 
1433 MSSQL 
1521 Oracle:(iSqlPlus Port:5560,7778) 
2082/2083 cpanel主机管理系统登陆 （国外用较多）
2222 DA虚拟主机管理系统登陆 （国外用较多） 
2601,2604 zebra路由，默认密码zebra
3128 squid代理默认端口，如果没设置口令很可能就直接漫游内网了 
3306 MySQL 
3312/3311 kangle主机管理系统登陆 
3389 远程桌面 
4440 rundeck 参考WooYun: 借用新浪某服务成功漫游新浪内网 
5432 PostgreSQL 
5900 vnc 
5984 CouchDB http://xxx:5984/_utils/ 
6082 varnish 参考WooYun: Varnish HTTP accelerator CLI 未授权访问易导致网站被直接篡改或者作为代理进入内网 
6379 redis未授权 
7001,7002 WebLogic默认弱口令，反序列 
7778 Kloxo主机控制面板登录 
8000-9090 都是一些常见的web端口，有些运维喜欢把管理后台开在这些非80的端口上 
8080 tomcat/WDCP主机管理系统，默认弱口令 
8080,8089,9090 JBOSS 
8083 Vestacp主机管理系统 （国外用较多） 
8649 ganglia 
8888 amh/LuManager 主机管理系统默认端口 
9200,9300 elasticsearch 参考WooYun: 多玩某服务器ElasticSearch命令执行漏洞 
10000 Virtualmin/Webmin 服务器虚拟主机管理系统 
11211 memcache未授权访问 
27017,27018 Mongodb未授权访问 
28017 mongodb统计页面 
50000 SAP命令执行 
50070,50030 hadoop默认端口未授权访问
```

#### 13.6 常见的端口和攻击方法

![常见端口和攻击方法](/images/blog/img/3.png)

如图。
