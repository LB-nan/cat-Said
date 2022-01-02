---
layout: post
title: 异步爬虫
categories: Python
description: python爬虫
keywords: python爬虫,异步爬虫,爬虫,异步
---

异步爬虫

### 1、简介

在爬虫中使用异步实现高性能的数据爬取操作。

在不使用异步的时候，同时爬取多个url的数据，前面的请求会阻塞后面的请求

```python
#!/usr/bin/env python3
# -*- coding:utf8 -*-
import requests
import time
 
if __name__ == '__main__':
    start_time = time.time()
    headers = {
        "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110 Safari/537.36"
    }

    urls = [
        'https://bj.58.com/changping/ershoufang/',
        'https://bj.58.com/chaoyang/ershoufang/',
        'https://bj.58.com/haidian/ershoufang/'
    ]

    def get_content(url):
        print('正在爬取',url)
        page_text = requests.get(url=url, headers=headers).text
        return page_text

    def parse(content):
        print('内容长度为：', len(content))

    for url in urls:
        content = get_content(url)
        parse(content)
    end_time = time.time()

'''
正在爬取 https://bj.58.com/changping/ershoufang/
内容长度为： 1363782
正在爬取 https://bj.58.com/chaoyang/ershoufang/
内容长度为： 1549302
正在爬取 https://bj.58.com/haidian/ershoufang/
内容长度为： 1407832
3.3931241035461426
'''
```

第一个执行完毕之后才会执行第二个，需要改成同时执行就需要使用到异步，也就是多线程or多进程

### 2、多线程&多进程

好处：可以异步进行操作

弊端：无法无限制的开启多线程或进程

### 3、进程池、线程池

好处：降低系统对进程或者线程创建和销毁的一个频率，从而降低系统开销

弊端：池中的进程或者线程的数量是有上限的

线程池的简单使用：

```python
import requests
from multiprocessing.dummy import Pool
import time
 
if __name__ == '__main__':
    start_time = time.time()
    headers = {
        "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110 Safari/537.36"
    }

    urls = [
        'https://bj.58.com/changping/ershoufang/',
        'https://bj.58.com/chaoyang/ershoufang/',
        'https://bj.58.com/haidian/ershoufang/'
    ]

    def get_content(url):
        print('正在爬取',url)
        page_text = requests.get(url=url, headers=headers).text
        return page_text

    def parse(content):
        print('内容长度为：', len(content))
    
    pool = Pool(3)
    contents = pool.map(get_content, urls)
    # 线程池的返回值
    print(len(contents))
    end_time = time.time()
    print(end_time - start_time)
    
"""
正在爬取 https://bj.58.com/changping/ershoufang/
正在爬取 https://bj.58.com/chaoyang/ershoufang/
正在爬取 https://bj.58.com/haidian/ershoufang/
3
1.229123830795288
"""
```

对比上面的串行的内容，快了2s多
