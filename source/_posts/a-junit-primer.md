title: 单元测试JUnit入门
date: 2015-07-11 11:35:17
tags:
    - junit
    - xp
    - unit test
    - java
categories:
    - agile
    - engineering

---

（下文摘自 申健/ @申导 翻译的[《有效的单元测试》](http://book.douban.com/subject/26364867/)附录A）

-------

在Java生态系统中，现如今事实上的单元测试框架是JUnit。年复一年，越来越少的Java程序员没有见过JUnit测试代码了。不过，每个人总有第一次，某些人也可能使用着其他的测试框架，那么我们编写了这个简短的附录，快速地开始用JUnit来编写测试。

理解JUnit有两个基本元素。首先，你必须了解JUnit测试代码的结构和生命周期。我们从这里开始。我们将看一看如何在测试类(test classes)中定义测试方法(test methods)，然后熟悉测试的生命周期——JUnit如何以及何时实例化和调用你的测试类及其方法。

其次，就是JUnit的断言(Assertion) API。基本的和常用的断言方法很简单，你看到它们的方法签名就知道如何使用了。因此，我们只会通过名字来调用这些方法，而聚焦于那些缺乏自我解释的更加“不透明”的断言。

总的来说，JUnit是一个小而简单的框架，我毫不怀疑你会快速地学会运行它。最终你会在某些地方卡住，可以求助于专门的JUnit书籍，比如Manning Publication出版的优秀书籍《JUnit in Action (2nd edition)》就会派上用场。在那之前，我希望本附录包含入门需要的全部内容，帮助你跟上本书的其他内容。

<!-- more -->

# A.1 基本的JUnit测试类

简而言之，JUnit测试类只是普通的Java类，包含一个或多个测试方法，以及零个或多个setup和teardown方法。JUnit也为找到的测试定义了一个简单的生命周期。

以下章节将依次讲解JUnit测试类的基本元素，从声明测试方法开始。

## A.1.1 声明测试方法

JUnit测试最基本的形式是带有注解的普通实例方法。这些方法执行被测代码，使用JUnit提供的API来断言所期望的条件和副作用。清单A.1中的例子是一个简单JUnit测试类的样子。


清单A.1 带有@org.junit.Test注解的测试方法
``` java
import org.junit.Test;
import static org.junit.Assert.assertEquals;
public class ExampleTest {
           @Test
           public void thisIsATestMethod() {
              assertEquals(5, 2 + 3);
}
           @Test
           public void thisIsAnotherTestMethod() {
              WorldOrder newWorldOrder = new WorldOrder();
              boolean result = newWorldOrder.isComing();
              assertFalse(result);
           }
}
```

清单A.1 展示了一个带有两个测试方法的简单JUnit测试类。如果JUnit发现一个非public的方法，就会忽略它。如果发现方法带有参数，就会忽略它。如果方法返回非void，就会忽略它。如果方法声明为static，就会忽略它。如果方法没有JUnit的@Test注解，就会忽略它。

真的很简单：测试方法必须是public void的，并且不带参数。(测试方法可以声明为抛出异常。这是可以的。)

现在看看JUnit抓到测试类之后，测试所要经历的生命周期。

## A.1.2 JUnit测试的生命周期


以清单A.1中的类为例，JUnit扫描各个方法，然后对满足上述约束的签名：
1.  实例化测试类的一个实例
2.  调用测试类实例的setup方法
3.  调用测试类
4.  调用测试类实例的teardown方法

对测试类中找到的所有测试方法，JUnit都会这样做，包括继承于基类的测试方法。同样，setup和teardown方法可以在要运行的测试类中定义，也可以在基类中定义。

如果测试没有抛出异常，就认为它通过了。例如，如果条件没有被满足，清单A.1中的assertEquals() 和 assertFalse()断言就会抛出异常来使测试失败。同样，如果某个setup或teardown方法抛出异常，该测试就会被认为是失败了，并相应地报告出来。


## A.1.3 测试的setup和teardown

说到setup和teardown，清单A.2中的小例子展示了它们的用法。


清单A.2 @Before和@After注解标示了setup和teardown方法

``` java
public class ExampleTest {
   private Locale originalLocale;
   @Before
   public void setLocaleForTests() {
      this.originalLocale = Locale.getDefault();
      Locale.setDefault(Locale.US);
   }
   @After
   public void restoreOriginalLocale() {
      Locale.setDefault(originalLocale);
   }
}
```

清单A.2中给出一个测试类例子，它确保了在测试执行期间活动区域设定是Locale.US，并确保原来的区域设定在事后被恢复。我们是童子军原则(见O’Reilly commons上的《The Boy Scout Rule》源自鲍勃大叔, 2009年11月24日, http://mng.bz/Cn2Q)的忠实粉丝，所以我们希望在测试执行之后，测试能保持原样（或者更好）。


测试类可以有任意多的@Before和@After方法；JUnit会确保调用到每一个，尽管它不保证调用的顺序。

还有@BeforeClass和@AfterClass注解，用来做类似的事情，只不过它们对每个测试类来说只会运行一次，而不是对每个测试方法运行一次。如果你只想在测试类的所有测试执行之前或之后运行一次，那就用得到这些。(我个人主要将其用在集成测试的上下文中，确保在发起网络连接之前，某个服务器组件正在运行。)


# A.2 JUnit 断言

如清单A.1所见，JUnit提供了一组常用的断言，作为org.junit.Assert类中的public static方法。将这些方法静态地导入是一个常见的实践，使其用法更加简洁。

断言用于检查两个对象是否相等，对象引用是否为null，两个对象引用是否指向同一个对象，条件是为true还是false，等等。完整的断言 API可以在网上www.junit.org找到，但这里有个快速的概述。


* assertEquals——断言两个对象（或基本对象）是否相等
* assertArrayEquals——断言两个数组是否包含相同的元素
* assertTrue——断言语句为真
* assertFalse——断言语句为假
* assertNull——断言对象引用为空
* assertNotNull——断言对象引用不为空
* assertSame——断言两个对象引用指向同一个实例
* assertNotSame——断言两个对象引用指向不同实例
* assertThat——断言对象满足指定条件（见A2.2节中更详细的解释）

特殊情况需要特殊对待，因此咱们快速浏览一些这些情况。首先，检查你预期抛出的异常。


## A.2.1 断言异常被抛出

有时你想要的代码行为就是在某些情况下抛出异常。例如，对于无效输入，你希望抛出IllegalArgumentException。如果你想要的行为就是异常，而JUnit将测试方法抛出的异常理解为测试失败，那你要如何测试这种预期的异常？

你可以在测试方法内放置try-catch，来捕获预期的异常（而且，如果没有抛出异常的话就令测试失败），但JUni提供了更方便的方式来做这件事，见清单A.3。


清单A.3 @Test注解允许我们声明预期的异常
``` java
        @Test(expected = IllegalArgumentException.class)
        public void ensureThatInvalidPhoneNumberYieldsProperException() {
           FaxMachine fax = new FaxMachine();
           fax.connect("+n0t-a-ph0n3-Numb3r");  // should throw an exception
        }
```

清单A.3中我们使用了@Test注解的expected属性，来声明我们期望在测试方法执行的时候抛出一个IllegalArgumentException。如果该异常没有被抛出（或是抛出了其他的异常），测试就会失败。

这是一个检查异常的简洁方式。但有时你想更具体地了解抛出的是哪种异常。例如，除了抛出的异常要符合某个类型之外，你还想检查其“message”中携带的特殊信息，或者它封装了某种“根因”异常。

这些情况下，你需要回滚到古老却优秀的try-catch，然后自行断言。清单A.4展示了之前例子的变体，它额外检查了抛出的异常中具体提到的无效参数。


清单A.4 使用try-catch来检查抛出的异常

```java
@Test
public void ensureThatInvalidPhoneNumberYieldsProperException() {
   String invalidPhoneNumber = "+n0t-a-val1d-ph0n3-Numb3r";
   FaxMachine fax = new FaxMachine();
   try {
      fax.connect(invalidPhoneNumber);
      fail("should've raised an exception by now");
   } catch (IllegalArgumentException expected) {
      assertThat(expected.getMessage(),
                  containsString(invalidPhoneNumber));
} }
```

这种方法将Java和JUnit的全部威力赋予我们，来断言任何需要检查的东西。然而这有点啰嗦，而且很容易忘记调用fail()，所以如果你只需要检查抛出异常的类型的话，就采用基于注解的方法吧，那样更干净和简洁。

说到简洁而强大的断言，当org.junit.Assert内建的断言不能满足你的要求时，你总是可以用自定义匹配器来扩展强大的assertThat()。看看assertThat()和Hamcrest是怎么做到的。


## A.2.2 assertThat() 和 Hamcrest匹配器


org.junit.Assert提供的断言中要数assertThat().最为特别。它是一个钩子，允许程序员自行扩展基本的断言，或者使用第三方匹配器(matchers)库。

基本语法是这样的：

``` java
assertThat(someObject, [matchesThisCondition]);
```

换句话说，第一个参数是作为断言上下文的对象或值，而第二个参数是匹配器，JUnit会将实际的断言工作委托给它。

这些匹配器不是普通的对象，而是Hamcrest匹配器。Hamcrest(https://github.com/hamcrest/JavaHamcrest)是一个开源API，它有自己的一组标准的匹配器实现，可用于测试。

如果内置的断言或Hamcrest匹配器都不满足你所要表达的意图，你可以通过实现Hamcrest的Matcher接口来创建自己的匹配器。假设你要断言一个看起来像是有效国际电话号码的字符串。这种情况下，一个基本的自定义Hamcrest匹配器会是清单A.5中的样子。


清单A.5 借助assertThat()来使用自定义的Hamcrest匹配器

```java
import static org.hamcrest.CoreMatchers.is;
import static org.hamcrest.CoreMatchers.not;
import static org.junit.Assert.assertThat;
import org.hamcrest.BaseMatcher;
import org.hamcrest.Description;
import org.hamcrest.Matcher;
import org.junit.Test;
public class ExampleTest {
    ...
    @Test
    public void providesPhoneNumberInInternationalFormat() {
          String phoneNumber = widgetUnderTest.getPhoneNumber();
          assertThat(phoneNumber, is(internationalPhoneNumber()));
    }
    public Matcher<String> internationalNumber() {
          return new BaseMatcher<String>() {
    /**
    ITU-T E.123 requires that international phone numbers
    include a leading plus sign, and allows only spaces to
    separate groups of digits.
     */
    @Override
    public boolean matches(Object candidate) {
        if (!(candidate instanceof String)) {
           return false;
        }
        return ((String) candidate)
    }.matches("^\\+(?:[0-9] ?){6,14}[0-9]$");

    @Override
    public void describeTo(Description desc) {
        desc.appendText("ITU-T E.123 compliant " +
                    " international phone number");
    } };
} }
```

当我们的测试调用assertThat(phoneNumber, is(internationalPhoneNumber()))时，JUnit使用辅助方法创建的BaseMatcher 来执行断言。

我们自定义的匹配器实现包含两个方法。matches()的责任是指出给定的候选对象是否通过了断言。本例中，我们的算法很微不足道：检查对象确实是String ，然后检查它满足某个匹配国际电话号码的正则表达式。

自定义匹配器中第二个方法describeTo()，负责给匹配器要寻找的内容提供有意义的描述。如果断言失败，JUnit会打印出类似下面的消息：

`java.lang.AssertionError:
Expected: is ITU-T E.123 compliant international phone number
     got: "+1 (234) 567-8900"`

当测试突然失败，这就很有用，因为它解释了你要找的是什么，而你得到的又是什么。好吧，这种情况下我们大概还需要查看匹配器的实现，才能知道兼容ITU-T E.123的国际电话号码需求——除非这是我们正在工作的代码库。

现在我们已经介绍了JUnit的基本元素。了解这些之后，你应该要好好消化一段时间了。当你最终碰到问题而需要更多东西时，请参考附录B，那里解释了如何进一步扩展JUnit，不仅仅是自定义匹配器哦。

------

# Reference:
* <http://www.jackyshen.com/2015/12/10/unit-testing/>
