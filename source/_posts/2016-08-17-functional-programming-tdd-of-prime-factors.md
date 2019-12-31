title: 用函数式编程来测试驱动出Prime Factor题目
date: 2016-08-17 21:06:07
tags:
	- functional programming
---

近年来，函数式编程[Funtional Programming](http://www.jackyshen.com/tags/functional-programming/)大行其道，成为业界的热门话题。2016年端午节期间，我给[中国软件匠艺小组](https://codingstyle.cn/)的小伙伴们出了一道题目：用[TDD](http://www.jackyshen.com/2015/12/10/tdd-test-driven-development/)的方式驱动出函数式编程版本的"Prime Factor"，不料各路神仙各显神通，争奇斗艳。

<!--more -->

## "Prime Factor"题目
这是一道经典的TDD题目：对给定的正整数N，求出其所有质因数，经典算法可以通过TDD驱动出二重循环，如下：

``` py
def calc(i):
	result = []
	for candidate in xrange(2,i):
		while i % candidate == 0 and i > candidate:
			result.append(candidate)
			i /= candidate
	result.append(i)
	return result

assert [2,2,2,3,3] == calc(72)
```
下面按照交作业的顺序展示一下大家的作品。然而TDD是一个推导的过程，仅仅看最终结果是不够的，能看到中间的状态就更好了。

## Ronald麦大仙的Python版本
``` py
def factors_from(i, xs):
    first = [x for x in xs if i % x == 0][0]
    return [first] + factors(i / first)

def factors(i):
    return [] if i < 2 else factors_from(i, xrange(2, i+1)) 
    
assert factors(3*7*11*13) == [3,7,11,13]
```

## 梁辰的Ruby版本
TDD过程存储于<http://cyber-dojo.org/kata/edit/9B0ED5F482?avatar=lion>

``` rb
def first_divisible_factor start_factor, number
  (start_factor..number).select{|factor| number%factor == 0}[0]
end
 
def factors_of number, start_factor=2
  return [] if number == 1
  factor = first_divisible_factor start_factor, number
  [factor] + factors_of(number/factor, factor)
end
```

## [武可](https://codingstyle.cn/asj)的Haskell版本
TDD过程存储于<http://cyber-dojo.org/kata/edit/2EF003C866?avatar=zebra>

``` haskell
factorOf _ 1 = []
factorOf p n 
    | exact_div = p : factorOf p (n `div` p)
    | no_more = [n]
    | otherwise = factorOf next_p n
    where 
        exact_div = n `mod` p == 0
        no_more = p * p  > n 
        next_p = p + 1

factor = factorOf 2
```

## [Jacky申导](https://codingstyle.cn/mebusw)的Python版本
TDD过程存储于<http://cyber-dojo.org/review/show/2BE7D52F15>

``` py
def f(n, p=2):
    if n < 2: return []
    if n % p == 0: return [p] + f(n/p, p)
    return f(n, p+1)

###############################################

from hiker import f
import unittest
 
class TestHiker(unittest.TestCase):
    def test_prime_factor(self):
        self.assertEqual([], f(1))
        self.assertEqual([2], f(2))
        self.assertEqual([3], f(3))
        self.assertEqual([2,2], f(4))
        self.assertEqual([2,3], f(6))
        self.assertEqual([2,2,2], f(8))
        self.assertEqual([3,3], f(9))
        self.assertEqual([2,2,3,3,3,5,5,19], f(2*2*3*3*3*5*5*19))
 
if __name__ == '__main__':
    unittest.main()
```

## 更多函数式编程内容见：
* <http://www.jackyshen.com/tags/functional-programming/>
* <https://codingstyle.cn/topics/node15>
