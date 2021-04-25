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

### 5、
