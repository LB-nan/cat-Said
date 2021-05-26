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
