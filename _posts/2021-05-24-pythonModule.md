---
layout: post
title: python模块
categories: Python
description: python模块
keywords: python模块
---

python中的模块

### 1、简介

在Python中，一个py文件就是一个模块。

### 2、模块引入

```py
# 有一个文件叫：module.py
def a():
  print('module')


# 使用
import module
module.a()  # module
```

在引入模块的时候python解释器会执行三个步骤

1. 执行源文件代码
2. 创建一个命名空间存放引入的文件里面的变量名
3. 在当前文件执行的名称空间中创建一个变量名，指向新创建的模块名称。

重复导入已经有的模块并不会重复执行，通过`import sys`，打印`sys.modules`可以看到都引入了哪些模块。

可以一次性引入多个

```py
import modulea, moduleb, modulec

# 或者
import modulea
import moduleb
import modulec
```

在全局导入模块就属于全局作用域，在函数体内导入模块就属于局部作用域。

不想引入前缀的话可以只用`from 模块 import 变量名`的方式

```py
# 这里就可以直接使用a这个函数名了，不需要加前面的了
from module import a
a()  # module


# 还可以一次性引入多个
from module import a, b, c, d, e

# 可以把模块内的名字全部引入，这种方式只支持在全局作用域引入，并且这种方法不太好
from module import *

# 用*号引入，我们可以在模块内定义可以导入的变量名，不在定义范围内的不给*引入
__all__=['a','b']
# 这样就只能引入 a,b
```

导入的时候如果变量名冲突，可以给模块起个`别名`

```py
# as 后面的是别名
import module as mymodule
mymodule.a()

# 可以给某个方法单独起别名
from module import a as mya
```

在导入模块的时候，如果该模块已经在内存中，就直接使用，如果不在，就会优先查找内置模块，然后去检索`sys.path`中定义的路径，找不到抛出异常，`sys.path`是一个列表类型，它里面可能会有`.zip/.egg`格式的压缩文件，python支持从压缩文件中导入模块，压缩文件相当于一个目录。

我们为了保证一个模块可以被正确找到，可以把文件的路径push到`sys.path`里面

```py
import sys
sys.path.append(r'url')  #也可以使用sys.path.insert()
```

一个python文件在被当做模块导入的时候，内置的`__name__`变量会赋值为模块名，如果作为脚本执行的话会赋值为`__main__`，我们可以通过判断这个去决定python脚本执行什么方法

```py
if __name__ == '__main__':
  foo.py被当做脚本执行时运行的代码
else:
  foo.py被当做模块导入时运行的代码
```

在编写模块的时候，需要加合适的注释，以及对环境的判断

```py
#!/usr/bin/env python #通常只在类unix环境有效,作用是可以使用脚本名来执行，而无需直接调用解释器。

# 模块的描述


# 模块的注释 等等

# 各种类和函数、变量的注释
```

### 3、包

包就是把一些模块放到一起，包的本质就是模块，包需要含有一个`__init__.py`的文件的文件夹，文件夹内可以组织子模块和子包。

```py
package/           # 顶级包
  - __init__.py    # 文件
  - modules/        # 子包
    - moduleA.py
    - moduleB.py
  - module.py      # 子模块
```

在python3中，可以没有`__init__.py`文件，import包不会报错，python2中不行。

在引入包的时候也会发生三个步骤，类似于引入模块的过程，但是由于包是一个文件夹，所以需要`__init__.py`来当模块的代码去运行

1. 执行包下面的`__init__.py`文件
2. 申请一个名称空间存放`__init__.py`执行后产生的变量名
3. 然后将申请的变量名指向生成的变量名

另外，导入包的时候并不会导入包下所有的子模块和子包

```py
#根目录创建文件加 modules   创建 __init__.py
from modules.modulea import funa
from modules.moduleb import funb
from modules.modulesc import func
from modules.mod.moduled import fund

# 或者
from .mod.moduled import fund  # 相对

# 安装这个目录创建文件，里面分别定义一个函数 funa / funb / func / fund

#根目录创建 1.py 
import modules as myM
myM.funa()
myM.funb()
myM.func()
myM.fund()

# 如果报错找不到的话 需要设置一下路径

import sys

sys.path.append(r'url')  # url是模块的路径


# --------------------------------
# 或者这样引入也行
from modules import funa

funa()

```

包内部的引用推荐使用相对导入，绝对导入是没有限制的，绝对导入更通用一点。

### 4、常用模块

python中常用的模块及用法

#### 4.1 time

时间模块

```py
# 获取时间戳
print(time.time())
# 获取本地时区时间，可以获得更详细的内容
print(time.localtime())  #time.struct_time(tm_year=2021, tm_mon=6, tm_mday=3, tm_hour=15, tm_min=12, tm_sec=38, tm_wday=3, tm_yday=154, tm_isdst=0)

# 获取 年月日 时分秒
print(time.strftime('%Y-%m-%d %H:%M:%S'))
print(time.strftime('%Y-%m-%d %X'))

# 等待5秒
time.sleep(5)

```

#### 4.2 datetime

datetime模块提供了处理日期和时间的类，既有简单的方式，又有复杂的方式。它虽然支持日期和时间算法，但其实现的重点是为输出格式化和操作提供高效的属性提取功能。

参考链接：`https://blog.csdn.net/gty931008/article/details/80254806`

常用的：

1. datetime.date: 表示日期
2. datetime.time: 表示时间
3. datetime.datetime: 表示日期时间

```py
print(datetime.datetime.now())  #获取年月日时分秒

print(datetime.datetime.now() + datetime.timedelta(days=3))  # 获取三天后的时间
```

时间戳和格式化的时间可以相互转换，但是需要通过结构化时间来做转换，时间戳和格式化时间不能直接转。

```py
import time

# 结构化时间转为时间戳
timer = time.localtime()  # 结构化时间
print(time.mktime(timer))  # 转成时间戳


# 时间戳转换为结构化时间
time.localtime(time.time())

# 结构化时间转为格式化时间
print(time.strftime('%Y-%m-%d %H:%M:%S'))

# 格式化时间转为结构化时间
print(time.strptime('2020-02-02 22:22:22', '%Y-%m-%d %H:%M:%S'))

#把时间戳直接转化为结构化时间
import datetime
print(datetime.datetime.fromtimestamp(time.time()))

```

一些常用方法：

```py
datetime.date.today() # 本地日期对象,(用str函数可得到它的字面表示(2014-03-24))
datetime.date.isoformat(obj) # 当前[年-月-日]字符串表示(2014-03-24)
datetime.date.fromtimestamp() # 返回一个日期对象，参数是时间戳,返回 [年-月-日]
datetime.date.weekday(obj) # 返回一个日期对象的星期数,周一是0
datetime.date.isoweekday(obj) # 返回一个日期对象的星期数,周一是1
datetime.date.isocalendar(obj) # 把日期对象返回一个带有年月日的元组


# datetime对象：
datetime.datetime.today() # 返回一个包含本地时间(含微秒数)的datetime对象 2014-03-24 23:31:50.419000
datetime.datetime.now([tz]) # 返回指定时区的datetime对象 2014-03-24 23:31:50.419000
datetime.datetime.utcnow() # 返回一个零时区的datetime对象
datetime.fromtimestamp(timestamp[,tz]) # 按时间戳返回一个datetime对象，可指定时区,可用于strftime转换为日期表示 
datetime.utcfromtimestamp(timestamp) # 按时间戳返回一个UTC-datetime对象
datetime.datetime.strptime(‘2014-03-16 12:21:21‘,”%Y-%m-%d %H:%M:%S”) # 将字符串转为datetime对象
datetime.datetime.strftime(datetime.datetime.now(), ‘%Y%m%d %H%M%S‘) # 将datetime对象转换为str表示形式
datetime.date.today().timetuple() # 转换为时间戳datetime元组对象，可用于转换时间戳
datetime.datetime.now().timetuple()
time.mktime(timetupleobj) # 将datetime元组对象转为时间戳
time.time() # 当前时间戳
time.localtime
time.gmtime
```

#### 4.3 random

随机模块

```py
import random

# 随机出大于0小于1的任意小数
print(random.random()) 

# 随机出一个1~3的任意整数，包括1和3   1 - 2 - 3
print(random.randint(1, 3))  

# 随机出大于等于1且小于3的任意整数， 1 - 2
print(random.randrange(1, 3))  

# 在参数列表内随机选择一个数
print(random.choice([1, 2, 3, '4'])) 

# 接受两个参数，第一个是一个列表，里面有一堆数据，第二个参数是指定选择几个数
# 在[1, 2, 3, '4']里面随机选择2个数出来
print(random.sample([1, 2, 3, '4'], 2))  

# 随机选大于1小于3的小数
print(random.uniform(1, 3)) 

# 打乱列表内数据的位置
arr = [1,2,3,4,5,6,7,8]
random.shuffle(arr)
print(arr)


# 随机验证码
res = ''
for i in range(6):
  # chr()：根据ASCII码的位置来取对应的字符   ord()：根据传入的字母获取ASCII码的位置
  s1 = chr(random.randint(65, 90))
  s2 = str(random.randint(0, 9))
  res += random.choice([s1, s2])
print(res)
```

#### 4.4 OS模块

os(opreate system)模块提供了非常丰富的方法用来处理文件和目录,是python标准库中的一个用于访问操作系统功能的模块，使用os模块中提供的接口可以实现跨平台访问。

主要用于：

1. 获取平台信息
2. 对目录的操作
3. 判断操作

常用API：

```py
import os

##### 系统操作

# 获取系统路径的分隔符  Windows： \\  Linux： /  macOS: /
print(os.sep)
# 获取使用的工作平台  Windows：nt   linux/unix：posix
print(os.name)
# 获取环境变量  
print(os.getenv('path'))
# 获取当前的路径  绝对路径
print(os.getcwd())
# 执行操作系统命令
os.system() 
# 全局环境变量
os.environ 


#### 目录操作  更多参考： https://www.runoob.com/python/os-file-methods.html

# 返回制定目录下的所有文件和目录名
os.listdir(dirname)
# 创建一个目录
os.mkdir(dirname)
# 删除一个目录，需要是空目录
os.rmdir(dirname)
# 跳转到制定的目录中
os.chdir()
# 可生成多层递归目录
os.makedirs('dirname1/dirname2') 
# 若目录为空，则删除，并递归到上一级目录，如若也为空，则删除，依此类推   
os.removedirs('dirname')    
# 重命名文件/目录
os.rename("oldname","newname")  
# 获取当前目录的父目录字符串名
os.pardir 


###### os.path的操作


# 返回文件名
os.path.basename(path)

# 返回文件路径
os.path.dirname(path)

# 获取文件大小 单位是字节，如果name是目录返回OL
os.path.getsize(name)

# 获取绝对路径
os.path.abspath(name)

#  拼接目录与文件名或目录, 将多个路径组合后返回，第一个绝对路径之前的参数将被忽略
os.path.join(path, name)

#  将path分割成目录和文件名二元组返
os.path.split(path)

#  返回path所指向的文件或者目录的最后存取时间
os.path.getatime(path)

#  返回path所指向的文件或者目录的最后修改时间
os.path.getmtime(path)

# 如果path存在，返回True；如果path不存在，返回False
os.path.exists(path)

#  如果path是绝对路径，返回True
os.path.isabs(path)

#  如果path是一个存在的文件，返回True。否则返回False
os.path.isfile(path)

#  如果path是一个存在的目录，则返回True。否则返回False
os.path.isdir(path)
```

#### 4.5 sys模块

该模块提供对解释器使用或维护的一些变量的访问，以及与解释器强烈交互的函数。它始终可用。

```py
# 命令行参数List，第一个元素是程序本身路径, 后续的是运行的时候接受的参数
sys.argv 

# 返回模块的搜索路径，初始化时使用PYTHONPATH环境变量的值 
sys.path 
```
