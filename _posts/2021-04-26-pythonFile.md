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
f = open('./test/a.txt','rt')
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

指定字符编码，默认的是`unicode`编码，如果乱码就需要指定字符编码

在文件读取的时候如果没有指定编码，会使用操作系统默认的编码来解码

1. Linux和Mac默认的是`utf-8`
2. Windows默认的是`GBK`

```py
with open('./test/c.txt', mode='rt') as f:
  res = f.read()
  print(res)

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

### 4、文件读写的模式

文件读写的模式分为两种：

1. 控制文件读写内容的模式
2. 控制文件读写操作的模式

控制文件读写内容的模式分为两种：`t`和`b`。这两种必须搭配读写操作的模式使用`r/w/a`

`t`表示文本模式

1. 读写都是以字符串(unicode)为单位
2. 只能针对文本文件
3. 必须指定字符编码

`b`表示二进制模式 `binary`

1. 读写文件都是以bytes/二进制为单位的
2. 可以针对所有文件
3. 不能指定字符编码

```py
# 读
with open('./test/a.txt', mode='rb') as f:
  res = f.read()
  print(res)

# 写
# 写入的时候需要指定字符编码
with open('./test/a.txt', mode='wb') as f:
  f.write('你好'.encode('utf-8'))

```

控制文件读写操作的模式分为三种：

1. `r`：只读模式
2. `w`：只写模式
3. `a`：只追加模式
4. 其他还有个`x`模式，只写模式，不可读，不存在则创建，存在则报错

另外还可以有个额外的`+`，三种模式配合`+`之后都可以读写操作

1. `r+`：可读可写，可以用`seek()`来控制文件指针，读取特定位置的内容，或者在特定位置写入内容
2. `w+`：可读可写，由于`w`模式每次打开会清空文件内容，所以使用它打开文件只会得到一个空的文件
3. `a+`：可读可写，由于`a`模式只会在末尾追加，所以`a+`也只能再末尾追加，不能控制文件指针移动

### 5、文件操作模式详解

以`t`模式为基础进行操作

#### 5.1 'rt'模式

`r`模式是默认的模式，为只读模式。

当读取的文件不存在时，会报错，当文件存在时，文件指针跳到开始位置

```py

with open('c.txt', mode='rt' ) as f:
  # read()：不传参的话默认从文件的开始位置一次性读到结尾，读完整个文件到内存里，当文件过大的时候可能会有占满内存的问题
  # read()：接受一个参数，为从文件中读取的字节数，默认为-1，整个文件
  res = f.read()
  print(res)


# 文件有多行
# 这是文件内容
# 写个测试内容2222
# 写个测试内容2223
# 写个测试内容2224
# 写个测试内容2225
# 写个测试内容2226

with open('./test/c.txt') as f:
  for line in f:
    print line

# 输出内容
# 写个测试内容2222

# 写个测试内容2223

# 写个测试内容2224

# 写个测试内容2225

# 写个测试内容2226

# 因为print()方法会默认添加个换行，可以通过`print(line, end='')`来去掉

```

#### 5.2 'wt'模式

`w`模式是只写模式，当文件不存在的时候会创建空文件，当文件存在的时候会清空文件，指针位于开始位置

```py
# 写完操作后，光标在内容的最后
with open('./test/a.txt', mode='wt') as f:
  f.write('写个锤锤')

```

`w`模式打开的瞬间清空文件，在打开且没有关闭的期间持续写内容，会连续写入，每次写入之后指针移动到当前文件的末尾。

```py
# 写完操作后，光标在内容的最后
with open('./test/a.txt', mode='wt') as f:
  f.write('写个锤锤\n')
  f.write('写个锤儿')

# 文件变成
#写个锤锤
#写个锤儿
```

#### 5.3 'at'模式

`a`模式是追加写模式，在文件不存在时会创建空文档,文件存在会将文件指针直接移动到文件末尾，然后再写入要追加写入的内容

```py
with open('./test/a.txt', mode='at') as f:
  f.write('追个锤锤\n')
  f.write('追个锤儿')
```

`a`模式与`w`模式的异同：

- 相同点：在文件不存在的时候都会创建，在文件打开的情况下都会持续写入
- 不同点：`a`模式在文件打开的时候不会清空，指针在文件末尾，新写的内容在文件的最后

#### 5.4 注册功能代码

注册，然后把用户名密码写入到文件中

```py
#coding=utf-8

# 存放已有的用户信息做校验
userInfo = {}

with open('./test/user.txt', 'r') as f:
  for line in f:
    name,pws = line.strip().split(':')
    userInfo[name] = pws

# 注册功能
while True:

  username = input('请输入用户名：')

  if username in userInfo:
    print('用户已存在，请重新输入')
    continue
 
  password = input('请输入密码：')
  re_password = input('请确认密码：')

  if password == re_password:
    print('注册成功')
    with open('./test/user.txt', 'a', encoding='utf-8') as f:
      f.write(f'{username}:{password}\n')
    break
  else:
    print('注册失败，两次密码不一致')
    continue
 
```

### 6、操作文件的方法

读操作：

1. read()：读取所有内容,执行完该操作后，文件指针会移动到文件末尾
2. readline()：读取一行内容,光标移动到第二行首部
3. readlines()：读取每一行内容,存放于列表中

- f.read()与f.readlines()都是将内容一次性读入内容，如果内容过大会导致内存溢出，若还想将内容全读入内存，则必须分多次读入，有两种实现方式：

```py
# 一
with open('某个大文件',mode='rt',encoding='utf-8') as f:
  # 按行读取，但是一行可能也很多，这个时候就需要第二种方式
  for line in f:
    print(line) 

# 二
with open('某个大文件',mode='rb') as f:
  while True:
    # f.read()可以设置字节数，按字节数读取，就不会爆了
    data=f.read(1024)
    if len(data) == 0:
      break
    print(data)
```

写操作：

1. f.write(): 普通写操作
2. f.writelines(): 可以写入一个列表
3. flush()：刷新，写入内容之后再调用可以刷新这个文件，写入的时候系统会等够一定数量的字节然后执行一次写入硬盘操作，执行这个的时候会立即写入一次硬盘

```py

# 普通的write
f.write('111111')

# writelines
ts = ['111', '222', '333', '444']
f.writelines(ts)

```

其他操作：

1. f.readable()：文件是否可读
2. f.writable()：文件是否可写
3. f.closed：文件是否关闭
4. f.encoding：如果文件打开模式为b,则没有该属性
5. f.name：获取文件的名字

### 7、文件指针的移动

文件指针的移动需要用到`f.seek()`方法，文件内指针的移动都是Bytes为单位的,唯一例外的是t模式下的read(n),n以字符为单位

f.seek(指针移动的字节数,模式控制)

模式有三种：

1. `0`：默认的模式,从文件的开头开始
2. `1`：以当前光标或指针所在位置开始
3. `2`：以文件尾部开始的

其中0模式可以在t或者b模式使用,而1跟2模式只能在b模式下用

```py
# 0模式的使用
with open('a.txt',mode='rt',encoding='utf-8') as f:
  # 从文件开头移动了3个字节
  f.seek(3,0)     
  # 从当前位置往后移动了3个字节
  f.seek(3,1)
  # 直接跳到文件末尾，第一个参数可以为负数，从后往回返
  f.seek(0,2)
```

### 8、文件修改

文件修改是修改内存中的数据，然后更新硬盘中的数据，用新内容覆盖旧内容。

```py
with open('./test/a.txt',mode='r+t',encoding='utf-8') as f:
    f.seek(2)
    f.write('aabbc')
```

还可以提前把文件都读取到内存中，然后再修改

```py
with open('./test/a.txt',mode='rt',encoding='utf-8') as f:
    data=f.read()
with open('./test/a.txt',mode='wt',encoding='utf-8') as f:
    f.write(data.replace('bc','cd'))
```

上面方法如果在文件数据很大的情况下会过多占用内存，我们可以创建一个临时文件用来操作，然后替换掉源文件

```py
# a.txt 文件的内容

# 你好,aaabbb

# 替换后的内容： 你好,ccabbb


import os

with open('./test/a.txt', mode='rt', encoding='utf-8') as read_f,\
        open('./test/a.txt.swap', mode='wt', encoding='utf-8') as wrife_f:
    for line in read_f:
        wrife_f.write(line.replace('aa', 'cc'))

os.remove('./test/a.txt')
os.rename('./test/a.txt.swap', './test/a.txt')
```
