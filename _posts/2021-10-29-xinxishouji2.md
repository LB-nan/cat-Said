---
layout: post
title: 信息收集二
categories: 网络安全
description: 信息收集
keywords: 信息收集
---

渗透的本质是信息收集。

### 1、旁站和C段

旁站往往存在业务功能站点，建议先收集已有IP的旁站，再探测C段，确认C段目标后，再在C段的基础上再收集一次旁站

旁站是和已知目标站点在同一服务器但不同端口的站点，通过以下方法搜索到旁站后，先访问一下确定是不是自己需要的站点信息。

查找的话可以通过下面的几个方法：

1. 站长之家：同ip网站查询`http://stool.chinaz.com/same` / `https://chapangzhan.com/`

2. google hacking：`https://blog.csdn.net/qq_36119192/article/details/84029809`

3. 网络空间搜索引擎，比如`FOFA`，可以直接搜索旁站和C段。该方法效率较高，并能够直观地看到站点标题，但也有不常见端口未收录的情况，虽然这种情况很少，但之后补充资产的时候可以用下面的方法nmap扫描再收集一遍

4. 在线C段`webscan.cc / https://c.webscan.cc/`

5. 利用脚本：

   ```python
   # 需要先安装requests
   # pip install requests
   #coding:utf-8
   import requests
   import json
   
   def get_c(ip):
       print("正在收集{}".format(ip))
       url="http://api.webscan.cc/?action=query&ip={}".format(ip)
       req=requests.get(url=url)
       html=req.text
       data=req.json()
       if 'null' not in html:
           with open("resulit.txt", 'a', encoding='utf-8') as f:
               f.write(ip + '\n')
               f.close()
           for i in data:
               with open("resulit.txt", 'a',encoding='utf-8') as f:
                   f.write("\t{} {}\n".format(i['domain'],i['title']))
                   print("     [+] {} {}[+]".format(i['domain'],i['title']))
                   f.close()
   
   def get_ips(ip):
       iplist=[]
       ips_str = ip[:ip.rfind('.')]
       for ips in range(1, 256):
           ipadd=ips_str + '.' + str(ips)
           iplist.append(ipadd)
       return iplist
   
   
   ip=input("请你输入要查询的ip:")
   
   ips=get_ips(ip)
   for p in ips:
       get_c(p)
   
   ```

6. Nmap，masscan扫描等：`nmap -p 80,443,8000,8080 -Pn 192.168.0.0/24`

#### 1.1 常见端口表

```txt
21,22,23,80-90,161,389,443,445,873,1099,1433,1521,1900,2082,2083,2222,2601,2604,3128,3306,3311,3312,3389,4440,4848,5432,5560,5900,5901,5902,6082,6379,7001-7010,7778,8080-8090,8649,8888,9000,9200,10000,11211,27017,28017,50000,50030,50060,135,139,445,53,88
```

注意：探测C段时一定要确认ip是否归属于目标，因为一个C段中的所有ip不一定全部属于目标。

### 2、网络空间搜索引擎

如果想要在短时间内快速收集资产，那么利用网络空间搜索引擎是不错的选择，可以直观地看到旁站，端口，站点标题，IP等信息，点击列举出站点可以直接访问，以此来判断是否为自己需要的站点信息。FOFA的常用语法

1. 同IP旁站：`ip="192.168.0.1"`

2. C段：`ip="192.168.0.0/24"`

3. 子域名：`domain="baidu.com"`

4. 标题/关键字：`title="百度"`

5. 如果需要将结果缩小到某个城市的范围，那么可以拼接语句 `title="百度"&& region="Beijing"`

6. 特征：`body="百度"或header="baidu"`

### 3、扫描敏感目录/文件

扫描敏感目录需要强大的字典，需要平时积累，拥有强大的字典能够更高效地找出网站的管理后台，敏感文件常见的如.git文件泄露，.svn文件泄露，phpinfo泄露等，这一步一半交给各类扫描器就可以了，将目标站点输入到域名中，选择对应字典类型，就可以开始扫描了，十分方便。

扫描工具：

1. 御剑：`https://www.fujieace.com/hacker/tools/yujian.html`

2. 7kbstorm：`https://github.com/7kbstorm/7kbscan-WebPathBrute`

3. bbscan：`https://github.com/lijiejie/BBScan`

   ```bash
   pip install -r requirements.txt
   ```

   1. 扫描单个web服务 www.target.com：`python BBScan.py --host http://www.target.com`

   2. 扫描www.target.com和www.target.com/28下的其他主机：`python BBScan.py --host www.target.com --network 28 `

      1. –network 参数用于设置子网掩码，小公司设为28~30，中等规模公司设置26~28，大公司设为24~26
      2. 当然，尽量避免设为24，扫描过于耗时，除非是想在各SRC多刷几个漏洞。

   3. 扫描txt文件中的所有主机：`python BBScan.py -f wandoujia.com.txt`

   4. 从文件夹中导入所有的主机并扫描：`python BBScan.py -d targets/`

   5. dirmap：`https://github.com/H4ckForJob/dirmap`,`pip install -r requirement.txt`

      1. 单个目标：`python3 dirmap.py -i https://target.com -lcf`
      2. 多个目标：`python3 dirmap.py -iF urls.txt -lcf`

   6. dirsearch：

      1.  `https://gitee.com/Abaomianguan/dirsearch.git`,`unzip `
      2.  `dirsearch.zip`,`python3 dirsearch.py -u `
      3.  `http://m.scabjd.com/ -e * `

   7. gobuster：

      ```bash
      sudo apt-get install gobuster 
      gobuster dir -u https://www.servyou.com.cn/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php -t 50
      
      # dir -u 网址 w字典 -x 指定后缀 -t 线程数量
      dir -u https://www.servyou.com.cn/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x "php,html,rar,zip" -d --wildcard -o servyou.log | grep ^"3402"
      
      ```

   8. dirbuster：kali里

#### 3.1 网站文件

网站的敏感文件，扫描工具可以扫描到这些关键文件，然后进行测试

1. robots.txt：蜘蛛爬虫抓取的协议文件，如淘宝的`https://www.taobao.com/robots.txt`

   ```txt
   User-agent: Baiduspider
   Disallow: /
   
   User-agent: baiduspider
   Disallow: /
   ```   

2. crossdomain.xml：跨域策略文件，可以通过这个里面查询子域名，如`www.taobao.com/crossdomain.xml`

   ```xml
   <cross-domain-policy>
       <allow-access-from domain="*.taobao.com" />
       <allow-access-from domain="*.taobao.net" />
       <allow-access-from domain="*.taobaocdn.com" />
       <allow-access-from domain="*.tbcdn.cn" />
       <allow-access-from domain="*.alicdn.com" />
   </cross-domain-policy>
   
   ```

3. sitemap.xml：XML网站地图协议，可以给搜索引擎用的，如：`http://www.xxwdzd.com/sitemap.xml`

4. 后台目录：就是后台目录了 

5. 网站安装包

6. 网站上传目录：可以上传一些shell

7. mysql管理页面

8. phpinfo：百度搜`inurl:"phpinfo()"`，找到一些内容查看，或者FOFA搜`title="phpinfo()"`,点一个进去`http://188.166.130.202/`可以查看到服务器的版本等各种信息，还可以进行xss注入`http://188.166.130.202/index.php?%3Cscript%3Ealert(1)%3C/script%3E`，可以在下面搜到这个内容

9. 网站文本编辑器：一般会有xss漏洞，和上传漏洞

10. 测试文件：

11. 网站备份文件(.rar、zip、.7z、.tar.gz、.bak)

12. DS_Store 文件

13. vim编辑器备份文件(.swp)

14. WEB—INF/web.xml文件

15. .git

16. .svn

一些常见的敏感文件泄露及渗透方法：`https://www.secpulse.com/archives/55286.html`

#### 3.2 网页备份

1. config.php
2. config.php~
3. config.php.bak
4. config.php.swp
5. config.php.rar
6. conig.php.tar.gz

#### 3.3 网站头信息收集

搜集网址的头信息，比如收集网站的中间件：web服务 ，如apache或者nginx、 iis7 iis7.5 iis8 nginx WebLogic tomcat；还有网站的前端框架之类的信息。

1. 网页F12 - network
2. `http://whatweb.bugscaner.com/look/`：在线指纹cms识别
3. 火狐的插件Wappalyzer
4. 命令行查询：`curl 域名 -i >C:\ip.txt`，查询结果并放到`c/ip.txt`文件中
5. FOFA查询`www.fofa.so`

### 4、网站识别

收集好网站信息之后，应该对网站进行指纹识别，通过识别指纹，确定目标的cms及版本，方便制定下一步的测试计划，可以用公开的poc或自己累积的对应手法等进行正式的渗透测试。

一些工具：

1. 云悉：`http://www.yunsee.cn/info.html`
2. 潮汐指纹：`http://finger.tidesec.net/`
3. CMS指纹识别：`http://whatweb.bugscaner.com/look/`
4. kali工具：`whatweb`，语法：`whatweb 域名`
5. 御剑cms识别：`https://github.com/ldbfpiaoran/cmscan`,`https://github.com/theLSA/cmsIdentification/`

### 5、敏感文件搜索

在网上通过搜索引擎的关键字去搜索一些敏感的信息

#### 6.1 在GitHub上搜索

关键字：

1. `in:name test`：搜索仓库标题含有关键字test的内容
2. `in:descripton test`：在仓库描述里搜索含有关键字test的内容
3. `in:readme test`：在README文件含有关键字的内容
4. `smtp 58.com passsword 3306`：按域名搜索关键字和内容

更多的搜索方式参考资料：

	1. `https://blog.csdn.net/qq_36119192/article/details/99690742`
	2. `http://www.361way.com/github-hack/6284.html`
	3. `https://docs.github.com/cn/github/searching-for-information-on-github/searching-code`

#### 6.2 谷歌搜索

通过`site`关键字 + `域名` + `信息`来搜索。比如：

1. `site:github.com sa password`：搜索sa和password一起的数据
2. `site:github.com svn username password`：搜索暴露出来的svn的用户名和密码的信息
3. `site:github.com password`：搜索含有'密码'的数据
4. `site:github.com 密码`：搜索含有'密码'的数据

#### 6.3 Google-hacking

语法：

1. `site:域名`
2. `inurl:url`：搜索村级的关键字网页
3. `intext`：搜索网页正文中的关键字
4. `filetype`：指定文件类型搜索`site:baidu.com filetype:pdf`

#### 6.4 wooyun漏洞库

可以在这里搜索一些公司泄露的数据

`https://wooyun.website/`

#### 6.5 网盘搜索

1. 凌云搜索：` https://www.lingfengyun.com/`
2. 盘多多：`http://www.panduoduo.net/`
3. 盘搜搜：`http://www.pansoso.com/`
4. 盘搜：`http://www.pansou.com/`

#### 6.6 社工库

社工库就是拥有很多人的信息的库。

社工库可以配合`www.reg007.com`这个网站一起使用。

#### 6.7 JS敏感信息

1. 网站的url连接写到js里面
2. js的api接口 里面包含用户信息 比如 账号和密码

获取js文件内的这些敏感信息可以通过下面的工具

`jsfinder`：下载地址`https://gitee.com/kn1fes/JSFinder`

用法：

```python
# 最基本用法
python3 JSFinder.py -u http://www.mi.com
    
# 深度查询
python3 JSFinder.py -u http://www.mi.com -d
    
# -ou和-os来指定保存url和子域名的文件名
python3 JSFinder.py -u http://www.mi.com -d -ou mi_url.txt -os mi_subdomain.txt

```

`packer-Fuzzer`：一款针对webpack前端打包工具所构造的网站进行快速、高效安全的渗透测试扫描工具。

地址：`https://gitee.com/ckz-code/Packer-Fuzzer?_from=gitee_search`

本工具支持自动模糊提取对应目标站点的API以及API对应的参数内容，并支持对：未授权访问、敏感信息泄露、CORS、SQL注入、水平越权、弱口令、任意文件上传七大漏洞进行模糊高效的快速检测。在扫描结束之后，本工具还支持自动生成扫描报告，您可以选择便于分析的HTML版本以及较为正规的doc、pdf、txt版本。

kali上面node环境安装：

```bash
sudo apt-get install nodejs && sudo apt-get install npm
```

使用：

```bash
# 克隆文件
git clone https://gitee.com/keyboxdzd/Packer-Fuzzer.git
# cd进去之后执行
pip3 install -r requirements.txt
python3 PackerFuzzer.py -u 域名
```

 `SecretFinder`：一款基于Python脚本的JavaScript敏感信息搜索工具

地址 ：`https://gitee.com/mucn/SecretFinder`

使用：

```python
python3 SecretFinder.py -i 域名 -e
# 需要保存的话可以指定-o
python3 SecretFinder.py -i https://targer.com/target.js -o result.html
```

