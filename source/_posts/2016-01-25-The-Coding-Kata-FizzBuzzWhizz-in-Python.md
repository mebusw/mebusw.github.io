title: 用Python实现编程练习Kata-FizzBuzzWhizz
date: 2016-01-25 15:28:03
tags:
    - python
    - functional programming
    - kata
    - craftsmanship
categories:
    - programming
    - python

---


> Functional programming leads to deep insights into the nature of computation. -- Martin Odersky
 

近日软件匠艺小组的朋友用函数式编程的方式重新实现了一个古老的Kata练习题:FizzBuzzWhizz，用了很多种语言及其特性，非常精妙。这是一个软件匠艺的文艺复兴时代。
原帖见<https://codingstyle.cn/topics/100> 


# 题目

FizzBuzzWhizz详细描述请自行查阅相关资料。此处以3, 5, 7为例，形式化地描述一下问题。

    r1
    + times(3) -> Fizz
    + times(5) -> Buzz
    + times(7) -> Whizz
    r2
    + times(3) && times(5) && times(7) -> FizzBuzzWhizz
    + times(3) && times(5) -> FizzBuzz
    + times(3) && times(7) -> FizzWhizz
    + times(5) && times(7) -> BuzzWhizz
    r3
    + contains(3) -> Fizz
    + the priority of contains(3) is highest
    rd
    + others -> others

# Python实现

借助原帖的测试用例，我用python语言也练习了一把。本来想引入Pipe库用管道式的方式写，可能会取得一些方便，但想想还是用原生的语法来做吧。

<!--more-->

``` py

def times(n, s):
    return lambda x: s if x % n == 0 else ''
time3 = times(3, 'Fizz')
time5 = times(5, 'Buzz')
time7 = times(7, 'Whizz')


def allof(*rules):
    return lambda x: reduce(lambda s, rule: s + rule(x), rules, '')


def anyof(*rules):
    def lmb(x):
        for rule in rules:
            if rule(x):
                return rule(x)
    return lmb

r1 = anyof(time3, time5, time7)
r2 = anyof(allof(time3, time5, time7),
           allof(time3, time5),
           allof(time3, time7),
           allof(time5, time7))
r3 = lambda x: 'Fizz' if str(x).count('3') else ''
rd = lambda x: str(x)
lord_of_the_rule = anyof(r3, r2, r1, rd)

```

这里是测试用例。

``` py
import unittest

class FizzBuzzWhizzTest(unittest.TestCase):

    def test_rule_1(self):
        self.assertEqual('Fizz', lord_of_the_rule(3))
        self.assertEqual('Buzz', lord_of_the_rule(5))
        self.assertEqual('Whizz', lord_of_the_rule(7))

    def test_rule_2(self):
        self.assertEqual('FizzBuzzWhizz', lord_of_the_rule(3 * 5 * 7))
        self.assertEqual('FizzBuzz', lord_of_the_rule(3 * 5))
        self.assertEqual('BuzzWhizz', lord_of_the_rule((5 * 7) * 2))

    def test_rule_3(self):
        self.assertEqual('Fizz', lord_of_the_rule(13))

    def test_priority_of_r3_greater_than_r2(self):
        # rather than 5*7 => BuzzWhizz
        self.assertEqual('Fizz', lord_of_the_rule(35))

    def test_rule_d(self):
        self.assertEqual('2', lord_of_the_rule(2))


if __name__ == "__main__":
    #import sys;sys.argv = ['', 'Test.testName']
    unittest.main()

```

源代码见 <https://github.com/mebusw/fizz-buzz-whizz/blob/master/python/FizzBuzzWhizzTest.py>
