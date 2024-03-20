title: " Python中的注解：Decorator (Annotation)"
tags:
  - annotation
  - decrator
  - python
categories:
    - programming
    - python


---

python's decorator is like Java's annotation, but more flexible and easy to implement because of python's syntax. Both is to use a time way to realize AOP, to insert job of other aspect and make you class/func concentrate to bussiness logic.

``` py

def anna(fn):
 def new_func(*args):
 print 'by anna args=%s' % args
 return fn(*args)
 return new_func

def annie(ar):
 print 'by annie1 ar=%s' % ar
 def _A(fn):
 def new_func(*args):
 print 'by annie2 args=%s' % args
 return fn(*args)
 return new_func
 return _A

class ccc():
 @anna
 def __init__(self):
 print 'ccc'
 @anna
 def ff(self, a):
 print a

&amp;nbsp;

@anna
def test1(a):
 print a

@annie('hi')
def test2(a):
 print a

test1((1,2))
test2((3,4))

```