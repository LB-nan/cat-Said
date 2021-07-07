---
layout: post
title: python面向对象
categories: Python
description: python面向对象
keywords: python面向对象
---

对象是一个容器，将数据和功能进行整合。

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
