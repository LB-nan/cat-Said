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

### 3、命名空间

`命名空间/名称空间`就是存放变量名与对象映射/绑定关系的地方，在程序执行期间最多会存在三种名称空间

1. 内置名称空间
2. 全局名称空间
3. 局部名称空间

加载顺序是: 1 > 2 > 3

查找顺序是：3 > 2 > 1

### 4、作用域

不同的名称空间有不同的作用域。

1. 全局作用域：内置名称空间和全局名称空间都在全局内存活
2. 局部作用域：局部名称空间在当前局部的作用域里临时存活

当python执行的时候，先执行当前作用域，查找当前的局部作用域的变量，然后再去全局查找

### 5、函数闭包

基于函数对象的概念，可以将函数返回到任意位置去调用，但作用域的关系是在定义完函数时就已经被确定了的，与函数的调用位置无关。也就是说函数被当做数据处理时，始终以自带的作用域为准。若内嵌函数包含对外部函数作用域（而非全局作用域）中变量的引用，那么该’内嵌函数’就是闭包函数，简称闭包(Closures)

```py
x=1
def outer():
    x=2
    def inner():
        print(x)
    return inner

func=outer()
func() # 结果为2
```

“闭”代表函数是内部的，“包”代表函数外’包裹’着对外层作用域的引用。因而无论在何处调用闭包函数，使用的仍然是包裹在其外层的变量。

### 6、装饰器

装饰器指的是为装饰对象添加新的功能，装饰器与被装饰的对象均可以是任意可调用对象。装饰器的作用就是在不修改被装饰对象源代码和调用方式的前提下为被装饰对象添加额外的功能。

可调用对象有函数，方法或者类。

```py
def outter(func):
  def wrapper(*args, **kwargs):
    res = func(*args, **kwargs)
    # 进行一些其他操作 。。。。。然后把返回值返回回去
    return res
  return wrapper

def home(name):
  print(name)

# 装饰home，把wrapper返回重新赋值给home，在wrapper里面调用home原函数
home = outter(home)

```

可以使用语法糖

```py
# 在被装饰对象的上面单独一行写  @outter  就可以了   需要定义在上面

# 原函数会有各种属性，也需要给wrapper添加上
from functools import wraps
def outter(func):
  @wraps(func)  # 给wrapper装饰，给wrapper添加原函数的各种属性
  def wrapper(*args, **kwargs):
    res = func(*args, **kwargs)
    # 进行一些其他操作 。。。。。然后把返回值返回回去
    return res
  return wrapper

@outter   # 相当于home = outter(home)
def home(name):
  print(name)

```

可以多次装饰，装饰从下到上执行

```py

@deco1
@deco2
@deco3
def index():
  pass

# 执行顺序是  3 => 2 => 1 
```

### 7、生成器

若函数体包含`yield`关键字，再调用函数，并不会执行函数体代码，得到的返回值即生成器对象，生成器内置有`__iter__`和`__next__`方法，所以生成器本身就是一个迭代器。

有了`yield`关键字，我们就有了一种自定义迭代器的实现方式。`yield`可以用于返回值，但不同于`return`，函数一旦遇到`return`就结束了，而yield可以保存函数的运行状态挂起函数，用来返回多次值

```py
def eater():
  while True:
    food = yield
    print(food)


g = eater()     # 调用带有生成器关键字的函数，得到生成器对象
next(g)         # 初始化函数，让函数执行一次，在yield的地方卡住，等待调用
g.send(1)   # 调用 g.send() 执行一次
g.send(2)

"""
包子1
2
"""
```

针对表达式形式的yield，生成器对象必须事先被初始化一次，让函数挂起在food=yield的位置，等待调用g.send()方法为函数体传值，g.send(None)等同于next(g)。

可以使用装饰器来完成为所有表达式形式yield对应生成器的初始化操作

```py
def deco(func):
    def wrapper(*args,**kwargs):
        g=func(*args,**kwargs)
        next(g)
        return g
    return wrapper

@deco
def eater():
  while True:
    food = yield
    print(food)

```

### 7.1 迭代器

迭代器是一个可以记住遍历的位置的对象。迭代器对象从集合的第一个元素开始访问，直到所有的元素被访问完结束。迭代器只能往前不会后退。迭代器有两个基本的方法：iter() 和 next()。

有`__iter__`属性的类型都可以生成迭代器对象，调用`__iter__()`会返回一个迭代器对象，调用`__next__()`可以进行遍历

```py
l = [1,2,3,4,5]

l = l.__iter__()

print(l.__next__())
print(l.__next__())
print(l.__next__())
print(l.__next__())
print(l.__next__())

```
