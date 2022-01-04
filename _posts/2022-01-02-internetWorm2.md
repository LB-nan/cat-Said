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

### 4、协程

协程不是进程或线程，其执行过程更类似于子例程，或者说不带返回值的函数调用

协程的几个概念：

- event loop：事件循环，相当于一个无线循环，可以把一些函数注册到这个事件循环上，当满足某些条件时，函数就会被循环执行
- corowtine：协程对象，我们可以将协程对象注册到事件循环中，它会被事件循环调用。可以使用`async`关键字来定义一个方法，这个方法在调用的时候不会立刻被执行，而是返回一个协程对象
- task：任务，是对协程对象的进一步封装，包含了任务的各个状态
- future：代表将来执行或还没有执行的任务，实际上和task没有本质区别
- async：定义一个协程对象
- await：用来挂起阻塞方法的执行

协程demo：

```python
#!/usr/bin/env python3
# -*- coding:utf8 -*-
import asyncio
 
if __name__ == '__main__':
  	# 定义一个协程对象函数
    async def request(url):
        print('正在请求：', url)
        print('请求成功：', url)
		
    # 返回一个协程对象
    c = request('www.baidu.com')

    # 创建一个事件循环对象
    loop = asyncio.get_event_loop()

    # 将协程对象注册到事件循环中,启动c
    loop.run_until_complete(c)

```

task对象的使用：

```python
#!/usr/bin/env python3
# -*- coding:utf8 -*-
import asyncio
 
if __name__ == '__main__':
    async def request(url):
        print('正在请求：', url)
        print('请求成功：', url)

    c = request('www.baidu.com')

    # 创建一个事件循环对象
    loop = asyncio.get_event_loop()

    # 注册一个任务,将协程对象封装进任务里
    task = loop.create_task(c)
    print(task) # 任务执行前的状态
    loop.run_until_complete(task)
    print(task) # 执行后的状态
    
'''
<Task pending name='Task-1' coro=<request() running at 协程.py:6>>
正在请求： www.baidu.com
请求成功： www.baidu.com
<Task finished name='Task-1' coro=<request() done, defined at 协程.py:6> result=None>
'''

```

task和future的使用差不多

future对象的使用：

```python
#!/usr/bin/env python3
# -*- coding:utf8 -*-
import asyncio
 
if __name__ == '__main__':
    async def request(url):
        print('正在请求：', url)
        print('请求成功：', url)

    c = request('www.baidu.com')

    # # 创建一个事件循环对象
    loop = asyncio.get_event_loop()

    # 注册一个功能
    future = asyncio.ensure_future(c)
    loop.run_until_complete(future)

```

还可以添加回调事件来执行：

```python
#!/usr/bin/env python3
# -*- coding:utf8 -*-
import asyncio
 
if __name__ == '__main__':
    async def request(url):
        return url

    def cb(task):
        print(task.result())

    c = request('www.baidu.com')

    # # 创建一个事件循环对象
    loop = asyncio.get_event_loop()
    future = asyncio.ensure_future(c)
    # 绑定回调，需要在协程对象那里有一个返回值，然后在回调函数里面通过`task.result()`来处理
    future.add_done_callback(cb)
    loop.run_until_complete(future)

```

在异步协程中如果出现了同步模块相关的代码，就无法实现异步。

代码如下：

```python
#!/usr/bin/env python3
# -*- coding:utf8 -*-
import asyncio
import time
 
if __name__ == '__main__':
    async def request(url):
        print('任务开始：',url)
        time.sleep(2)
        print('任务结束：',url)

    urls = [
        'www.url1.com',
        'www.url2.com',
        'www.url3.com'
    ]
    tasks = []
    for url in urls:
        c = request(url)
        task = asyncio.ensure_future(c)
        tasks.append(task)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(asyncio.wait(tasks))

'''
任务开始： www.url1.com
任务结束： www.url1.com
任务开始： www.url2.com
任务结束： www.url2.com
任务开始： www.url3.com
任务结束： www.url3.com
'''
```

如上代码，在`time.sleep(2)`的地方被阻塞了，在asyncio中遇到阻塞操作必须进行手动挂起，可以替换成如下代码：

```python
async def request(url):
  print('任务开始：',url)
  await asyncio.sleep(2)
  print('任务结束：',url)

'''
任务开始： www.url2.com
任务开始： www.url3.com
任务结束： www.url1.com
任务结束： www.url2.com
任务结束： www.url3.com
'''
```

### 5、多任务异步协程

开启一个服务器：

```python
import time
import flask
from flask import Flask
app = Flask(__name__) 

@app.route('/a')
def indexa():
    time.sleep(2)
    return 'a'

@app.route('/b')
def indexb():
    time.sleep(2)
    return 'b'

@app.route('/c')
def indexc():
    time.sleep(2)
    return 'c'

if __name__ == '__main__':
     app.run(host='0.0.0.0',port=80,threaded=True)

```

运行，然后访问`http://192.168.3.17/a`返回了`a`

多任务协程代码

```python
# -*- coding:utf8 -*-
import requests
import asyncio
import time
 
if __name__ == '__main__':

    urls = [
        'http://192.168.3.17/a',
        'http://192.168.3.17/b',
        'http://192.168.3.17/c'
    ]

    async def get_page(url):
      	print('开始下载：', url)
        page_text = requests.get(url = url).text
        print('下载完毕：', url, '=》', page_text)

    tasks = []

    for url in urls:
        c = get_page(url)
        task = asyncio.ensure_future(c)
        tasks.append(task)
    
    loop = asyncio.get_event_loop()
    loop.run_until_complete(asyncio.wait(tasks))

'''
开始下载： http://192.168.3.17/a
下载完毕： http://192.168.3.17/a =》 a
开始下载： http://192.168.3.17/b
下载完毕： http://192.168.3.17/b =》 b
开始下载： http://192.168.3.17/c
下载完毕： http://192.168.3.17/c =》 c
'''
```

发现是串行执行的，是因为`requests.get()`是基于同步进行请求的，我们需要使用基于异步的网络请求模块进行请求

#### 5.1 aiohttp

异步网络请求模块

安装

```bash
pip install aiohttp
pip3 install aiohttp
```

引用

```python
import aiohttp
```

修改上面的bug

```python
    async def get_page(url):
        print('开始下载：', url)
        async with aiohttp.ClientSession() as session:
            # 因为session发送http请求期间是会阻塞的 所以要使用await进行手动挂起
            async with await session.get(url) as res:
                page_text = await res.text()
                print('下载完毕：', url, '=》', page_text)
                
'''
# 执行结果
开始下载： http://192.168.3.17/a
开始下载： http://192.168.3.17/b
开始下载： http://192.168.3.17/c
下载完毕： http://192.168.3.17/a =》 a
下载完毕： http://192.168.3.17/b =》 b
下载完毕： http://192.168.3.17/c =》 c
'''
```

使用异步模块之后就变成了异步了，这样就完成了多任务异步协程了。
