---
layout: post
title: python爬虫
categories: Python
description: python爬虫
keywords: python爬虫
---

用python写爬虫，爬虫写的好...

### 1、爬虫

爬虫就是编写程序模拟浏览器上网，让其去互联网抓取数据的过程

爬虫分类：

1. 通用爬虫：抓取系统重要组成部分，抓取的是一整张页面数据。
2. 聚焦爬虫：是建立在通用爬虫的基础上，抓取页面中特定的局部内容。
3. 增量式爬虫：检测网站中数据更新的情况，只抓取网站中最新出来的数据。

爬虫的矛与盾：

- 反爬机制：网站可以通过制定相应的策略或者技术手段来防止爬虫程序进行网站数据的爬取
- 反反爬策略：爬虫程序可以通过制定相关的策略或者技术手段，破解网站中具备的反爬机制，从而爬取数据。

#### 1.1 robots.txt协议

君子协议。规定了网站中哪些数据可以被爬虫爬，哪些不可以。

如知乎的：`https://www.zhihu.com/robots.txt`

```txt
# 部分内容
User-agent: Baiduspider
Disallow: /appview/
Disallow: /login
Disallow: /logout
Disallow: /resetpassword
Disallow: /terms
Disallow: /search
Allow: /search-special
Disallow: /notifications
Disallow: /settings
Disallow: /inbox
Disallow: /admin_inbox
Disallow: /*?guide*
```

### 2、HTTP/HTTPS

HTTP协议：就是服务器和客户端进行数据交互的一种形式。

常用请求头信息：

1. User-Agent：请求载体的身份标识
2. Connection：请求完毕后，是断开连接还是保持连接

常用响应头信息：

1. Content-Type：服务器响应回客户端的数据类型

HTTPS协议：安全的超文本传输协议，就是多了加密。

加密方式：

1. 对称秘钥加密
2. 非对称秘钥加密
3. 证书秘钥加密

具体内容可以看另外的博客

1. `https://catsaid.cn/2021/03/02/http/`
2. `https://catsaid.cn/2021/04/16/https/`

### 3、requests模块

requests模块是Python中原生的一款基于网络请求的模块，功能非常强大，简单便捷，效果极高。

该模块的最重要作用就是模拟浏览器发请求。

使用模块流程：

1. 指定URL
2. 发起请求
3. 获取响应数据
3. 数据解析
4. 持久化存储

安装：

```py
$ curl https://bootstrap.pypa.io/get-pip.py | python3
$ pip3 install requests
```

使用：

```python
#!/usr/bin/env python3
# -*- coding:utf8 -*-
import requests

if __name__ == '__main__':
    url = 'https://www.catsaid.cn/'
    res = requests.get(url = url)
    page_text = res.text
    print(page_text)
    with open('./catsaid.html', 'w', encoding='utf-8') as f:
        f.write(page_text)
    print('打印结束')
    
```

### 4、UA检测/伪装

UA：User-Agent（请求载体的身份标识）

UA检测：门户网站的服务器会检测对应请求的载体身体标识，如果检测到请求的载体身份标识是某一款浏览器，说明该请求是一个正常的请求；如果没检测到就说明该请求不正常(爬虫)，拒绝该次请求，不予响应。所以就需要UA伪装。

UA伪装：让爬虫对应的请求载体身份标识伪装成某一款浏览器。

假设有如下爬虫代码

```python
#!/usr/bin/env python3
# -*- coding:utf8 -*-
import requests

if __name__ == "__main__":
    url = "https://www.sogou.com/web"
    kw = input('enter:')
    params = {
        "query": kw
    }
    res = requests.get(url=url, params=params)
    page = res.text
    with open(kw+'.html', 'w', encoding="utf-8") as f:
        f.write(page)
    print('ok!')

```

进行测试的时候，搜狗会返回如下内容：

```html
<div class="content-box">
    <p class="p2">用户您好，我们的系统检测到您网络中存在异常访问请求。<br>此验证码用于确认这些请求是您的正常行为而不是自动程序发出的，需要您协助验证。</p>
</div>
```

进行UA伪装：

```python
headers={
  "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110 Safari/537.36"
}
res = requests.get(url=url, params=params, headers=headers)
page = res.text
with open(kw+'.html', 'w', encoding="utf-8") as f:
  f.write(page)

```

### 5、POST/GET请求爬取

POST爬取百度翻译输入的接口，获取翻译的值

```python
#!/usr/bin/env python3
# -*- coding:utf8 -*-
import requests
import json

if __name__ == '__main__':
    url = 'https://fanyi.baidu.com/sug'
    text = input('请输入要翻译的单词：')
    data = {
        "kw": text
    }
    # UA伪装
    headers = {
        "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110 Safari/537.36"
    }
    res = requests.post(url=url, data=data, headers=headers)
    res_json = res.json()
    # 持久化存储
    fp = open(text+'.txt', 'w', encoding='utf-8')
    json.dump(res_json, fp=fp, ensure_ascii=False)

    print('持久化存储完成，翻译结果是：',res_json['data'][0]['v'])

```

结果：

```bash
请输入要翻译的单词：cat
持久化存储完成，翻译结果是： n. 猫; 猫科动物; 狠毒的女人; 爵士乐爱好者 vt. 把（锚）吊放在锚架上; 〈俚〉寻欢，宿娼
```

GET爬取豆瓣电影喜剧排行

```python
#!/usr/bin/env python3
# -*- coding:utf8 -*-

import requests
import json

if __name__ == '__main__':
    url = 'https://movie.douban.com/j/chart/top_list'
    params = {
        'type': '24',
        'interval_id': '100:90',
        'action': '',
        'start': "0",
        'limit': "20"
    }

    headers = {
        "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110 Safari/537.36"
    }

    res = requests.get(url=url, params=params, headers=headers)
    res_json = res.json()
    print(res_json)
    fp = open('douban.json', 'w', encoding='utf-8')
    json.dump(res_json, fp=fp, ensure_ascii=False)

```

多页面数据组合爬取，爬取药监局化妆品行业的企业信息

```python
#!/usr/bin/env python3
# -*- coding:utf8 -*-
import requests

if __name__ == '__main__':

    url = 'http://scxk.nmpa.gov.cn:81/xk/'
    url1 = 'http://scxk.nmpa.gov.cn:81/xk/itownet/portalAction.do?method=getXkzsList'
    url2 = 'http://scxk.nmpa.gov.cn:81/xk/itownet/portalAction.do?method=getXkzsById'
    headers = {
        "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110 Safari/537.36"
    }
    data = {
        'on': 'true',
        'page': 1,
        'pageSize': 15,
        'productName': '',
        'conditionType': 1,
        'applyname': '',
        'applysn': ''
    }
    res = requests.post(url=url1,data=data, headers=headers).json()
    arr = res['list']
    idarr = []
    for i in arr:
        idarr.append(i['ID'])

    details = []
    for id in idarr:
        data2 = {
            'id': id
        }

        detail = requests.post(url = url2, data=data2, headers=headers).json()
        details.append(detail)

    with open('hzp.txt', 'w', encoding='utf-8') as f:
        for d in details:
            f.write(str(d))
    print(details)

    print('over')

```

### 6、数据解析

Python中解析数据的方法：

1. 正则
2. bs4
3. xpath

数据解析原理：

1. 解析的局部的文本内容都会在标签之间或者标签对应的属性中进行存储
2. 进行指定的标签定位
3. 标签或者标签对应的属性中存储的数据值进行提取。

较为通用的中文乱码处理问题

```python
name = name.encode('iso-8859-1').decode('gbk')
```

#### 6.1 bs4

bs4数据解析的原理：

1. 实例化一个beautifulSoup对象，并且将页面源码数据加载到对象中
2. 通过调用beautifulSoup对象中相关的属性或者方法进行标签定位和数据提取

使用前的环境安装：

```bash
pip install bs4
pip install lxml
```

使用：

```python
from bs4 import BeautifulSoup
# 对象的实例化有两种方法
# 1. 将本地的html文档中的数据加载到该对象中
# 2. 将互联网上获取的页面源码加载到该对象中
if __name__ == '__main__':
  # 将本地的html文件的数据加载到该对象中
  fp = open('./test.html', 'r', encoding='utf-8')
  # 互联网上获取数据
  # page_text = requests.get(url).text
  # soup = BeautifulSoup(page_text, 'lxml')
  # 实例化的时候需要同时传入数据
  soup = BeautifulSoup(fp, 'lxml')
```

用于数据解析的方法和属性：

1. soup.tagName：返回数据中指定的标签的第一组内容，如`soup.a`返回第一组a标签及内容
2. soup.find(tagName)：相当于`soup.tagName`
   1. 根据class属性值定位tag：`soup.find('div', class_='c1')`
   2. 根据id：`soup.find('div', id='id1')`
   3. 根据attribute：`soup.find('div', attrs='a1')`
3. soup.find_all()：返回的是一个列表：`find_all(name, attrs, recursive, text, limit, **kwargs)`
4. soup.select()：使用css选择器进行定位，可以使用class或者id，返回的是一个列表：`soup.select('.c1')`
5. 获取标签之间的文本数据：
   1. soup.a.text：可以获取标签下的所有子节点的文本节点内容
   2. soup.a.string：只可以获取标签下的文本节点内容，不可以获取子节点的文本节点内容
   3. soup.a.get_text()：可以获取标签下的所有子节点的文本节点内容
6. 获取标签之间的属性值：`soup.a['href']`

#### 6.2 xpath

最常用且最便捷高效的一种解析方式。

xpath解析原理：

1. 实例化一个etree对象，且需要将解析的页面源码加载到该对象中
2. 调用etree对象中的xpath方法结合xpath表达式实现标签的定位和内容的捕获

环境安装：

```bash
pip install lxml
```

实例化：

```python
from lxml import etree
# 本地HTML文档
etree.parse(filepath)

# 互联网获取的源码
page_text = requests.get(url).text
etree.HTML(page_text)
```

xpath demo：

```python
#!/usr/bin/env python3
# -*- coding:utf8 -*-
from lxml import etree
 
if __name__ == '__main__':
		# 报错：lxml.etree.XMLSyntaxError 可以加下面的代码解决
    parser = etree.HTMLParser(encoding='utf-8')
    tree = etree.parse('hzp.html',parser=parser)
    # tree = etree.parse('hzp.html')
    print(tree)
    # xpath()：接受css选择器进行匹配数据，返回一个列表
    # /html/head/div     '/'的意思是'>'  可以写成 /html//div  //div
    r = tree.xpath('/html/head/titie')
    print(r)

```

可以根据属性进行精确选择

```python
r = tree.xpath('//div[@class="c1"]')
```

可以进行索引定位，获取class为'cn'的div下的第3个p标签

```python
r = tree.xpath('//div[@class="cn"]/p[3]') # 这里的索引是从1开始
```

获取标签的文本值

```python
r = tree.xpath('//div[@class="cn"]/p[3]/a/text()') # 返回的是一个列表
t = r[0]
```

获取标签的属性的值，获取img的src属性

```python
r = tree.xpath('//div[@class='c2']/img/@src')[0]
```

爬取58同城上面北京昌平二手房价格demo

```python
#!/usr/bin/env python3
# -*- coding:utf8 -*-
import requests
from lxml import etree

if __name__ == '__main__':

    parser = etree.HTMLParser(encoding='utf-8')
    url = 'https://bj.58.com/changping/ershoufang/'
    headers = {
        "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110 Safari/537.36"
    }
    page_text = requests.get(url=url, headers=headers).text
    tree = etree.HTML(page_text,parser=parser)
    # 拿到每一项的div父级
    divList = tree.xpath('//section[@class="list"]/div[@class="property"]')
    for div in divList:
        title = div.xpath('.//div[@class="property-content"]//h3/text()')[0]
        priceInfo = div.xpath('.//div[@class="property-content"]//p[@class="property-price-total"]/span/text()')
        obj = {
            "名称": title,
            "价格": priceInfo[0]+ priceInfo[1]
        }
        with open('58昌平二手房.txt', 'a', encoding='utf-8') as f:
            f.write(str(obj) + '\n')
    print('爬取完毕')
```

### 7、验证码

验证码是门户网站的反爬手段之一，需要登录才可以访问网站内容

验证码识别方式：

1. 人工识别
2. 第三方自动识别

### 8、模拟登录

登录之后才可以爬取信息

模拟登录流程：

1. 查看登录接口需要携带的请求参数及验证码识别
2. post发送请求，录入需要的登录信息
3. 拿到身份标识，进行其他爬取请求

创建会话对象，session会话对象的作用：

1. 可以进行请求的发送
2. 如果请求过程中产生了cookie，则该cookie会自动存储、携带在该session对象中

使用流程：

1. 创建session对象`session = requests.Session()`
2. 使用session对象进行模拟登陆post请求的发送，cookie就会被存储在session中
3. 爬取需要的数据

### 9、代理

对一些网站爬取次数太多，会被封ip，代理就是用来破解封IP反爬机制的。

代理：代理服务器，代理网络用户去获取网络信息。

作用：

1. 可以突破自身ip访问的限制
2. 可以隐藏自己真实的ip

