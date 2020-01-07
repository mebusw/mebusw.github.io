title: TDD测试驱动开发
date: 2015-12-10 09:58:04
tags:
    - tdd
    - xp
categories:
    - agile
    - engineering

---

# 定义

在编写任何产品代码之前，先写一个测试来表达期望的行为，此时测试应当会失败，因为功能还没有实现。然后编写实现来使测试通过。换句话说，__"只为修复失败的测试而编写代码！"__
TDD要求测试可以完全地自动化运行，通常会借助单元测试作为基础。


![](http://blog.da2k.com.br/uploads/2015/01/tdd-red-refactor-green.jpg)

<!--more-->

# 做法

TDD循环分为三步：
1. "红"-先写一个失败的测试；
2. "绿"-实现产品代码从而使测试变得通过；
3. "重构"-在测试通过的保护之下对代码进行重构，优化设计。



# 好处
TDD是一种编程技术和设计方法，无论在开始编程之前进行了怎样的设计和建模，TDD都有助于提高代码质量和设计质量。TDD可以赋予你对代码的质量的自信以及对代码进行重构的勇气。采用TDD，我们将会得到简单、清晰的设计，代码也是清晰和无bug的。采用TDD的结果就是可以让我们拥有一套伴随产品代码的详尽的自动化测试集，将来无论出于什么原因而需要对代码进行更新维护时，在这套测试集的回归保护之下，产品代码将会一直是健壮的。

# 示例
下面以一个求出斐波那契(Fibonacci)数列的例子来介绍测试驱动开发，工具采用Java语言和JUnit。

待开发的对象仅仅是一个fib()函数。
第一个测试显示fib(0)=0，实现返回一个常量。

``` java
public void testFibonacci() {
    assertEquals(0, fib(0));
}

int fib(int n){
    return 0;
}
```

第二个测试显示fib(1)=1。让这个测试通过有好几种办法，这里我们选择把0当做特殊情况对待。
``` java
public void testFibonacci() {
    assertEquals(0, fib(0));
    assertEquals(1, fib(1));
}

int fib(int n){
    if (n == 0) return 0;
    return 1;
}
```

第二个测试显示fib(2)=1。
第三个测试显示fib(3)=2。
如法炮制，把较小的输入当做特殊情况。

``` java
public void testFibonacci() {
    assertEquals(0, fib(0));
    assertEquals(1, fib(1));
    assertEquals(1, fib(2));
}

int fib(int n){
    if (n == 0) return 0;
    if (n <= 2) return 1;
    return 2;
}
```

目前测试全部通过。测试中已经开始出现令人讨厌的重复了，再增加新的测试用例，只会使情况变得更糟。
现在我们可以进行一般化了，函数返回2，但是其真正用意并不是2，而是1+1。
注意，此时重构代码之后，所有测试用例仍然应该全面通过。

``` java
int fib(int n){
    if (n == 0) return 0;
    if (n <= 2) return 1;
    return 1 + 1;
}
```

其中，第一个1是fib(n-1)的一个实例，第二个1是fib(n-2)的一个实例。

``` java
int fib(int n){
    if (n == 0) return 0;
    if (n <= 2) return 1;
    return fib(n - 1) + fib(n - 2);
}
```

整理一下，由于同样的结构也适用于fib(2)，因此我们强化第二个条件判断。
``` java
int fib(int n){
    if (n == 0) return 0;
    if (n == 1) return 1;
    return fib(n - 1) + fib(n - 2);
}
```

如此，我们就得到了整个斐波那契数列，函数实现完全是由测试驱动出来的。


# Reference:
* 《测试驱动开发 (Test Driven Development)》, by Kent Beck
* <http://www.jackyshen.com/2015/12/10/unit-testing/>
* <http://www.jackyshen.com/2015/07/11/a-junit-primer/>
* <http://www.jackyshen.com/2015/12/09/refactoring/>
* <http://www.jackyshen.com/2014/05/04/origin-and-tools-of-bdd/>
* 想要进一步了解敏捷工程实践，关注[Certified Scrum Developer国际认证课程](http://www.uperform.cn)

