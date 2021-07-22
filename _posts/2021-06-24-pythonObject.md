---
layout: post
title: python面向对象
categories: Python
description: python面向对象
keywords: python面向对象
---

对象是一个容器，将数据和功能进行封装整合。

### 1、class 

```py
# 定义一个类
class Student:
  school = 'xxx大学'
  # 该方法会在对象产生之后自动执行，专门为对象进行初始化操作
  # 第一个参数是python默认的空对象，不需要手动传
  def __init__(self, schoolName):
    self.school = schoolName

  # self：谁调用的方法，指向谁，默认的
  def choose(self):
    print(self.school)
  
  def setSchool(self,name):
    self.school = name

# 获取一个实例
stu1 = Student('北京大学')
# 查看实例的数据
print(stu1.__dict__) # {'school': '北京大学'}

print(stu1.choose())  # 北京大学
print(stu1.setSchool('清华大学'))
print(stu1.choose())  # 清华大学

```

实例化过程

1. 产生一个空对象
2. 自动调用类中的`__init__`方法，将空对象以及参数传给`__init__`
3. 返回初始化好的对象

### 2、封装

面向对象特效：封装、继承、多态。

封装就是把属性和方法搞到一个对象里面，还可以控制属性与方法的隐藏与开放。

属性或者方法的隐藏（变成私有）就是在变量名前加`__`，加了也不是完全的隐藏，可以通过`_类名_属性名`的方式来读取到

```py

class Test:
  __age=18
  def __init__(self, name):
    self.name = name
  def getAge(self):
    return self.__age
  
  def __getName(self):
    return self.name

  def objGetName(self):
    return self.__getName()

obj = Test('测试')
print(obj.name)  # 测试
print(obj.__age) # ttributeError: 'Test' object has no attribute '__age'



# print(obj.name)
# print(obj.__age)
print(obj.objGetName())  # 测试
print(obj.__getName())   # 'Test' object has no attribute '__getName'

print(Test._Test__age)   # 18  可以通过这种语法获取到

```

#### 2.1 @property

python的@property是python的一种装饰器，是用来修饰方法的。可以使用@property装饰器来创建只读属性，@property装饰器会将方法转换为相同名称的只读属性,可以与所定义的属性配合使用，这样可以防止属性被修改。

具体查看`https://zhuanlan.zhihu.com/p/64487092`

### 3、继承

继承是一种创建新类的方式，在Python中，新建的类可以继承一个或多个父类，新建的类可称为子类或派生类，父类又可称为基类或超类。

```py
class Parent1:
  pass

class Parent2:
  pass

class Sub1(Parent1): #单继承
  pass

class Sub2(Parent1,Parent2): #多继承
  pass
```

通过类的内置属性__bases__可以查看类继承的所有父类

```py
Sub2.__bases__
```

多继承需要注意`菱形问题`

- 菱形问题：基类 A B C D, B和C继承至A， D多继承B和C，形成菱形继承问题

```py
class A(object):
    def test(self):
        print('A')


class B(A):
    def test(self):
        print('B')


class C(A):
    def test(self):
        print('C')


class D(B,C):
    pass


obj = D()
obj.test() # B
```

在继承的时候，py会生成一个表，从最后的一个类一直查找到最开始的类，生成一个线性表，然后继承是从左到右查找，找到就完事，所以`D`身上没有，就去找`B`,如果把`class D(B,C):`换成`class D(C,B):`就会去先找`C`，就会打印`C`

这个表称为:`MRO 方法解析顺序(MRO)列表`

查看`MRO`列表

```py
D.mro()
```
