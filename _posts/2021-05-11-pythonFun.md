---
layout: post
title: python函数
categories: Python
description: python函数
keywords: python 
---

python函数相关操作

### 1、函数定义

```py
def 函数名(参数1,参数2,...):
  """文档描述"""
  函数体
  return 值
```

具体使用:

```py
def test(x, y , z):
  return x + y + z

res = test(1, 2, 3)
print(res)  # 6
```

### 2、参数

可以传形参，也可以传实参，就是带默认值的形参

```py
# x,y,z 就是形参
def test(x, y, z):

# 实参  a就是实参
def test(a=2):
```

不确定参数有几个可以使用`*args`

```py
def test(a, b, c, d, *args):
  print(args)   

test(1,2,3,4,5,6,7,89)  

# (5, 6, 7, 89)
```

前面有具体的形参接受的就都使用对应的变量存储了，没有接受的都被`args`统一管理了。

如果在`最后一个`形参的前面加`**`，在调用函数的时候，溢出的都会被接受

```py
def test(a, **kwargs):
  print(kwargs)

test(1, **{'b': 2, 'c': 3, 'd': 4})
```
