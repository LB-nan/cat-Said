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

#### 3.4 子域名解析

通过子域名的解析指向 也有可能指向目标的同一个IP上。可以使用工具对子域名进行穷举。

在线子域名查询：

1. `https://securitytrails.com`
2. `http://tool.chinaz.com`
3. `https://phpinfo.me/domain/`：子域名扫描工具

找到子域名后，可以把子域名保存下了，然后做域名转ip的批量解析，可以找到真实IP。
