---
layout: post
title: python数据类型
categories: Python
description: python数据类型
keywords: python 
---

详细记录一下python的数据类型

### 1、数字类型 Number

数字类型有四种：

1. 整型 `int`
2. 长整型 `long int`
3. 浮点型 `float`
4. 复数 `complex numbers)`

数字类型可以进行类型转换

```py
# 将x转换为一个整数  
int(x)

# 转换为长整数
long(x)

# 转换为浮点数
float(x)

# 创建一个复数
complex(r, i)

# 转换为整数
ord(x)

# 16进制
hex(x)

# 8进制
oct(x)

# 将一个整数转换为Unicode字符  
unichr(x)

# 将一个整数转换为一个字符  
chr(x)
```

#### 1.1 python中运算

Python 中数学运算常用的函数基本都在 math 模块、cmath 模块中。

- Python math 模块提供了许多对浮点数的数学运算函数。
- Python cmath 模块包含了一些用于复数运算的函数。
- cmath 模块的函数跟 math 模块函数基本一致，区别是 cmath 模块运算的是复数，math 模块运算的是数学运算。

```py
# 使用必须先引入

import math
```

#### 1.2 相关函数

选择一些常用的，具体看文档

1. abs(x)：返回数字的绝对值
2. floor(x)：返回向下取整的数
3. round(x [,n])：返回浮点数x的四舍五入值，如给出n值，则代表舍入到小数点后的位数。
4. pow(x, y)：x**y 运算后的值。
5. random()：随机生成下一个实数，它在[0,1)范围内。
6. choice(seq)：从序列的元素中随机挑选一个元素，比如random.choice(range(10))，从0到9中随机挑选一个整数。
7. 三角函数系列的函数
8. pi：圆周率
9. e：常量e，自然常数

### 2、字符串 String

字符串是很常见的数据类型

一些常用的字符串方法

1. string.count(str, beg=0, end=len(string)): 返回 str 在 string 里面出现的次数，如果 beg 或者 end 指定则返回指定范围内 str 出现的次数
2. string.decode()：以 encoding 指定的编码格式解码 string
3. string.encode()：以 encoding 指定的编码格式编码 string
4. string.find()：检测 str 是否包含在 string 中
5. string.format()：格式化字符串
6. string.index()：跟find()方法一样，只不过如果str不在 string中会报一个异常.
7. string.isspace()：如果 string 中只包含空格，则返回 True，否则返回 False.
8. string.isnumeric()：如果 string 中只包含数字字符，则返回 True，否则返回 False
9. string.isupper()：如果 string 中包含至少一个区分大小写的字符，并且所有这些(区分大小写的)字符都是大写，则返回 True，否则返回 False
10. string.join(seq)：以 string 作为分隔符，将 seq 中所有的元素(的字符串表示)合并为一个新的字符串
11. string.lower()：转换 string 中所有大写字符为小写.
12. string.upper()：转换 string 中的小写字母为大写
13. string.lstrip()：截掉 string 左边的空格
14. string.replace(str1, str2)：把 string 中的 str1 替换成 str2
15. string.rstrip()：删除 string 字符串末尾的空格.
16. string.strip(): 方法用于移除字符串头尾指定的字符（默认为空格）或字符序列。
17. string.split(): 分割字符串
18. string.format(): 字符串格式化
19. string.center(n,'-'): 把字符串放中间，两边用- 补齐

### 3、列表类型 List

列表是Python中最基本的数据结构。序列中的每个元素都分配一个数字 - 它的位置，或索引，第一个索引是0，第二个索引是1，依此类推。

```py
list1 = ['physics', 'chemistry', 1997, 2000]
list2 = [1, 2, 3, 4, 5, 6, 7 [8, 9]]
 
print("list1[0]: ", list1[0])          # ('list1[0]: ', 'physics')

# 取出多个
print("list2[1:5]: ", list2[1:5])      # ('list2[1:5]: ', [2, 3, 4, 5])  

# 后面拿
print(list1[-1])                       # 2000

# 删除
del list1[0]

# 追加
list1.append('222')

# 插入
list1.insert(1, 'insert')

# 删除特定的一项
list1.remove(1997)

# 扩展 将2追加到1后面
list1.extend(list2)

# 统计某一项出现的次数
list2.count(1)

# 排序 默认从小到大  相反的话list2.sort(reverse=Ture)
list2.sort()     

# 翻转
list2.reverse()

# 根据给定的值获取索引
list2.index(2)

# 移除一个值
list2.pop()
```

列表的函数：

1. `cmp(list1, list2)` : 比较两个列表的元素
2. `len(list)` : 列表元素的个数
3. `max(list)` : 列表里的最大值
4. `min(list)` : 列表里的最小值
5. `list(seq)` : 将元祖转换为列表

用列表模拟队列、堆栈

```py

# 队列 FIFO
l.append('first')
l.pop(0)

# LIFO
l.append('secound')
l.pop()

```

### 4、元组 Tuple

Python的元组与列表类似，不同之处在于元组的元素不能修改，元组使用小括号，列表使用方括号，元组创建很简单，只需要在括号中添加元素，并使用逗号隔开即可。

元组不可变是指：元组每一项对应的内存地址不可变，如果存放的是引用类型的数据，比如列表类型或者字典类型，修改这些类型的值不会修改内存地址就可以改变。

```py

# 创建元组
tup1 = (1,2,3,4,5,6)

# 元组中只有一个元素的时候，需要在后面加个’，‘
tup2 = (8,)

# 访问元组中的值
tup1 = ('physics', 'chemistry', 1997, 2000)
tup2 = (1, 2, 3, 4, 5, 6, 7 )
print "tup1[0]: ", tup1[0]
print "tup2[1:5]: ", tup2[1:5]

# 元组不能修改，可以通过两个元组拼接成一个新元组
tup1 = [1,2,3]
tup2 = [4,5,6]
tup3 = tup1 + tup2

# 删除元组，元组不能修改，但是可以直接全部删除
del tup1

# 如果存的是可变类型的值
tup1 = [1, [2, 3, 4], {'a': 1, 'b': 2}]
tup1[1][0] = 'aawdaw'
print(tup1) # [1, ['aawdaw', 3, 4], {'a': 1, 'b': 2}]
```

元组内置函数

1. cmp(tup1, tup2): 比较两个元组元素
2. len(tup)：计算元组长度
3. max(tup)：返回元组中元素最大值
4. min(tup)：返回元组中元素最小值
5. tuple(sqp)： 将列表转换为元组

### 5、字典类型 Dictionary

字典是另一种可变容器模型，且可存储任意类型对象, 类似于js里的对象，字典的每个键值 key=>value 对用冒号 : 分割，每个对之间用逗号(,)分割，整个字典包括在花括号 {} 中。

键必须是唯一的，但值则不必。值可以取任何数据类型，但键必须是不可变的，如字符串，数字。

```py
dic = {
  'a': 10,
  'b': 20, 
  7: 777
}

# 使用方法创建个空字典
d = dict()

# 访问的话使用key访问
# dic['a']
print(dic[7])

# 不可修改key，可以修改value
dic['a'] = '111'

# 可以新增key
dic['c'] = 333

# 删除或删除某一项
del dic['c']
del dic

# 获取长度
len(dic)

# 转换成字符串
str(dic)

# 输出类型
type(dic)

# 转换为字典
dict(x=1, y = 2)
```

字典的内置方法

1. dic.clear(): 清空字典
2. dic.copy()：浅拷贝一份
3. dic.fromkeys()：创建一个新字典，以序列seq中元素做字典的键，val为字典所有键对应的初始值
4. dic.get(key, default=None)：根据key返回对应的val，如果不存在返回默认值
5. dic.items(): 返回一个迭代器，以列表返回可遍历的(键, 值) 元组数组
6. dic.keys()：返回一个迭代器，返回所有的key
7. dic.update(dic2): 将第二个的键值对更新到第一个里面
8. dic.values()：返回一个迭代器，返回所有的val
9. dic.popitem(): 随机返回并删除字典中的最后一对键和值。

### 6、集合类型 Set

集合（set）是一个无序的不重复元素序列。

可以存放多个值，但主要用于：去重、关系运算。

集合的定义：用`{}`内用逗号分隔开多个元素，并且满足一下3个条件

1. 集合内元素必须为不可变类型
2. 集合内元素无序
3. 集合内元素不重复

```py
s = {1,2}  # s = set({1,2})
```

需要注意的是：空的`{}`代表了字典，如果要定义空集合的话，使用`set()`

集合的常用方法

1. add(): 添加元素
2. clear(): 清空集合
3. copy(): 拷贝一个集合
4. discard(): 删除集合中指定的元素，如果不存在不报错
5. remove(): 删除集合中指定的元素，如果不存在则报错
6. update(): 给集合添加元素
7. pop(): 随机移除元素
8. difference(): 返回多个集合的差集
9. intersection(): 返回集合的交集
10. union(): 返回集合的并集
11. issuperset(): 判断该方法的参数集合是否为指定集合的子集
12. issubset(): 判断指定集合是否为该方法参数集合的子集。

#### 6.1 集合的关系运算

```py
# 取交集
dic1 = {'a', 'b', 'c'}
dic2 = {'a', 'b', 'd', 'e', 'f'}
res = dic1 & dic2
print(res) # set(['a', 'b'])

## 使用集合的内置方法实现
print(dic1.intersection(dic2))


# 取并集
dic1 = {'a', 'b', 'c'}
dic2 = {'a', 'b', 'd', 'e', 'f'}
res = dic1 | dic2
print(res) # set(['a', 'c', 'b', 'e', 'd', 'f'])

## 使用集合的内置方法实现
print(dic1.union(dic2))

# 取差集 有前后顺序的问题
res = dic1 - dic2
res2 = dic2 - dic1
print(res) # set(['c'])
print(res2) # set(['e', 'd', 'f'])

## 使用集合的内置方法实现
print(dic1.difference(dic2))

# 对称差集
dic1 = {'a', 'b', 'c'}
dic2 = {'a', 'b', 'd', 'e', 'f'}
res = dic1 ^ dic2
print(res) # set(['c', 'e', 'd', 'f'])

## 使用集合的内置方法实现
print(dic1.symmetric_difference(dic2))

# 父子集：一个集合完全包含另一个集合, 相等的话互为父子 
dic1 = {'a', 'b'}
dic2 = {'a', 'b', 'd', 'e', 'f'}
print(dic1 > dic2) # false
print(dic1 < dic2) # true  dic2是dic1的爹

## 使用集合的内置方法实现
print(dic1.issuperset(dic2))  # >
print(dic1.issubset(dic2))  # <
```

#### 6.2 去重

集合的去重有局限性

1. 只能针对不可变类型去重
2. 无法保证原来的顺序

去重可以使用如下方案

```py
l = [
  {'name': 'c', 'age': 18},
  {'name': 'd', 'age': 18},
  {'name': 'd', 'age': 18},
  {'name': 'e', 'age': 18},
  {'name': 'c', 'age': 18},
]

new_l = []

for i in l:
  if i not in new_l:
    new_l.append(i)

print(new_l)  # [{'age': 18, 'name': 'c'}, {'age': 18, 'name': 'd'}, {'age': 18, 'name': 'e'}]

```

### 7、数据类型总结

把数据类型按不同的方向区分

#### 7.1 按存储个数区分

- 只能存一个值`可称为标量/原子类型`：数字、字符串
- 可以存多个值`容器类型`：列表、元组、字典

#### 7.2 按访问方式区分

- 直接访问`只能通过变量名访问整个值`：数字
- 顺序访问`可以用索引访问，又称为序列类型`：字符串、列表、元组
- key访问`可以使用key访问，又称为映射类型`：字典

#### 7.3 按可变不可变区分

- 可变类型：列表、字典
- 不可变类型：数字、字符串、元组
