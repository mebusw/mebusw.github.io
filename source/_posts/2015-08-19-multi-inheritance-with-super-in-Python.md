title: Python中多继承与super()用法
date: 2015-08-19 10:58:01
tags:
    - python
categories:
    - programming
    - python
---
Python类分为两种，一种叫经典类，一种叫新式类。两种都支持多继承。

考虑一种情形，B继承于A，C继承于A和B, 但C需要调用父类的__init__()函数时，前者会导致父类A的__init__()函数被调用2次，这是不希望看到的。而且子类要显式地指定父类，不符合DRY原则。

```python
# 经典类
class A():
    def __init__(self):
        print 'A'

class B(A):
    def __init__(self):
        A.__init__(self)
        print 'B'

class C(B, A):
    def __init__(self):
        A.__init__(self)
        B.__init__(self)
        print 'C'

```

采用新式类，要求最顶层的父类一定要继承于`object`，这样就可以利用super()函数来调用父类的__init__()等函数，每个父类都执行且执行一次，并不会出现重复调用的情况。而且在子类的实现中，不用到处写出所有的父类名字，符合DRY原则。

```python
# 新式类
class A(object):
    def __init__(self):
        print 'A'

class B(A):
    def __init__(self):
        super(B, self).__init__()
        print 'B'

class C(B, A):
    def __init__(self):
        super(C, self).__init__()
        print 'C'

```

采用super()方式时，会自动找到第一个多继承中的第一个父类，但是如果还想强制调用其他父类的__init__()函数或两个父类的同名函数时，就要用老办法了。

```python

class Person(object):
    name = ""
    sex = ""
    def __init__(self, name, sex='U'):
        print 'Person'
        self.name=name
        self.sex=sex


class Consumer(object):
    def __init__(self):
        print 'Consumer'
    
class Student(Person, Consumer):
    def __init__(self, score,name):
        print Student.__bases__
        super(Student, self).__init__(name, sex='F')
        Consumer.__init__(self)
        self.score=score

s1 = Student(90, 'abc')
print s1.name, s1.score, s1.sex


```