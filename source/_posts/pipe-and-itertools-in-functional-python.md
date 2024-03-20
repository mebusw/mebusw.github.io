title: 函数式Python中的Pipe与itertools
date: 2015-08-31 21:25:26
tags:
    - python
    - functional programming
    - pipe
    - itertools
categories:
    - programming
    - python
    - functional
---

可迭代器(iterable)，不仅限于list/str等，还包括任何包含有`yield`关键字的函数，后者未必有规律的迭代特征。标准库中的itertools包提供了更加灵活的产生迭代器的工具，这些工具的输入大都是已有的迭代器函数的封装，并且itertools给出的函数都是针对广义迭代器而言。而`len()`等函数是针对狭义迭代器，即sequence(i.e. str, list, tuple)而言的。
以内置函数`range()`为例，执行结果会是一次性计算好整个序列。这对于很长的序列来说会比较耗时，甚至带来性能问题。因而，python还提供了内置函数，提供了惰性求值版本，那就是`xrange()`。它利用`yield`特性，第一次执行时仅仅返回迭代器，不到用时是不会求值的。
实际上，itertools提供的函数都是惰性的，并且给原内置函数都重写了惰性版本。如`imap()`对于内置的`map()`。


扩展库[Pipe](https://github.com/JulienPalard/Pipe)则对内置函数和部分itertools进行了封装，提供了类似unix bash下的管道式调用风格，更接近人类从左到右的阅读习惯，使得代码更加优雅。其他动态语言，如ruby, c#-lambda java8-lambda也都提供了类似的链式调用形式。
另外，也提供了@Pipe装饰器，可以非常方便地扩展出自己的管道函数，或者继续封装其他itertools中的有用函数。

这里是Pipe官方给出的例子，用管道函数式编程解出<https://projecteuler.net/>中的三道题目：
``` py

    # Find the sum of all the multiples of 3 or 5 below 1000.
    euler1 = (itertools.count() | select(lambda x: x * 3) | take_while(lambda x: x < 1000) | add) \
           + (itertools.count() | select(lambda x: x * 5) | take_while(lambda x: x < 1000) | add) \
           - (itertools.count() | select(lambda x: x * 15) | take_while(lambda x: x < 1000) | add)
    assert euler1 == 233168

    # Find the sum of all the even-valued terms in Fibonacci which do not exceed four million.
    euler2 = fib() | where(lambda x: x % 2 == 0) | take_while(lambda x: x < 4000000) | add
    assert euler2 == 4613732

    # Find the difference between the sum of the squares of the first one hundred natural numbers and the square of the sum.
    square = lambda x: x * x
    euler6 = square(itertools.count(1) | take(100) | add) - (itertools.count(1) | take(100) | select(square) | add)
    assert euler6 == 25164150
```

**注意**：所有*惰性求值*的迭代器，都是只能求值一次的，如果再次求值会什么也得不到，因为`yield`堆栈已经走到底，无法回头。因此，当要对惰性迭代器重复使用时，必须故意地提前将其求值展开，或者利用`itertools.tee`来克隆一个迭代器。

<!-- more -->

## 输入输出
```py
"42" | stdout # 输出到标准输出 >>> 42

(0, 1, 2) | as_list # 求值并转换list类型并输出 >>> [0, 1, 2]

lineout # 行输出到标准输出
tee # 输出迭代器的每个元素，常用于debug。不同于itertools.tee!
as_tuple # 转换tuple类型并输出
as_dict # 转换dict类型并输出
netcat # 发送网络请求并读取响应
netwrite # 发送网络请求但不读取响应
```


## 数学运算
```py
[1, 2, 3, 4] | concat("#") # 连接字符串 >>>'1#2#3#4'

average # 求平均
count # 求长度或计数。类似len，但适用于所有迭代器
add # 求和，同sum
max # 求最大值，同max
min # 求最小值，同min
any # 只要有一个元素满足条件就返回真，类似any，但适用于所有迭代器
all # 如果所有元素都满足条件才返回真，类似all，但适用于所有迭代器

```

## 组合工具
``` py
(1, 2, 3, 4, 5, 6, 7, 8, 9) \
            | groupby(lambda x: x % 2 and "Even" or "Odd")
            | select(lambda x: "%s : %s" % (x[0], (x[1] | concat(', '))))
            | concat(' / ') # 将key函数作用于原循环器的各个元素, 根据key函数结果，将拥有相同函数结果的元素分到一个新的迭代器。以key函数返回结果为键, 以新迭代器为值的键值对序列。封装了itertools.groupby >>> 'Even : 1, 3, 5, 7, 9 / Odd : 2, 4, 6, 8'

[[1, 2], [3, 4], [5]] | chain | concat # 封装了itertools.chain, 将两个可迭代器展开连接成为一个。 >>> '1, 2, 3, 4, 5'

(1, 2, 3) | chain_with([4, 5], [6]) | concat # 封装了itertools.chain, 将两个可迭代器展开连接成为一个。>>> 1, 2, 3, 4, 5, 6

[[1, 2], [[[3], [[4]]], [5]]] | traverse | concat # 递归地展开迭代器并连接成一个 >>> '1, 2, 3, 4, 5'

'abc' | permutations(2) | as_list   # 排列组合之排列，封装了itertools.permutations, 从'abc'中挑选2元素，比如ab, ba, bc, ... (ab, ba都包含)将所有结果排序，返回为新的迭代器。

itertools.combinations('abc', 2)   # 排列组合之组合, 从'abcd'中挑选两个元素，比如ab, bc, ... 将所有结果排序，返回为新的迭代器。 

itertools.combinations_with_replacement('abc', 2) # 排列组合之组合，但允许两次选出的元素重复。即多了aa, bb, cc

itertools.product('abc', [1, 2]) | as_list   # 多个迭代器的笛卡尔积。相当于嵌套循环 >>> [('a', 1), ('a', 2), ('b', 1), ('b', 2), ('c', 1), ('c', 2)]

```

## 产生无限序列
```py
itertools.count(5, 2)     # 从5开始的整数循环器，每次增加2，即5, 7, 9, 11, 13, 15 ...步长默认为1

itertools.cycle('abc')    # 重复序列的元素，即a, b, c, a, b, c ...

itertools.repeat(10, 5)   # 重复元素10，共重复5次，即10,10,10,10,10。不写次数的话，默认无穷循环。
```

## 序列运算
```py
xrange(5) | aggregate(lambda x, y: x + y, initializer=0) # 同内置reduce函数 >>> 10

[5, -4, 3, -2, 1] | sort(key=abs) | concat # 排序, 同sorted。>>> '1, -2, 3, -4, 5'

[1, 2, 3] | select(lambda x: x * x) | concat # 作用同map/imap, >>> '1, 4, 9'

[1, 2, 3] | where(lambda x: x % 2 == 0) | concat # 作用同filter/ifilter >>> '2'

[1, 2, 3, 4] | take_while(lambda x: x < 3) | concat # 不断取出元素，直到运算为假，封装了itertools.takewhile, >>> '1, 2'

[1, 2, 3, 4] | skip_while(lambda x: x < 3) | concat # 不断跳过元素，直到运算为假，封装了itertools.dropwhile >>>  '3, 4'

(1, 2, 3, 4, 5, 6, 7, 8, 9) \
            | izip([9, 8, 7, 6, 5, 4, 3, 2, 1]) \
            | concat # 封装了zip的惰性版本，即itertools.izip，纵向合并两个序列 >>> '(1, 9), (2, 8), (3, 7), (4, 6), (5, 5), (6, 4), (7, 3), (8, 2), (9, 1)'

(1, 2, 3, 4, 5, 6, 7, 8, 9) | islice(2, 8, 2) | concat # 封装了slice的惰性版本，即itertools.islice，切片器 >>> '3, 5, 7'

itertools.compress('ABCD', [1, 1, 1, 0])  # 根据[1, 1, 1, 0]的真假值情况，选择第一个参数'ABCD'中的元素。 >>> A, B, C

take # 取出n个元素
skip # 跳过n个元素
first # 取出第一个元素
tail # 取出倒数n个元素
reverse # 逆序，同reversed
itertools.tee # 克隆出n个相同的迭代器

```


## 其他惰性求值版本函数
```py
itertools.imap(pow, (1,2,3),(1,2,3)) | as_list # map的惰性版本 >>> [1, 4, 27]

itertools.starmap(pow, ((2,3),(2,3))) | as_list # map的惰性版本，但是对sequence中每个元组来执行函数 >>> [8, 8]

itertools.ifilter # filter的惰性版本
itertools.ifilterfalse # filter的惰性反值版本
itertools.izip # zip的惰性版本
itertools.izip_longest # slice的惰性版本
```


## 自定义Pipe管道函数
```py
from pipe import *

select = Pipe(lambda iterable, pred: (pred(x) for x in iterable))

# OR

@Pipe
def first(iterable, default=None):
    try:
        return next(iter(iterable))
    except StopIteration:
        return default
```
## 参考：
* <http://www.cnblogs.com/vamei/p/3174796.html>
* <http://www.cnblogs.com/cython/articles/2169009.html>
* <http://www.jackyshen.com/2015/05/21/async-operations-in-form-of-sync-programming-with-python-yielding/>