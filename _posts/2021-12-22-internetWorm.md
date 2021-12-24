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



