---
layout: post
title: python文件处理
categories: Python
description: python文件处理
keywords: python 
---

python关于file文件类型的相关内容

### 1、文件类型 File

文件是操作系统提供给用户/应用程序操作硬盘的一个媒介。用户/应用程序可以通过文件将数据永久保存到硬盘中。

操作文件的过程是在向操作系统发送系统调用，然后再转换成具体的硬盘操作。

python中的file文件类型就是来操控系统文件的。

### 2、基本操作流程

打开文件：`open(url, model)` 函数常用形式是接收两个参数：文件名(file)和模式(mode)。

简单的基本打开文件、读取文件、关闭文件的操作如下：

```py
# 默认的默认就是 ’r‘  mode='rt'  'rt':读模式，文本模式
f = open('./test/a.txt','r')
print(f)

# 完整的打开 读取  关闭     './test/a.txt'=》’abc‘
f = open('./test/a.txt', 'r')
# print(f) 
data = f.read()
f.close()
print(data)
```

打开文件的路径的分隔符问题：`\`可能会被认为是转义符

```py
# 解决方案一
open(r'a\b\c.d.txt')  # r代表了这个是个纯字符串，\就不代表了反义的符号的含义

# 解决方案二
open('/a/b/c.d.txt')
```

### 3、with

当打开了一个文件，操作完没有去关闭的时候就会造成浪费，所以要记着打开必须`f.close()`进行关闭，但是总会有遗忘，python提供了一个语法`with`

`with`语句实质是上下文管理。

```py
# 相当于 f = open('./test/a.txt','r')，但是with会自动关闭文件，释放资源
with open('./test/a.txt', mode='r') as f:
  res = f.read()
  print(res)

# 可以打开多个文件
with open('./test/a.txt', mode='r') as f, open('./test/b.txt', mode='r') as f1:
  res = f.read()
  res1 = f1.read()
  print(res)
  print(res1)
```
