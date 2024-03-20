title: 使用Groovy语言替代JUnit为Java程序编写单元测试
date: 2015-10-01 14:33:46
tags:
    - unit test
    - groovy
    - jvm
    - java

categories:
    - programming
    - java
---

(本文改编自 @申导 翻译的[《有效的单元测试》](http://book.douban.com/subject/26364867/)，如果对本文感兴趣，请支持正版书籍。)

编程是用计算机可理解的语言来表达你的想法和意图。对于Java程序员来说就是编写一种可以由Java编译器编译为可以运行在JVM上的字节码的代码。不止一种编程语言可以编写能运行在JVM上的代码，不过每种JVM语言都具有其独特的语法和感觉，但有一点是相同的：关于在JVM创建应用程序这件事上，她们都号称比Java更加简洁和更具表达力。

# 另类JVM语言

另类JVM语言的历史可追溯到15年前，那时Jim Hugunin在编写Jython，即一种JVM上的Python语言实现。尽管Jython难以获得发展的动力，但它启发了后来许多JVM语言的出现。

受到Jython的启发，2003年Groovy语言开始在JVM上登场，有着精简语法的Groovy承诺与Java代码之间流畅的互操作性和极高简洁性，使之成为JVM上编写脚本的重要选择。其他运行在JVM平台上的语言还包括Scala, JRuby, Clojure等，以及Java本身。


概括来说，各种另类JVM语言的一些潜在优势在于：

* 更少的样板代码语法可以去芜存菁
* 更多的文本(literal)数据结构
* 针对标准类型的额外方法
* 更多强大的语法结构

<!-- more -->

新语言、新语法、新API刚开始看起来会令人生畏，但你从来都不是一个人在战斗。针对这些语言已经存在大量信息和文档，既有在线的也有实体书。另外，在这些新编程语言周围形成的社区是友好和充满热情的，很容易融入进去。

我们可以自由支配新语言结构的威力。闭包、列表比较、原生正则表达式、字符串插值、模式匹配、隐式类型转换——不胜枚举，这些语言特性迟早会派上用场。

这些另类JVM语言的语法糖和表达力、丰富的API、以及强大的语法结构所带来的好处全部都可以运用在编写测试上。许多公司（包括我自己的）越来越多地转向成熟语言例如Scala来开发他们的生产系统。其他一些公司则由于各种各样的原因，还在犹豫要不要开头。


# 我们可以从采用Groovy来编写单元测试来开始

除了更适合完成工作的优点，团队还可以通过从自动化测试上开始采用新语言从而在一个安全的环境中尝试和学习。以这种诱导性毒品的方式，程序员有时间去学习语言及其惯例，而不用担心由于不熟悉新技术而导致未知的生产问题。

不用动态语言如Groovy编写生产代码的一个更常见原因是性能。表达力和简洁语法的喜悦有时是牺牲绝对性能才换来的。潜在的性能损失通常对于测试代码来说是无所谓的。毕竟测试代码的第一优先级是可读性，而这正是动态语言的强大之处。只要性能损失不严重，你就能从表达力和语法简单性中受益。

总的来说，最适合编写测试的是那些提供简洁语法和通用数据结构的语言，而不是那些将复杂计算变为现实的语言。从这点来讲，Groovy更容易令Java程序员上手。


# 用Groovy语言来编写测试

用Groovy编写单元测试的前提是用显著轻量的语法来表达意图。尽管Groovy仍与Java相似，但由于它将许多Java语法变成可选从而变得轻量。

例如，在Groovy中分号、括号和return关键字都变成可选的，可以不写。
Groovy采用一些方式减少了程序员需要写的样板代码。因为Groovy的可见性修饰符缺省为public，你可以省略它，除非你要特别地限制访问。你也可以看到在Groovy中可以不声明变量类型，而是用def来声明变量。我们经常会从其他字符串、数字和对象的字符串表示来组装字符串。Groovy也能使这种事情变得更整洁。

```py
class ClusterTest {
    def numberOfServers = 3
    def cluster
    
    @Before
    void setUp() {
        cluster = new Cluster()
        numberOfServers.times {
            def name = "server-${it}"
            cluster.add new Server(name: name, maxConnections: 1)
        }
    }
}

```

这个例子展示了许多特性，显示了Groovy语言在创建复杂对象方面是如此优秀。一开始是个循环体，在其中创建了多个较小对象作为测试夹具。


```py
def reverse = [
      equals: false,
      compare: { Object[] args -> args[1].compareTo(args[0]) }
   ] as Comparator
Collections.sort(list, reverse)
```

你可以用map代替代码块来实现更复杂的接口，充分享受函数式编程的魅力吧。

## Groovy式的JUnit测试

```java
public class RegularJUnitTest {
   private ComplexityCalculator complexity = new ComplexityCalculator();
   @Test
   public void complexityForSourceFile() {
      double sample1 = complexity.of(new Source("Sample1.java"));
      double sample2 = complexity.of(new Source("Sample2.java"));
      assertThat(sample1, is(greaterThan(0.0)));
      assertThat(sample2, is(greaterThan(0.0)));
      assertTrue(sample1 != sample2);
} }
```

为了比较，咱们用Groovy重写一个JUnit4测试。

```py
class GroovyJUnitTest {
   def complexity = new ComplexityCalculator()
   @Test
   void complexityForSourceFile() {
      def sample1 = complexity.of new Source("Sample1.java")
      def sample2 = complexity.of new Source("Sample2.java")
      assertThat sample1, is(greaterThan(0.0d))
      assertThat sample2, is(greaterThan(0.0d))
      assertTrue sample1 != sample2
   }
}

```

注意源代码的长度是相同的。尽管Groovy提供了大量的语法糖，然而大多数编写良好的JUnit测试就算换成Groovy，也不会变得更短。但圆括号和分号将会大大减少，并且越复杂的测试，Groovy带来的收益越多。

再来一个比较，可以看出用Groovy写单元测试，在准备数据时候更加简洁，更加精益：
``` java
    //java
    AdPipeLineState state1 = new AdPipeLineState();
    AdBranch branch1 = new AdBranch();
    branch1.setBranchId(1);
    state1.setAdBranch(branch1);
    AdPipeLineState state2 = new AdPipeLineState();
    AdBranch branch2 = new AdBranch();
    branch1.setBranchId(2);
    state1.setAdBranch(branch2);

    List<AdPipeLineState> states = new ArrayList();
    states.add(branch1);
    states.add(branch2);

    assertEquals("...", dao.buildSqlToQueryLogOfAllBranches(states);

    ////////////////////////////
    //groovy
    def state1 = new AdPipeLineState(adBranch: new AdBranch(branchId: 1))
    def state2 = new AdPipeLineState(adBranch: new AdBranch(branchId: 2))

    assertEquals "...", dao.buildSqlToQueryLogOfAllBranches([state1, state2])
```


