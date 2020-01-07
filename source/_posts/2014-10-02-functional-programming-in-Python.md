title: Python函数式编程
date: 2014-10-02 22:12:31
tags:
    - python
    - functional programming
    - recursive
    - clojure
categories:
    - programming
    - python
    - functional
---

![](http://res.uperform.cn//differential%20functions.png)

## 函数式编程
如果程序中的函数仅接受输入并产生输出，即输出只依赖于输入，内部数据不可变，避免保存程序状态，用同样的输入值反复调用可以得到相同的结果，那么这种编程范式就称为[函数式编程(Functional Programming，简称FP，又称泛函编程)](http://en.wikipedia.org/wiki/Functional_programming#First-class_and_higher-order_functions)。

这种风格也称声明式编程(Declarative Programming)，与之相对的是指令式编程(Imperative Programming)，后者中的对象会不断修改自身状态。函数式编程强调程序的执行结果比执行过程更重要，倡导利用若干简单的执行单元让计算结果不断渐进，逐层推导复杂的运算，而不是设计一个复杂的执行过程。

函数编程语言最重要的基础是λ演算（lambda calculus），函数可以像数值一样被赋值于变量，还可以作为其他函数的输入（引数）和输出（传出值）进行传递。

函数式编程历史悠久，最古老的例子莫过于1958年被创造出来的LISP了。而随着程序结构复杂，面向对象编程大行其道。近年来，简洁而且特别适合计算任务的函数式编程又重新崛起，不仅仅是纯粹的函数式语言如Haskell、Clojure、Elixir等，各种流行语言javascripts、python、Objective-C、C#、Swift甚至Java都纷纷吸收函数式编程的部分形式。而且，不仅仅是计算任务，近年还出现了用FP编写的UI应用程序，如LightTable等。

Paul Graham在《黑客与画家》一书中写道：同样功能的程序，极端情况下，Lisp代码的长度可能是C代码的二十分之一。

本文作者@申导 主要采用Python语言为例，是因为它虽然不是纯粹的FP，但Python能够胜任各种编程形式，简洁优雅，通俗易懂，语法接近于Java/C++，特别适合从主流语言转过来的学习者。

<!--more-->



## 纯函数(Pure functions)

纯函数指的是没有依赖于外部内存或I/O，函数间无共享变量（与面向对象范式有所不同），即不带有副作用(side-effect)的函数。每次调用函数都会返回新值，而不会修改原来的变量，这也称为不变性(Immutable)，想象以下在Java中将每一个入参变量都声明为final的情景。
这样的函数没有隐含期望，也便于进行黑盒测试。

来看个非函数式的例子，它改变了变量的值。

``` c
int cnt;
void inc() {
    cnt++;
}
```

函数式的例子，不改变变量的值，而是返回一个新值。

``` c
int cnt;
int inc() {
    return cnt+1;
}
```

这个特点可以用来优化代码。例如，一个无副作用的纯函数，其执行结果具有不变性，那么其执行结果就可以缓存起来，供下次调用。再比如，两个互不依赖的纯函数，其执行顺序可以互换，甚至并行地执行而无需互斥。

在python中，不可变的元组(tuple)数据结构特别适合函数式编程。


## 高阶函数(Higher-order functions)

在FP中，首要特点就是将函数视为一等公民，函数可以当做参数来进行传递，形成所谓的高阶函数，形如 `z=g(f(x),y)`，还能像变量一样被创建和修改。

读者如果使用过C语言，一定记得标准库中的快排函数，其中第4个参数是一个函数指针，用于传入一个比较(compare)函数，而排序动作被抽象成了一个模板函数。这就是一个典型的高阶函数：`qsort(compare(items))`

``` c
void qsort(void *items, size_t nitems, size_t size, int (*compare)(const void *, const void*));
```

如果你写过前端网页，那么对以下javascript代码肯定不陌生:

``` js
setTimeout(function() {
    alert("Another tick");
}, 1000);
```

这种形式在非纯粹的函数式编程语言里面多有吸收，用于简化语法。连最古板的面向对象语言Java也终于在Java8中引入了lambda。


### lambda(匿名λ函数)

使用lambda可以定义简单的单行匿名函数。lambda的语法如下：`lambda args: expression`

``` py
lambda_add = lambda x, y: x + y

def normal_add(x,y):
    return x+y
    
assert lambda_add(2,3) == normal_add(2,3)
```

匿名λ函数与使用def定义的函数完全一样，可以使用lambda_add作为函数名进行调用。然而，提供lambda的目的是为了编写偶尔为之的、简单的、可预见不会被修改的匿名函数。


### reduce函数

考虑一个求和的例子，一般会采用循环：
``` py
def my_sum(numbers):
    total = 0
    for x in numbers:
        total = total + x
    return total

my_sum(range(1, 100))
```

如果再求乘积呢？
``` py
def my_product(numbers):
    total = 1
    for x in numbers:
        total = total * x
    return total

my_product(range(1, 100))
```

想到DRY原则，上述两段函数存在了不少重复。我们看到除了初始值和运算符不同，其实整体的流程是差不多的，那么归纳(reduce)一下如何？

``` py
def my_reduce(numbers, function, initial):
    total = initial
    for x in numbers:
        total = function(total, x)
    return total
    
my_reduce(range(1, 100), lambda t,x: t+x, 0)
my_reduce(range(1, 100), lambda t,x: t*x, 1)
```

Python内置的`reduce(function, iterable[, initializer]) `函数已经实现了对列表元素依次归纳的场景，而内置的`all()`,`any()`,`sum()`,`max()`,`min()`等函数都是基于它衍生而来。


### map、zip、filter、sorted函数

Python内置函数还有`map(function, iterable, ...)`了，它抽象了另一种情景，即遍历列表中的每个元素，对每个元素执行传入的函数，并返回包含所有新元素的新列表。

而`zip(iterable1, iterable2, ...) `函数则对多个列表进行合并，每个列表的第n个元素组成一个元组(tuple)，然后返回包含这些元组的新列表

`filter(function, iterable)`函数的功能是遍历列表，如果以元素作为参数调用function时返回True的话则将其过滤出来，最后返回包含所有过滤出的元素的新列表。

`sorted(iterable, ...)`顾名思义，这就是排序函数。排序天生就是一个函数行为，完全没必要放到什么类中。


### 简化代码

有了这些内置函数，你的代码会变得更简洁，没有了循环体，数据集，操作，返回值都放到了一起。特别是用了`reduce()`以后，连`for`,`while`循环都省了。例如，下列函数可以替代for语句来达到循环5次调用f(x)的目的。

``` py
def f(a):
    print a

print reduce(lambda _, x: f(x), range(5), 0)
```

再看个例子，我们有3辆车比赛，简单起见，我们分别给这3辆车有70%的概率可以往前走一步，一共有5次机会，我们打出每一次这3辆车的前行状态。用指令式编程的代码如下：
``` py
from random import random
 
time = 5
car_positions = [1, 1, 1]
 
while time:
    # decrease time
    time -= 1
 
    print ''
    for i in range(len(car_positions)):
        # move car
        if random() > 0.3:
            car_positions[i] += 1
 
        # draw car
        print '-' * car_positions[i]
```

如果改用函数式或称指令式编程，则是这样的：
``` py
from random import random
L = [0]*3
reduce(lambda ll,_: map(lambda x:(x+1) if random() > 0.3 else x, ll), range(5), L)
```

再看看用FP模拟Unix下的`echo`命令：
``` py
def monadic_print(x):
    print x
    return x
 
echo_FP = lambda: monadic_print(raw_input("FP -- "))=='quit' or echo_FP()
echo_FP()
```


### 偏函数(Partial function)与柯里化(Currying)

柯里化(Currying)技术是把接受多个参数的函数变换成只接受部分参数（比如原函数的第一个参数）的函数，并且返回接受余下的参数的新函数，新函数称为偏函数。
形如：`f(x,y) ==> f(x)(y)`，可用于批量生成相似的函数。思考一下：平方函数与立方函数的抽象关系是如何的？


Python不像Scala语言那样支持Currying。然而稍作变通即可达到生成偏函数的效果：

``` py
def add(x, y):
    return x + y

def add_to(n):
    return lambda x: add(n, x)

assert add(3, 2) == add_to(3)(2)
```

但Python内置的functools模块提供了一个函数partial，可以为任意函数生成偏函数：
`functools.partial(func[, *args][, **keywords])`

``` py
import functools
f3 = functools.partial(add, 3)
assert add(3, 2) == f3(2)

```
---

## 闭包(Closure)
如果一个函数定义在另一个函数的作用域内，并且引用了外层函数的变量，则该函数称为闭包。下例中`inner()`就是一个闭包，本身是一个函数，而且可以访问(在python2.x中是只读的)本身之外的变量`n`。

``` py
def f():
    n = 1
    def inner():
        print n
    return inner
    
f()()
```


### 函数装饰器(Decorator)


Python的Decorator(函数装饰器)在功能上类似Java的函数注解(Annotation)。它首先是个闭包，存放了fn及自定义的变量。然后再返回一个wrapper函数，真正对fn及fn的参数进行AOP处理。如果要使用带参数的decorator还需要多包裹一层，先返回一个保存着decorator参数的闭包，再返回一个保存了fn的闭包。


看一个通俗的计算函数运行时间的例子，其中对于运行时间的统计与原功能做到了分离：
``` py
import time
 
def timeit(func):
    def wrapper():
        start = time.clock()
        func()
        end =time.clock()
        print 'used:', end - start
    return wrapper

@timeit
def foo():
    print 'in foo()'
 
foo()
```



再看一个计算斐波那契数列的例子，每次递归都会有重复计算，如果能讲中间结果记录下来就可以提高性能。(增加了可选的 @warps 是为了避免一些副作用，比如func.__name__等属性保持为原函数的名字而非wrapper，防止采用反射时遇到问题。)

``` py
from functools import wraps
def memo(fn):
    cache = {}
    miss = object()
 
    @wraps(fn)
    def wrapper(*args, **kwargs):
        result = cache.get(args, miss)
        if result is miss:
            result = fn(*args)
            cache[args] = result
        return result
 
    return wrapper
 
@memo
def fib(n):
    if n < 2:
        return n
    return fib(n - 1) + fib(n - 2)
```


实质上，`@decorator`写法其实是高阶函数的语法糖，每一次装饰都生成了一个新的函数。下例中的两种写法是等价的：
``` py
@decorator_one
@decorator_two
def fn():
    pass
    
func = decorator_one(decorator_two(fn))    
```

``` py
@decorator_one(arg1, arg2)
@decorator_two
def fn(param1):
    pass

func = decorator_one(arg1, arg2) (decorator_two (fn) )
```

---

## 递归(Recursion)

在FP中，通常通过递归来实现循环。递归函数会不断调用自身，直到到达最基本的条件。

看个用线性递归代替循环来求和的例子(从1...5循环)：
``` py
def lsum(f, a, b):
    if (a>b):
        return 0
    else:
        return f(a)+lsum(f, a+1, b)

print lsum(lambda x:x, 1, 5)
```

### 尾递归(Tail Recursion)

由于每次线性递归(Linear Recursive)调用都需要维护一个栈(stack)，来保存临时状态，因此大量递归会带来性能问题，但是利用尾递归(Tail Recursive)可以进行优化，每次递归通过传参的方式来传递状态，减少stack占用。如果编译器支持的话，还可以将递归形式展开优化为while循环的形式(目前Python编译器暂不支持该优化)。下例中变量`acc`在每次递归后都会将最新状态带入下一次递归。
``` py
def tsum(f, a, b):
    def loop(a, acc):
        if a>b:
            return acc
        else:
            return loop(a+1, acc+f(a))
    return loop(a,0)

print tsum(lambda x:x, 1, 5)
```


---

## 严格与非严格求值、惰性求值

FP语言可以分为严格（及早）求值与非严格（惰性）求值(Strict vs. Non-strict evaluation)，区别在于对表达式求值的时机。看下面这个例子：

``` py
print len([2+1, 3*2, 1/0, 5-4])
```
在Python中执行上述语句会报错，因为以0为除数是非法的。可以看出Python对于数值运算是严格求值的，而像Haskell的默认方式就是非严格求值，因而上述语句的执行结果就是4，即列表的长度。

而Python中也存在惰性求值的语法，比如相对于`range(n)`函数，`xrange(n)`是其惰性版本。
再如相对于列表生成器`[x+1 for x in range(5)]`，惰性版本可以写成`(x+1 for x in range(5))`。你可以print一下，看看两者的区别。

支持惰性求值的编译器会像数学家看待代数表达式那样看待函数式程序：抵消相同项从而避免执行无谓的代码，安排代码执行顺序从而实现更高的执行效率甚至是减少错误。

惰性求值技术允许定义无穷数据结构，因为只有真正会被用到的数才会被计算出来。

## 迭代器 (Iterator)

只要实现了`__next__()`函数的类，都可成为迭代器，每次调用`next()`函数，就应当返回序列中的下一个值。内置的数据结构如tuple、list、dict、set等都已经实现了迭代器。

对于列表，for循环通常是以遍历迭代器的形式，比如要从1~5循环，可以写成：
``` py
for i in [1,2,3,4,5]:
    pass
```

对于列表，如果还想获得循环的索引，可以这样写：
``` py
for i, index in enumerate([1,2,3,4,5]):
    pass
```

对于字典，可以这样遍历：
``` py
for k, v in {'a':1, 'b':2}.items():
    pass
```

内置的itertools库提供了更有效更丰富的迭代器，包括去重、笛卡尔积、无限迭代、条件迭代。同时还有各种内置函数的惰性版本，比如相对于`map()`的`imap()`等。

开源库Fn.py库也实现了无限序列等。


### 列表生成器的解析

列表生成器可用来快速生成列表，可以代替map()或filter()的使用。(注意例子中用的是严格求值的方式，否则还需要一次遍历才能展开列表)

比如下例中的写法是等价的：
``` py
[x+1 for x in range(5)]
map(lambda x:x+1, range(5))

[x for x in range(10) if x%2==0]
filter(lambda x:x%2==0, range(10))
```

如果是多重循环解析，则可以写成：(注意例子中可以用惰性求值的方式)
``` py
((x, y) for x in range(3) for y in range(x))
```

如果是组合循环解析，则可以写成：(注意例子中可以用惰性求值的方式)
``` py
(x for x in (y.doSomething() for y in lst) if x>0)
```


### 生成器(Generator)


生成器是一个特殊的迭代器，需要用到`yield`关键字。包含该关键字的函数会自动成为一个生成器对象。里面的代码一般是一个有限或无限循环结构，每当调用该函数时，会执行到yield代码为止并返回本次迭代结果。然后冻结(freeze)在这一行，直到外部调用者的下一次调用该函数时，再返回下一次迭代结果。通过这种方式，迭代器可以实现惰性求值。

看一个用生成器来计算斐波那契数列的例子。其中求值函数是一个无限循环的生成器，而外部调用该生成器时，需要显式地控制迭代次数。

``` py
def fibonacci():
    a = b = 1
    yield a
    yield b
    while True:
        a, b = b, a+b
        yield b

for num in fibonacci(): 
    if num > 100: break
    print num,
```

另一例是牛顿法开平方根，而每次迭代都会更加逼近真实值。下例是生成器与尾递归两种写法的比较，其中生成器内是一个有条件循环。(”_”是合法变量名，用作变量占位符，最后一行的reduce相当于for循环的作用)

``` py
def square(k):
    guess=1
    yield guess
    while abs(guess*guess-k)>0.001:
        guess=(guess+k/guess)/2.0
        yield guess
    return
for z in square(2):
    print z


def improve(guess, k):
    return (guess+k/guess)/2.0
print reduce(lambda guess, _: improve(guess, 2), range(4), 1)
```

再看一个中序(inorder)遍历二叉树的例子，里面用到了生成器的递归嵌套：
``` py
class Tree:
    def __init__(self,left, label, right) :
        self.left = left;
        self.label = label;
        self.right = right;

def inorder(t):
    if t is not None:
        for x in inorder(t.left): yield x
        yield t.label
        for x in inorder(t.right): yield x

def make_tree(array):
    if len(array) == 1:
        return Tree(None, array[0], None)
    return Tree(make_tree(array[0]), array[1], make(array[2]))

tree = make_tree([[['a'], 'b', ['c']], 'd', [['e'], 'f', ['g']]])
print [n for n in inorder(tree)]
```

### 管道式表达式

函数嵌套调用，看起来没有那么清爽。如果能将数据看成流，函数之间像shell里面的管道一样来传递数据，结果会更清楚一些。下段代码示例揭示了其中的原理，通过在装饰器中重载`__ror__`运算符(从右向左进行"或"操作)并且返回一个迭代器来做到这一点，于是，函数也就可以通过`|`运算符来互相操作了。

``` py
class Pipe(object):
    def __init__(self, func):
        self.func = func
 
    def __ror__(self, other):
        def generator():
            for obj in other:
                if obj is not None:
                    yield self.func(obj)
        return generator()
 
@Pipe
def even_filter(num):
    return num if num % 2 == 0 else None
 
@Pipe
def multiply_by_three(num):
    return num*3
 
@Pipe
def convert_to_string(num):
    return 'The Number: %s' % num
 
@Pipe
def echo(item):
    print item
    return item
 
def force(sqs):
    for item in sqs: pass
 
nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
force(nums | even_filter | multiply_by_three | convert_to_string | echo)
```

这正是开源库[pipe.py](http://pypi.python.org/pypi/pipe)所实现的管道式调用/流式操作。这种方式也比较适合参数校验、判空等场景。pipe.py的用法更加简洁一些：

``` py
from pipe import * 

range(5) | add

fibonacci() | where(lambda x: x % 2 == 0) | take_while(lambda x: x < 10000) | add

@Pipe
def take_while_idx(iterable, predicate): 
    for idx, x in enumerate(iterable):
        if predicate(idx): yield x
        else: return

fibonacci() | take_while_idx(lambda x: x < 10) | as_list
```

用pipe和itertools重新实现一下之前的赛车题目：
``` py
from random import random
from pipe import *
import itertools

print itertools.count(1) | take(5) | aggregate(lambda ll,_: ll | select(lambda x:(x+1) if random() > 0.3 else x), initializer=[0]*3) | as_list

```

## 参考：

<http://en.wikipedia.org/wiki/Functional_programming#Pure_functions>
<http://inst.eecs.berkeley.edu/~cs61a/book/chapters/functions.html>
<http://www.cnblogs.com/huxi/archive/2011/07/15/2107536.html>
<http://baike.baidu.com/view/1439396.htm>
<http://www.infoq.com/cn/articles/fn.py-functional-programming-python>
<http://coolshell.cn/articles/10822.html>
<http://coolshell.cn/articles/11265.html>
<http://www.oschina.net/translate/python-functional-programming-part1>
