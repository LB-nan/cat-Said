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

多继承会让代码的可读性变差，python提供了`mixins`机制

#### 3.1 mixins机制

在多继承背景下尽可能的提升多继承的可读性。它没有改变多继承的本质，只是通过一些命名规范、编写规范来提高可读性写代码。

```py
class A(object):
    def test(self):
        print('A')

class TobMixin:
    def onlyB();
        print('b')

class B(A, TobMixin):
    def test(self):
        print('B')


class C(A):
    def test(self):
        print('C')


class D(B,C):
    pass

```

B和C都继承A，但是B有点额外需求，而C不需要，所以不能在A里面加，只能再新建一个新类给B，然而太多父类容易晕，就在命名方面增强可读性。

上面代码里的`TobMixin`类就是一个`mixin`机制的写法，通过给父类改名字，让可读性提高。看到这个类的时候就知道它是归属于谁的父类。可以给B里面再混入一些功能。

`mixins`机制的类通常以`Mixin\able\ible`为后缀结尾、`mixins`机制的类必须职责单一，如果有多个功能就写多个类，反正可以多继承，子类再没有这个mixin类的时候也不影响功能。

#### 3.2 子类调用父类方法

有两种方式

1. 具体写明调用某个类下的某个函数方法
2. 在子类里面调用`super()`来调用父类提供的方法

```py
class People:
  def __init__(self, name):
    self.name = name

class Terch(People):
  def __init__(self, name, age, level):
    self.name = name
    self.age = age
    self.level = level

# Terch类继承People，但是不想额外写name，需要复用父类的的时候，有两种方式

# 1. 直接调用
class Terch(People):
  def __init__(self, name, age, level):
    People.__init__(self, name)
    self.age = age
    self.level = level

# 2. super()
class Terch(People):
  def __init__(self, name, age, level):
    # python2写法
    super(Terch, People).__init__(self, name)
    # python3
    super().__init__(self, name)
    self.age = age
    self.level = level

```

`super()`：调用super()函数会得到一个对象，会参照当前类的MRO去访问当前类的父类里面查找。

### 4、多态

多态指的是一类事物有多种形态，多态性指的是可以在不用考虑对象具体类型的情况下而直接使用对象，这就需要在设计时，把对象的使用方法统一成一种。

Python中一切皆对象，本身就支持多态性，多态性的好处在于增强了程序的灵活性和可扩展性。

```py
class Animal:
  def eat(self):
    print('吃东西')

class People(Animal):
  def eat(self):
    print('人')
    super().eat()

class Pig(Animal):
  def eat(self):
    print('pig')
    super().eat()

pig = Pig()
people = People()

# 定义统一的解控，接收传入的不同对象
def animal_eat(animal):
  animal.eat()


animal_eat(pig)
animal_eat(people)


# --------------------------------------

import abc

# 指定metaclass属性将类设置为抽象类，抽象类本身只是用来约束子类的，不能被实例化
class Animal(metaclass=abc.ABCMeta):
    @abc.abstractmethod # 该装饰器限制子类必须定义有一个名为talk的方法
    def talk(self): # 抽象方法中无需实现具体的功能
        pass

class Cat(Animal): # 但凡继承Animal的子类都必须遵循Animal规定的标准
    def talk(self):
        pass

cat=Cat()
cat.talk()
```

### 4、方法、函数

类中定义的函数分为两大类：绑定方法和非绑定方法(静态方法)，其中绑定方法又分为绑定到对象的对象方法和绑定到类的类方法。

绑定给对象的方法：就是定义后给对象使用的方法

```py
class Student:
  school = 'xxx大学'
  def __init__(self, schoolName):
    self.school = schoolName

  def choose(self):
    print(self.school)

# 获取一个实例对象 stu1
stu1 = Student('北京大学')

# stu1.choose()  调用choose()，choose()就是绑定给对象的方法
print(stu1.choose())  # 北京大学
```

绑定给类的方法：就是给类使用的

```py

class Student:
  school = 'xxx大学'
  def __init__(self, schoolName):
    self.school = schoolName

  def choose(self):
    print(self.school)

  @classmethod
  def stuClassMethod(cls):  # 必须接收一个参数，自动将类Student传给cls
    print('class')

Student.stuClassMethod()  # class
```

静态方法/非绑定方法：为类中某个函数加上装饰器@staticmethod后，该函数就变成了非绑定方法，也称为静态方法。由于没有绑定给任何对象或者类，所以类和对象都可以调用

```py

class Student:
  school = 'xxx大学'

  @staticmethod
  def stumethod():
    print('static methods')


Student.stumethod()

stu1 = Student()
stu1.stumethod()

# static methods
# static methods
```

总结：绑定方法与非绑定方法的使用：若类中需要一个功能，该功能的实现代码中需要引用对象则将其定义成对象方法、需要引用类则将其定义成类方法、无需引用类或对象则将其定义成静态方法。

### 5、对象的属性

可以用`hasattr`来判断是否存在某个属性，用`getattr`获取某个属性，还有`setattr、delattr`

```py
class Teacher:
    def __init__(self, name):
        self.name =name

t=Teacher('liming')

hasattr(t,'name') 

getattr(t,'name',None) # 不存在name属性的话返回默认值None

setattr(t,'age',12) 

delattr(t,'age') 
```

可以通过这些方法来十分灵活的操作对象的属性，这种机制叫作`反射`.

### 6、元类

元类是类的类。

class关键字在帮我们创建类时必然会调用元类`type`，自定义元类的话必须继承自`type`，只有继承了type类才能称之为一个元类

```py
# 自定义元类
class MyTypeClass(type):
    pass

# 继承自定义元类
class MyClass(metaclass=MyTypeClass):
    pass

```

在继承元类的时候，调用了`MyTypeClass`的`__call__`内置方法，然后在`__call__`内部调用了`MyClass`的`__new__`方法然后调用了`MyClass`的`__init__`方法

```py

class MyTypeClass(type):
  def __call__(self, *args, **kwargs):
      # 生成一个对象
      obj = self.__new__(self)

      # 初始化对象
      self.__init__(obj, *args, **kwargs)

      # 可以在这里控制对象的各种属性

      # 返回一个对象
      return obj
```

在调用类创建一个对象的时候会发生三件事

1. 产生一个空对象
2. 调用__init__初始化对象
3. 返回这个对象
