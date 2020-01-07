title: 单元测试
date: 2015-12-10 09:57:50

tags:
    - junit
    - xp
    - unittest
categories:
    - agile
    - engineering

---
单元测试（Unit Testing简称UT）是最微小规模的测试；测试粒度在某个类、函数或代码块。典型地由程序员而非测试员来做。单元测试是在软件开发过程中要进行的最低级别的测试活动，软件的独立单元将在与程序的其他部分相隔离的情况下进行测试。

在Java语言环境下，推荐采用JUnit，也可以和Eclipse集成或单独运行。

语言环境 | 推荐工具
---- | ----
Java/Android | JUnit
C# | NUnit
iOS | XCTest
Javascript | Jasmine

下文以Java的单元测试框架JUnit4和Eclipse为例。

<!--more-->

理解JUnit有两个基本元素。首先，你必须了解JUnit测试代码的结构和生命周期。我们从这里开始。我们将看一看如何在测试类(test classes)中定义测试方法(test methods)，然后熟悉测试的生命周期——JUnit如何以及何时实例化和调用你的测试类及其方法。
其次，就是JUnit的断言(Assertion) API。基本的和常用的断言方法很简单，你看到它们的方法签名就知道如何使用了。因此，我们只会通过名字来调用这些方法，而聚焦于那些缺乏自我解释的更加“不透明”的断言。

简而言之，JUnit测试类只是普通的Java类，包含一个或多个测试方法，以及零个或多个setup和teardown方法。JUnit也为找到的测试定义了一个简单的生命周期。
JUnit测试最基本的形式是带有注解的普通实例方法。这些方法执行被测代码，使用JUnit提供的API来断言所期望的条件和副作用。

下面展示了一个带有一个测试方法和一个setup方法的简单JUnit测试类。测试方法必须是public void的，并且不带参数。(测试方法可以声明为抛出异常。这是可以的。)


``` java
import static org.junit.Assert.*;
import org.junit.*;

pulbic class CalculatorTest() {
    private Calculator calculator = new Calculator();

    @Before
    public void setUp() throws Exception {
        calculator.clear();
    }

    @Test
    public void given_2_when_add_3_then_answer_is_5() {
        //given
        calculator.add(2);

        //when
        calculator.add(3);

        //then
        assertEquals(5, calculator.getAnswer());
    }
}

```

可以看到，每个单元测试都是由三部分组成的，称为3A模式：
1.  *Arrange* - 准备被测类或函数，以及所依赖的协作者，准备相应的场景数据： 
    WorldOrder newWorldOrder = new WorldOrder();
2.  *Act* – 调用被测方法，并得到返回结果：
    boolean result = WorldOrder.isComing();
3.  *Assert* – 断言返回结果是否与期望值是否一致，作为单元测试的结果：
    assertFalse(result);

测试方法的命名也可用given-when-then的三段式，将方法名作为测试用例的描述。

@Before和@After和注解用来在每个测试执行之前和之后调用，一般用来做测试的事前设置(SetUp)和事后清理(TearDown)。测试类可以有任意多的@Before和@After方法；JUnit会确保调用到每一个，尽管它不保证调用的顺序。

# Reference:
* <http://www.jackyshen.com/2015/07/11/a-junit-primer/>
* 想要进一步了解敏捷工程实践，关注[Certified Scrum Developer国际认证课程](http://www.uperform.cn)
