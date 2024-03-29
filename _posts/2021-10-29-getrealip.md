---
layout: post
title: 信息收集三
categories: 网络安全
description: 信息收集
keywords: 信息收集
---

获取真实ip

### 1、简介

很多网址使用了CDN加速服务，此服务会隐藏真实ip，加速静态文件的访问速度，而且你请求网站服务时，cdn服务会根据你所在的地区，选择合适的线路给予你访问，由此达网站加速的效果，cdn不仅可以加速网站访问，还可以提供waf(防火墙)服务，如防止cc攻击，SQL注入拦截等多种功能，再说使用cdn的成本不太高，很多云服务器也免费提供此服务。在进行黑盒测试的时候，往往成了拦路石，所以掌握cdn找真实ip成了不得不掌握的一项技术。

![CDN](/images/blog/img/CDN.png)

### 2、判断是否使用CDN

可以使用`超级ping`，显示不同的地区ping一个网址的域名来查看。

1. `http://ping.chinaz.com/`
2. `http://ping.aizhan.com/`
3. `https://www.17ce.com/`

不同的地区有不同的ip，就可以确定使用CDN了。

### 3、找真实ip方法集合

找到真实IP可以继续旁站检测，找其他站点进行突破，也可以绕过cdn进行访问，从而绕过waf针对攻击语句的拦截 发现有攻击语句就会对攻击的IP封堵。

#### 3.1 通过DNS历史绑定记录查找ip

通过一些DNS解析网站去找，可能会找到在未绑DNS之前的解析记录。

DNS解析网站：

1. `https://dnsdb.io/zh-cn/ ###DNS查询`
2. `https://x.threatbook.cn/ ###微步在线`
3. `http://viewdns.info/ ###DNS、IP等查询`
4. `https://tools.ipip.net/cdn.php ###CDN查询IP`
5. `https://sitereport.netcraft.com/?url=域名` ：查看CND历史

当找到了真实IP，通过修改`hosts`的记录来绑定网站和真实IP，从而绕过DNS服务，就绕过了DNS服务的waf服务。

拿到真实ip修改完hosts之后，可以通过一些方法来检测有端口开放没有，或者验证网站是否正常打开

```bash
# 检测某个端口是否开放
$ nc ip port -vv

# 检测curl能不能获取到
$ curl 域名

# nmap扫描端口
$ nmap -sT -A IP
```

另外：拿到多个IP可以打开浏览器访问IP+端口，看看报错，是无法访问，还是nginx报错，如果是nginx报错就是DNS的IP，如果无法访问应该就是真实IP了。

#### 3.2 子域名解析

通过子域名的解析指向 也有可能指向目标的同一个IP上。可以使用工具对子域名进行穷举。

在线子域名查询：

1. `https://securitytrails.com`
2. `http://tool.chinaz.com`
3. `https://phpinfo.me/domain/`：子域名扫描工具

找到子域名后，可以把子域名保存下了，然后做域名转ip的批量解析，可以找到真实IP。

#### 3.3 国外DNS获取真实IP

部分CDN只针对国内的ip访问做了cdn优化，如果国外IP访问，就可以直接访问真实IP

#### 3.4 利用ico查找IP

1. 可以在`FOFA.so`里面通过icon图标搜索
2. 可以在`zoomeye.org`里面通过icon搜索

#### 3.5 360测绘中心

`https://quake.360.cn`

#### 3.6 利用SSL证书寻找IP

证书颁发机构(CA)必须将他们发布的每个SSL/TLS证书发布到公共日志中，SSL/TLS证书通常包含域名、子域名和电子邮件地址。因此SSL/TLS证书成为了攻击者的切入点。

获取网站SSL证书的HASH再结合Censys

利用Censys搜索网站的SSL证书及HASH，在`https://crt.sh`上查找目标网站SSL证书的HASH

再用Censys搜索该HASH即可得到真实IP地址

#### 3.7 Censys

Censys是一个国外的测绘网站，和360测绘中心类似，但是是免费的。

Censys工具就能实现对整个互联网的扫描，Censys是一款用以搜索联网设备信息的新型搜索引擎，能够扫描整个互联网，Censys会将互联网所有的ip进行扫面和连接，以及证书探测。

若目标站点有https证书，并且默认虚拟主机配了https证书，我们就可以找所有目标站点是该https证书的站点。

#### 3.8 邮箱获取真实IP

网站在发邮件的时候，会附带真实的IP地址 进入邮箱 查看源文件头部信息 选择from，有可能会有真实IP

#### 3.9 网站敏感文件获取真实IP

主要就是看源代码的几种途径查找

1. 文件探针
2. phpinfo
3. 网站源代码
4. 信息泄露
5. GitHub信息泄露
6. js文件

#### 3.10 F5 LTM解码法

当渗透进内网，查ip的时候可以用这个方法查内网ip。

当服务器使用F5 LTM做负载均衡时，通过对set-cookie关键字的解码真实ip也可被获取，

例如：`Set-Cookie: BIGipServerpool_8.29_8030=487098378.24095.0000`，先把第一小

节的十进制数即487098378取出来，然后将其转为十六进制数1d08880a，接着从后至前，

以此取四位数出来，也就是0a.88.08.1d，最后依次把他们转为十进制数10.136.8.29，也就

是最后的真实ip。

`rverpool-cas01=3255675072.20480.0000; path=/`

3255675072 转十六进制 c20da8c0 从右向左取 c0a80dc2 转10进制 192 168 13 194

#### 3.11 APP获取真实ip

如果网站有app，使用Fiddler或BurpSuite抓取数据包 可能获取真实IP

模拟器MuMu模拟器抓包

#### 3.12 配置不当获取真实IP

在配置CDN的时候，需要指定域名、端口等信息，有时候小小的配置细节就容易导致CDN防护被绕过。

- 案例1：为了方便用户访问，我们常常将`www.test.com` 和 `test.com` 解析到同一个站点，而CDN只配置了`www.test.com`，通过访问`test.com`，就可以绕过 CDN 了。
- 案例2：站点同时支持http和https访问，CDN只配置 https协议，那么这时访问http就可以轻易绕过。

#### 3.13 其他方式

1. banner：可以通过一些引擎搜素，找到目标服务器 IP 段后，可以直接进行暴力匹配 ，使用zmap、masscan 扫描 HTTP banner，然后匹配到目标域名的相同 banner

2. 长期关注：在长期渗透的时候，设置程序每天访问网站，可能有新的发现。每天零点 或者业务需求增大 它会换ip 换服务器的。

3. 流量攻击：发包机可以一下子发送很大的流量。

   这个方法是很笨，但是在特定的目标下渗透，建议采用。

   cdn除了能隐藏ip，可能还考虑到分配流量

   不设防的cdn 量大就会挂，高防cdn 要大流量访问。

   经受不住大流量冲击的时候可能会显示真实ip。

   站长->业务不正常->cdn不使用->更换服务器。

4. 被动获取：被动获取就是让服务器或网站主动连接我们的服务器，从而获取服务器的真实IP

   如果网站有编辑器可以填写远程url图片，即可获取真实IP

   如果存在ssrf漏洞 或者xss 让服务器主动连接我们的服务器 均可获取真实IP。

5. 扫全网获取真实ip

扫描工具：

1. `https://github.com/superfish9/hackcdn`
2. `https://github.com/boy-hack/w8fuckcdn`
3. `https://github.com/zmap/zmap`：ZMap号称是最快的互联网扫描工具，能够在45分钟扫遍全网。
4. `https://github.com/robertdavidgraham/masscan`：Masscan号称是最快的互联网端口扫描器，最快可以在六分钟内扫遍互联网。

### 4、隐藏资产探测-hosts碰撞

很多时候访问目标资产IP响应多为：401、403、404、500，但是用域名请求却能返回正常的业务系统（禁止IP直接访问），因为这大多数都是需要绑定host才能正常请求访问的 （目前互联网公司基本的做法）（域名删除了A记录，但是反代的配置未更新），那么我们就可以通过收集到的目标的 域名 和 目标资产 的IP段组合起来，以 IP段+域名 的形式进行捆绑碰撞，就能发现很多有意思的东西。

在发送http请求的时候，对域名和IP列表进行配对，然后遍历发送请求 （就相当于修改了本地的hosts文件一样），并把相应的title和响应包大小拿回 来做对比，即可快速发现一些隐蔽的资产。

进行hosts碰撞需要目标的域名和目标的相关IP作为字典
