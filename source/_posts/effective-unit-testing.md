title: 有效的单元测试之一--代码坏味道（读书笔记精华带源码）
date: 2022-01-01 14:35:18
tags:
    - ci
        - unit testing
        - automation
        - java

---

（摘要）



---



[TOC]

## 自动化测试的价值

开发者应该编写自动化测试，以便当发现回归问题时就使构建失败。而且，测试先行的编程风格已有大量的专业研究，使用自动化测试不仅是保护回归，而且是帮助设计，在编写代码之前就指出代码的期望行为，从而在验证实现之前先验证设计。

来认识一下小马。小马是著名的编程奇才，两年前刚毕业，然后加入了本地一家投行的IT部门，为银行开发用于在线自助服务的web应用程序。作为团队中最年轻的成员，起初他保持低调，集中精力学习银行的领域知识，熟悉“这里做事的方式”。

几个月后，小马注意到团队的工作很多都是**返工**：修复程序员的错误。他开始关注团队修复错误的类型，发现单元测试可以轻易地捕获到其中大多数。当他感觉到哪里存在特别容易出错的代码时，小马就对其编写单元测试。

> 测试帮助我们捕获错误。

一段时间以后，团队其他人也开始到处编写单元测试。Marcus已被测试感染 (test-infected) 了，他碰过的代码几乎都具有相当高的自动化测试覆盖率。除了在第一次时犯错，他们不会再花费时间修复过去的错误，待修复缺陷的总数在下降。测试开始清晰可见地影响着团队工作的质量。

自从小马编写第一个测试，已经过去近一年了。团队的测试覆盖率在近几个月快速提高，达到了98%的分支覆盖率。小马曾认为他们应该推动那个数字直到100%。但过去几周，他打定了主意——那些缺失的测试不会给他们带来更多价值，花费更多精力来编写测试不会带来额外的收益。许多没有测试覆盖的代码，只因所用的API要求实现某些接口，但小马的团队并未用到它们，那么何必测试那些空方法桩呢？

> 100%测试覆盖率不是目标

100%的覆盖率并不能够确保没有缺陷——它只能保证你所有的代码都执行了，不论程序的行为是否满足要求。与其追求代码覆盖率，不如将重点关注在确保写出有意义的测试。

后来，高级软件架构师老赛加入了自助服务团队，并快速地成为了低级成员的导师，包括小马在内。小马习惯于先写代码，在提交到版本控制系统之前再补充单元测试。但老赛的风格是先写一个会失败(很明显是这样)的测试，再写足够使测试通过的代码，然后写另一个失败的测试。他重复这个循环直到完成任务。

与老赛共事，小马意识到自己的编程风格开始转变。他的对象结构不同了，代码看起来稍微不同了，只是因为他开始从调用者的角度来审视代码的设计和行为了。

> 测试帮助我们针对实际使用来塑造设计。

想到这些，小马觉得好像明白了一些道理。当他试图用语言表达出他的编程风格是如何改变的，以及收到了哪些效果时，小马明白了他写的测试不仅仅是质量保证的工具，或是对错误及回归的保护。测试作为一种**设计代码的方式**，提供了具体的目的。编写使失败测试通过的代码比以前的方式简单多了，而且该做的也都做了。

> 通过明确地指出所需的行为，测试帮助我们避免镀金。

主人公小马的经历正如许多爱好测试的编程人员一样，在不断地认识和理解测试。我们经常因为相信单元测试有助于避免尴尬、耗时的错误而开始编写它们。随后我们会学到，将测试作为安全网只是等式的一部分，而另一部分——或许是更大部分——其好处是我们将测试表达为代码的思考过程。

大多数人同意说编写**一些**测试是不费脑筋的。但随着我们接近完全的代码覆盖率，我们不那么确定了——我们差不多已经为一切都编写了测试，而剩下的没有测试的代码是微不足道，几乎不会破坏。这就是某些人所谓的收益递减。因此，

> 编写测试的最大价值不在于结果，而在于编写过程中的学习。

（略，请阅读正版原书）



## 何为优秀与代码坏味道

可读性、可维护性、可信赖性...







## 单元测试代码的可读性

### 原始类型断言

断言应该表达某种假设或意图。它们应该声明代码的行为。基本断言的问题在于它们缺乏意义，因为断言的基本原理和意图隐藏在看上去无意义的单词和数字背后，造成它们难以理解，并且难以验证断言的正确性。

下面例子展示了一个**全局正则搜索(grep)**程序的测试。grep程序其实就是逐行处理某种文本输入，在处理时可以包括或排除掉含有特定子串或模式的文本行。测试应当是你的对象所提供功能的具体例子，那么我们来看一看这个测试是否说清楚了grep程序该做的事情。但愿这个测试没有受到原始类型断言的折磨！

```java
@Test
public void outputHasLineNumbers() {
   String content = "1st match on #1\nand\n2nd match on #3";
   String out = grep.grep("match", "test.txt", content);
   assertTrue(out.indexOf("test.txt:1 1st match") != -1);
   assertTrue(out.indexOf("test.txt:3 2nd match") != -1);
}
```

这个测试在干什么？首先，我们定义了文本字符串`content`来表示一段输入，然后调用grep程序，然后对grep结果输出中的某些东西进行断言。这些**东西**就是问题所在。断言的目标并不清楚，因为断言过于基本——它与测试的其他部分说着不同的语言。

具体来说，我们在这个断言中是要计算输出结果中的另一个文本字符串的索引，并将其与`-1`进行比较；如果索引为`-1`，那么测试失败。测试的名字叫做`outputHasLineNumbers`，显然，对于在代码中grep()的具体调用，输出结果应当包含正在进行逐行查找的文件名，后面加上行号。

不幸的是，我们不得不经过这个思考过程才能理解为什么我们要计算索引，为什么查找`test.txt:1`而不是其它东西，为什么与`-1`进行比较，当索引是`-1`或不是`-1`时测试是否会失败？这无需火箭科学就能找到答案，但却是我们大脑的不必要的认知工作。

<!--more-->

### 过度断言

**过度断言**是如此谨慎地敲定每个待检查行为的细节，以至于它变得脆弱，并且掩盖了整体广度和深度之下的意图。当你遇到过度断言，很难说清它要检查什么，并且当你退后一步观察，会看到测试打断的频率可能远超平均水平。它如此挑剔，以至于无论任何变化都会造成输出与期望不同。

下例中，公司希望收集到销售人员如何实施演示的日志数据，记录哪个销售人员展示了哪页讲义，以及停顿时间等。在与中央服务器同步日志文件之前，我们转换日志文件的格式，对其进行预处理，使中央服务器更加容易切割日志文件并将数字转存到中央数据库。我们利用时间戳计算幻灯片持续时间。负责这种转换的对象叫做`LogFileTransformer`。

```java
public class LogFileTransformerTest {
	private String expectedOutput;
	private String logFile;
	@Before
	public void setUpBuildLogFile() {
		StringBuilder lines = new StringBuilder();
		appendTo(lines, "[2005-05-23 21:20:33] LAUNCHED");
		appendTo(lines, "[2005-05-23 21:20:33] session-id###SID");
		appendTo(lines, "[2005-05-23 21:20:33] user-id###UID");
		appendTo(lines, "[2005-05-23 21:20:33] presentation-id###PID");
		appendTo(lines, "[2005-05-23 21:20:35] screen1");
		appendTo(lines, "[2005-05-23 21:20:36] screen2");
		appendTo(lines, "[2005-05-23 21:21:36] screen3");
		appendTo(lines, "[2005-05-23 21:21:36] screen4");
		appendTo(lines, "[2005-05-23 21:22:00] screen5");
		appendTo(lines, "[2005-05-23 21:22:48] STOPPED");
		logFile = lines.toString();
	}

  @Before
	public void setUpBuildTransformedFile() {
		StringBuilder file = new StringBuilder();
		appendTo(file, "session-id###SID");
		appendTo(file, "presentation-id###PID");
		appendTo(file, "user-id###UID");
		appendTo(file, "started###2005-05-23 21:20:33");
		appendTo(file, "screen1###1");
		appendTo(file, "screen2###60");
		appendTo(file, "screen3###0");
		appendTo(file, "screen4###24");
		appendTo(file, "screen5###48");
		appendTo(file, "finished###2005-05-23 21:22:48");
		expectedOutput = file.toString();
	}
	
  @Test
	public void transformationGeneratesRightStuffIntoTheRightFile()
	throws Exception {
		TempFile input = TempFile.withSuffix(".src.log").append(logFile);
		TempFile output = TempFile.withSuffix(".dest.log");
		newLogFileTransformer().transform(input.file(), output.file());
		assertTrue("Destination file was not created", output.exists());
		assertEquals(expectedOutput, output.content());
	}
// rest omitted for clarity
}
```

第一个断言检查目标文件是否创建。第二个断言检查目标文件的内容符合期望。现在，第一个断言的价值值得商榷，而且很可能需要删除。但我们主要关注第二个断言——过度断言：	`assertEquals(expectedOutput, output.content());`

它精确地验证了测试名称所暗示的内容，这是个重要的断言。问题是这个测试太宽泛了，导致断言对整个日志文件进行大规模比较。它是一张厚厚的安全网，这毫无疑问，即使是输出中最微小的变化也会使断言失败。也正是这里存在问题。

永不失败的测试没有价值——它可能没有测试任何东西。而在另一个极端，总是失败的测试也很烦人。我们需要的是一个过去曾经失败的测试——证明它能够抓住与被测代码的预期行为之间的偏差——并且如果我们改动被测代码它就会再次失败。

本例中的测试太容易失败而变得脆弱，因此没能满足这条标准。但那只是更基本问题的症状——过度断言的问题。日志文件格式或内容的微小变化都是测试失败的原因。断言并无本质错误。问题在于测试违反了构成优秀测试的基本指导原则。

> **一个测试应该只有一个失败的原因。**



这个原则看起来眼熟，其实它是著名的面向对象设计原则的变体，也即单一职责原则(SRP)所说的“一个类应该有且只有一个改变的理由”。

解法：

当我们碰到过度细节的断言成瘾，第一步行动是识别无关细节并将其从测试中移除。本例中，你会查看要转换的日志文件，尝试减少行数。

我们希望它代表一份有效的日志文件，并且对于测试目的来说足够详尽。例如，日志文件具有对五个屏幕的计时。也许两三个就够了？我们能否只用一个？这个问题使我们考虑下一个改进：拆分这个测试。

问问自己，当不再测试x、y或z情况下，能够迅速通过测试所需的最小日志文件要多少行。这里x、y或z都是可以单独进行测试的主要候选者。下面展示一种可能的解决方案，将日志文件的每个方面及其转换都抽取到单独的测试。

```java
public class LogFileTransformerTest {
    private static final String END = "2005-05-23 21:21:37";
    private static final String START = "2005-05-23 21:20:33";
    privateLogFilelogFile;

    @Before
    public void prepareLogFile() {
    logFile = new LogFile(START, END);
    }
    
    @Test
    public void overallFileStructureIsCorrect() throws Exception {
        StringBuilder expected = new StringBuilder();
        appendTo(expected, "session-id###SID");
        appendTo(expected, "presentation-id###PID");
        appendTo(expected, "user-id###UID");
        appendTo(expected, "started###2005-05-23 21:20:33");
        appendTo(expected, "finished###2005-05-23 21:21:37");
        assertEquals(expected.toString(), transform(logFile.toString()));
    }

    @Test
    public void screenDurationsGoBetweenStartedAndFinished() throws Exception {
        logFile.addContent("[2005-05-23 21:20:35] screen1");
        String out = transform(logFile.toString());
        assertTrue(out.indexOf("started") <out.indexOf("screen1"));
        assertTrue(out.indexOf("screen1") <out.indexOf("finished"));
    }

    @Test
    public void screenDurationsAreRenderedInSeconds() throws Exception {
        logFile.addContent("[2005-05-23 21:20:35] screen1");
        logFile.addContent("[2005-05-23 21:20:35] screen2");
        logFile.addContent("[2005-05-23 21:21:36] screen3");
        String output = transform(logFile.toString());
        assertTrue(output.contains("screen1###0"));
        assertTrue(output.contains("screen2###61"));
        assertTrue(output.contains("screen3###1"));
    }

    // rest omitted for brevity
    private String transform(String log) { ... }
    private void appendTo(StringBuilder buffer, String string) { ... }
    private class LogFile { ... }
}
```

### 位操作的断言

位(bit)操作是计算机的底层运算符，抽象层次低从而显得如此模糊，以至于其意图和意义对读者来说是个谜。

```java
public class PlatformTest {
    @Test
    public void platformBitLength() {
    assertTrue(Platform.IS_32_BIT ^ Platform.IS_64_BIT);
    }
}
```

这里我们要检查什么？断言包含了描述，它断言两个**布尔**值的“^位运算”结果为**真**。位运算符做了什么？什么时候断言会失败？

如果你恰好工作在一个相对底层的领域，经常与位运算打交道，你会很快推断出我们在进行一次XOR(异或)运算，意味着如果位运算符两边的**布尔**值相同时，断言就会失败。

位运算符正是测试的问题所在。位运算符是用于位和字节运算的强大语言特性。但在这个测试的上下文中，我们并非处于位和字节的领域。我们处于“我们应该运行在32位或64位架构”的领域，而精髓隐藏在无关的位运算符背后。

那个特殊的位运算符完全适合去有效地执行一个简洁的断言，但我们并不是在优化测试断言。我要确保我们正确地构建正确的事情——我们的代码做我们期望的事情，同时我们的期望是有道理的。出于这个目的，必然有一个更好的方式来表达我们的意图，而不是位运算符。



解法：

用一个或多个布尔运算符来替换位运算符，清晰地依次表达期望。

```java
public class PlatformTest {
    @Test
    public void platformBitLength() {
        assertTrue("Not 32 or 64-bit platform?",
        Platform.IS_32_BIT || Platform.IS_64_BIT);
        assertFalse("Can’t be 32 and 64-bit at the same time.",
        Platform.IS_32_BIT && Platform.IS_64_BIT);
    }
}
```

它变得更加冗长，但断言的表述也更加明确。在这种情况下，纯英文消息非常有助于理解。

### 次要的细节

代码可读性源于快速和忠实地向读者揭示意图、目的和意义。当程序员扫视一段代码，他们是在寻找牛肉，而不想要酱汁。有时我们的测试代码充满了太多的酱汁。

JRuby有个模块叫ObjectSpace，令你的程序访问到所有运行期生存的对象。例如，你可以遍历某种类型的所有对象。下列代码表示了ObjectSpace的一个测试，用于确保按类型查找的代码能正常工作。你能迅速说出这个测试在做什么吗？

```java
public class TestObjectSpace {
    private Ruby runtime;
    privateObjectSpaceobjectSpace;
 
    @Before
    public void setUp() throws Exception {
        runtime = Ruby.newInstance();
        objectSpace = new ObjectSpace();
    }
 
    @Test
    public void testObjectSpace() {
        IRubyObject o1 = runtime.newFixnum(10);
        IRubyObject o2 = runtime.newFixnum(20);
        IRubyObject o3 = runtime.newFixnum(30);
        IRubyObject o4 = runtime.newString("hello");
        objectSpace.add(o1);
        objectSpace.add(o2);
        objectSpace.add(o3);
        objectSpace.add(o4);
        List storedFixnums = new ArrayList(3);
        storedFixnums.add(o1);
        storedFixnums.add(o2);
        storedFixnums.add(o3);
        Iterator strings = objectSpace.iterator(runtime.getString());
        assertSame(o4, strings.next());
        assertNull(strings.next());
        Iterator numerics = objectSpace.iterator(runtime.getNumeric());
        for (inti = 0; i< 3; i++) {
            Object item = numerics.next();
            assertTrue(storedFixnums.contains(item));
        }
        assertNull(numerics.next());
    }
}
```

我们在测试`ObjectSpace`的行为，我们首先向其中“添加”某种类型的对象，然后请求该类型的迭代器(Iterator)，检查是否拿回了那些对象。但人脑一次就只能应付这么多概念，而跟踪所有那些列表和迭代器就实在太多了。

解法：

测试的本质应当是开门见山，这可以归结为几个简单的准则：

1. 将不必要的安装(setup)抽取到私有方法或安装部分。

2. 起个恰当的、描述性的名字。

3. 一个方法内尽量只有一个抽象层次。

首先，我们改为在安装方法中创建那4个对象，将局部变量转为测试类的字段。毕竟，他们只是测试的道具。其次，由于测试对两类对象区别对待——无论是“Fixnums”还是“Strings”——我们应当照此命名以凸显类型。

```java
public class TestObjectSpace {
    ...
    privateIRubyObject string;
    private List<IRubyObject>fixnums;
    
    @Before
    public void setUp() throws Exception {
        ...
        string = runtime.newString("hello");
        fixnums = new ArrayList<IRubyObject>() {{
        add(runtime.newFixnum(10));
        add(runtime.newFixnum(20));
        add(runtime.newFixnum(30));
        }};
    }
    ...
}
```

现在我们将重点移到测试方法本身，以及第三条准则：一个方法内尽量只有一个抽象层次。说到抽象，在这个测试中我们关心的是，一旦我们向ObjectSpace中添加了某些对象，可以精确地通过各自的迭代器找到那些对象。清单4.12中显示了可读性方面的明显改进，仅仅提供单一的抽象层次。

```java
public class TestObjectSpace {
    private Ruby runtime;
    privateObjectSpace space;
    privateIRubyObject string;
    private List<IRubyObject>fixnums;

    @Before
    public void setUp() throws Exception {
        runtime = Ruby.newInstance();
        space = new ObjectSpace();
        string = runtime.newString("hello");
        fixnums = new ArrayList<IRubyObject>() {{
        add(runtime.newFixnum(10));
        add(runtime.newFixnum(20));
        add(runtime.newFixnum(30));
        }};
    }
    
    @Test
    public void testObjectSpace() {
        addTo(space, string);
        addTo(space, fixnums);
        Iterator strings = space.iterator(runtime.getString());
        assertContainsExactly(strings, string);
        Iterator numerics = space.iterator(runtime.getNumeric());
        assertContainsExactly(numerics, fixnums);
    }
    
    private void addTo(ObjectSpace space, Object... values) { }
    private void addTo(ObjectSpace space, List values) { }
    private void assertContainsExactly(Iterator i, Object... values) { }
    private void assertContainsExactly(Iterator i, List values) { }
}
```

这已经好很多了；随着所有安装和细节从测试方法本身移出去，主要部分显露了出来。另外，你也可以试着给夹具对象起个更具描述性的名字，而不必将它们移到安装方法，看看是否那样就已经足够修复附加细节的坏味道。毕竟，只有一个测试用到那些夹具对象。



### 人格分裂

当测试显现出人格分裂时，它认为它本身体现了多个测试。那是不对的。一个测试应当仅检查一件事并妥善执行。

下面的测试类针对一些命令行接口。它其实是用不同的命令行参数来测试Configuration对象的行为。

````java
public class TestConfiguration {
    @Test
    public void testParsingCommandLineArguments() {
        String[] args = { "-f", "hello.txt", "-v", "--version" };
        Configuration c = new Configuration();
        c.processArguments(args);
        assertEquals("hello.txt", c.getFileName());
        assertFalse(c.isDebuggingEnabled());
        assertFalse(c.isWarningsEnabled());
        assertTrue(c.isVerbose());
        assertTrue(c.shouldShowVersion());
        c = new Configuration();
        try {
            c.processArguments(new String[] {"-f"});
            fail("Should've failed");
        } catch (InvalidArgumentException expected) {
            // this is okay and expected
        }
    }
}
````

该测试用例的多重人格体现在它涉及了文件名，调试、警告、信息开关、版本号显示，还处理了空的命令行参数列表。注意这个测试没有遵循**安排-执行-断言**(arrange-act-assert)结构。

解法：



## 单元测试代码的可维护性

### 重复

重复是代码的万恶之源。

重复是存在多份拷贝，或对单一概念的多次表达——这都是不必要的重复。

```java
public class TemplateTest {
    @Test
    public void emptyTemplate() throws Exception {
         assertEquals("", new Template("").evaluate());
}
   @Test
   public void plainTextTemplate() throws Exception {
      assertEquals("plaintext", new Template("plaintext").evaluate());
   }
}
```

是最常见的文本字符串重复，在两个断言中，空串和plaintest字符串都出现了两次，我们叫这种重复为文字重复 (Literal Duplication)。你可以通过定义局部变量来移除它们。在上面的测试类中还存在另一种重复，也许比显而易见的字符串重复有趣得多。当我们提取那些局部变量时，这种重复会变得更加清晰。

```java
public class TemplateTest {
   @Test
   public void emptyTemplate() throws Exception {
      String template = "";
      assertEquals(template, new Template(template).evaluate());
}
   @Test
   public void plainTextTemplate() throws Exception {
      String template = "plaintext";
      assertEquals(template, new Template(template).evaluate());
   }
}
```

你会发现在那两个测试函数中，只有字符串是不同的。将那些不同的部分提取为局部变量以后，剩下的断言何止是相似，简直一模一样。它们都实例化了一个Template对象，调用了evaluate函数对模板求值，并断言此函数返回相应的字符串。这种操作不同数据的重复逻辑，我们叫做结构重复 (Structural Duplication)。以上的两个代码块用一致的结构操作了不同的数据。让我们去掉这种重复，并看看代码会变成什么样。

```java
public class TemplateTest {
   @Test
   public void emptyTemplate() throws Exception {
      assertTemplateRendersAsItself("");
}
   @Test
   public void plainTextTemplate() throws Exception {
      assertTemplateRendersAsItself("plaintext");
   }
   private void assertTemplateRendersAsItself(String template) {
      assertEquals(template, new Template(template).evaluate());
} }
```



文字重复或结构重复，长得一样，很容易看出来，可以通过提炼相似的结构来清晰地识别文字重复 (Literal Duplication) 和结构重复 (Structural Duplication)，但语义重复 (Semantic Duplication) ——用看起来并不相同的表达和实现完成相同功能或概念——很难看出来。

```java
@Test
public void groupShouldContainTwoSupervisors() {
      List<Employee> all = group.list();
      List<Employee> employees = new ArrayList<Employee>(all);
      Iterator<Employee> i = employees.iterator();
      while (i.hasNext()) {
         Employee employee = i.next();
         if (!employee.isSupervisor()) {
            i.remove();
         }
      }
      assertEquals(2, employees.size());
}

@Test
public void groupShouldContainFiveNewcomers() {
      List<Employee> newcomers = new ArrayList<Employee>();
      for (Employee employee : group.list()) {
         DateTime oneYearAgo = DateTime.now().minusYears(1);
         if (employee.startingDate().isAfter(oneYearAgo)) {
            newcomers.add(employee);
         }
      }
      assertEquals(5, newcomers.size());
}
```

除了通过group.list()方法查询雇员的完整列表之外，这两个方法没有太多的文字重复，但是它们又很相似，都是查询雇员的完整列表并通过某些条件进行过滤。



解法：

第一步，通过改变过滤方法，使之成为结构重复。第二步，再通过提炼变量和方法，消除结构重复。

### 条件逻辑

条件执行语句使代码难以理解。你应该在测试方法中避免条件执行结构，例如if、else、for、 while和switch等。当测试要检查的行为比较复杂时，这一点尤其重要。这些语言结构固然有用，但对于测试代码，你应该小心行事。为了测试的可维护性，你需要保持测试的复杂度低于对应的解决方案。



以下测试用例创建了Dictionary（字典）对象，用数据填充它，并验证请求到的Iterator (迭代器)中内容是正确的。

```java
public class DictionaryTest {
	@Test
	public void returnsAnIteratorForContents() throws Exception {
		Dictionary dict = new Dictionary();
		dict.add("A", new Long(3));
		dict.add("B", "21");  

		for (Iterator e = dict.iterator(); e.hasNext();) {
			Map.Entry entry = (Map.Entry) e.next();
			if ("A".equals(entry.getKey())) {
				assertEquals(3L, entry.getValue());
			}
			if ("B".equals(entry.getKey())) {
				assertEquals("21", entry.getValue());
			} 
		}
	}
}
```



测试实际上期望能在Iterator中找到填充的那两个条目。在修改测试时，比方说通过重构来改变Dictionary类的API，我们的目标是使意图明确和减少出错几率。对于测试代码中的全部条件执行语句，我们很容易认为某个断言已经通过但实际上却没有运行过。这是我们要避免的。



解法：

这种情况在处理过度复杂的代码时经常遇到，第一步就是简化它，通常是将一段代码提炼到独立且命名良好的方法中。

```java
@Test
public void returnsAnIteratorForContents() throws Exception {
    Dictionary dict = new Dictionary();
    dict.add("A", new Long(3));
    dict.add("B", "21");
    assertContains(dict.iterator(), "A", 3L);
    assertContains(dict.iterator(), "B", "21");
    }
private void assertContains(Iterator i, Object key, Object value) {
    while (i.hasNext()) {
      Map.Entry entry = (Map.Entry) i.next();
      if (key.equals(entry.getKey())) {
        assertEquals(value, entry.getValue());
        return; 
      }
	}
  fail("Iterator didn't contain " + key + " => " + value);
}
```

提炼出的方法中仍然存在循环，但是测试的意图变得清晰，使得可维护性大不相同了。

而且还更正了原先测试中的另一个问题：即使Iterator是空的，测试也不会失败。我们是这样修改的，当找到预期条目就使断言返回，而如果没找到符合的条目就使测试失败。最后的fail()调用很容易被漏掉，导致测试虽然通过但是代码却不工作，令人头痛。长点儿心吧！

### 脆弱的测试

修改代码导致了测试失败——那是件好事情，测试显然注意到了你认为重要而需要检查的变更——这不同于一直失败的测试。我们说的是一直亮了两周红灯的测试，而且没人去修复它。那个测试大概会继续再失败两周或者两个月，直到有人修复它，或者更有可能的是，删掉它。这都在情理之中，因为一直失败的测试几乎是没用的。你无法从它那里得知任何你还不知道的事情，或者可以信任的事情。

还有一种失败的测试带给我们的生活不必要的麻烦：断断续续失败的**脆弱测试**。脆弱的意思就像**喊“狼来了”**，也就是说，虽然测试使构建失败了，但是你去看的时候，再次执行却又通过了，刚才似乎只是个意外。

大多数情况下，脆弱测试失败时都涉及了线程和竞态条件，其行为依赖于日期或时间，或测试依赖于计算机的性能，因而在运行期间受到I/O速度或CPU负载的影响。访问网络资源的测试有时也会表现得脆弱，比如当网络或资源临时不可用的时候。例子展示了一个我不止一次碰到的问题：对文件系统时间戳的错误假设。

```java
@Test
public void logsAreOrderedByTimestamp() throws Exception {
   generateLogFile(logDir, "app-2.log", "one");
   generateLogFile(logDir, "app-1.log", "two");
   generateLogFile(logDir, "app-0.log", "three");
   generateLogFile(logDir, "app.log", "four");
   Log aggregate = AggregateLogs.collectAll(logDir);
   assertEquals(asList("one", "two", "three", "four"),
          aggregate.asList());
}
```

问题在于，被测逻辑依赖于文件的时间戳。我们期望日志文件按照时间戳顺序进行汇总，将最早修改的文件作为最旧的日志文件，以此类推，并没有按照文件名的字母顺序对日志文件进行排序。结果让我们失望了。这个测试在Linux上时不时地失败，而在Mac OS X上几乎总是失败。我们看看测试中用来写日志文件的generateLogFile()方法：

```java
private void generateLogFile(final File dir, final String name,
         final String... messages) {
   File file = new File(dir, name);
   for (String message : messages) {
      IO.appendToFile(file, message);
   }
}
```

我们栽在了忘记给创建日志文件的被测方法传递时间。具体来说，我们忘记了在不同的计算机和平台上时间流逝得不一样。某些平台，比如Mac OS X，文件系统时间戳每秒跳动一次，也就是说除非文件生成时间足够长，或者文件生成地恰到好处，否则下一次generateLogFile()调用极有可能导致日志文件带有相同的时间戳。

当你的测试遇到时间相关的问题时，你应该先通过适当的API来模拟相应的情况。对于用正确时间戳来产生日志文件这个例子，你可以依靠Java的File API，用setLastModified明确地递增每个日志文件的时间戳。清单5.7显示了修改后的工具方法。

```java
private AtomicLong timestamp = new AtomicLong(currentTimeMillis());
private void generateLogFile(final File dir, final String name, final String... messages) {
   File file = new File(dir, name);
   for (String message : messages) {
			IO.appendToFile(file, message);
   } 
   file.setLastModified(timestamp.addAndGet(TEN_SECONDS));
}
```

这样我们就有效地确保每个日志文件都收到了不同的时间戳。测试能够可靠地工作于所有平台和计算机，而不需要再缓慢地执行测试了。

对于间歇性的测试失败，时间戳并非唯一的麻烦来源。最突出的是多线程，它往往会使事情复杂化。涉及随机数生成的也好不到哪儿去。无论我们是否提早意识到要针对随机测试失败来保护自己，我们解决这些情况的方法都是一样的：

1. 规避它。

2. 控制它。

3. 隔离它。

最简单的办法就是彻底地规避这个问题，切断任何不确定行为的来源。例如，我们可以通过文件名的数字后缀而非时间戳来对日志文件排序。如果不能轻易地绕过棘手的部分，我们就试图控制它。例如，可以采用Fake(伪造对象)来代替随机数生成器，令其精确地返回我们需要的值。最后，如果找不到一个方法来充分地规避或控制这个问题的源头，我们的最后一招就是将难题隔离在尽可能小的范围内。这就将大多数代码从不确定行为中解脱出来，从而在一处而且仅仅一处来解决难题。

### 残缺的文件路径

**残缺的文件路径**会使你无法转移代码，除了自己的电脑之外，在任何其他人的电脑上都无法运行。我们都知道这个问题。假如你从没读过代码，却天生就懂得说“我们不应该在这里硬编码这个路径”，或者天生就知道明确地引用文件系统某个位置会自寻烦恼，那就怪了。下面的测试用例，它采用绝对路径从文件系统读出一份文档。

```java 
public class XmlProductCatalogTest {
  private ProductCatalog catalog;
  @Before
  public void createProductCatalog() throws Exception {
    File catalogFile = new File("C:\\workspace\\catalog.xml");
    catalog = new XmlProductCatalog(parseXmlFrom(catalogFile));
  }
  @Test
  public void countsNumberOfProducts() throws Exception {
    assertEquals(2, catalog.numberOfProducts());
  }
  // remaining tests omitted for brevity
}
```

文件路径有什么问题？首先，绝对文件路径明显地绑定了Microsoft Windows操作系统。开发者要是在Mac或Linux系统上运行测试的话，马上会得到“找不到文件(file not found)”之类的I/O错误。这段测试代码不是自包含的(self-contained)。你需要从版本控制系统中检出代码，然后马上就看到所有测试都通过。用了这种特定于平台的绝对文件路径的话，想都别想了。

解法：

我们现在有一个仅限于Windows的绝对文件路径。它不能在任何Mac或Linux系统上工作。除非我们全都是Windows用户，否则我们至少应该将路径改为与平台无关，比如Java的File API支持这样做：

```java
new File("/workspace/catalog.xml");
```



在Windows和UNIX 两个平台家族中，它都**能**胜任，比如你使用Windows上的C盘，那么路径就是`C:\workspace`，而在UNIX系统上路径就是`/workspace`。我们仍然被绑定在workspace这个特定位置上。这不像绑定特定用户名那么糟糕（比如：`/User/lasse`），但其实仍会强迫所有开发者都将自己的工作区(workspace)放在相同的物理位置上。

尽量采用抽象引用，比如系统属性(system properties)、环境变量，或任何能获得的相对路径。不建议像System.getProperties("user.home")这样来选择相对路径，虽然在所有平台上都会返回一个有效的路径，但是那些引用里面仍然是绝对路径，仍会强迫开发者们将其工作区放在指定位置。尤其当你开发新版本同时还要维护旧版本，这种情况就非常棘手。每当你从新开发工作切换到旧分支改Bug时，你不得不在未完成工作的基础上检出旧分支，因为绝对路径永远指向同一个位置。总是尽可能去引用相对路径，例如：

```java
new File("./src/test/data/catalog.xml");
```



### 永久的临时文件

临时文件应该是暂时性的，使用后即抛弃和删除。**永久的临时文件**这个测试坏味道，指的是临时文件并非暂存而是被保留了下来，也就是说在下个测试运行前它都不会被删除。

俗话说，“YY是失败之母”，而程序员往往就假设临时文件是暂时性的。这会带来一些意外，而那正是我们试图避免去调试的行为。事实上，你应该尝试根本不使用文件，因为它根本不是你要测试的对象。

想象针对一个基于文件的产品登记册(Product Catalog)对象的一系列测试。第一个测试会创建一个空的临时文件，不包含任何条目，然后用空文件初始化登记册，并检查登记册如期为空。第二个测试不创建文件，而是基于一个缺失的文件来初始化登记册，登记册这时应该表现得就好像那个文件存在似的，但只是个空文件。第三个测试又创建一个临时文件——这次包含了两个产品——用该文件来初始化登记册，然后检查登记册确实是包含了那两个产品。

```java
public class XmlProductCatalogTest {
   private File tempfile;
   @Before
   public void prepareTemporaryFile() {
      String tmpDir = System.getProperty("java.io.tmpdir");
      tempfile = new File(tmpdir, "catalog.xml");
   }

  @Test
  public void initializedWithEmptyCatalog() throws Exception {
     populateCatalogWithProducts(0);
     ProductCatalog catalog = new XmlProductCatalog(tempfile);
     assertEquals(0, catalog.numberOfProducts());
  }
  @Test
  public void initializedWithMissingCatalog() throws Exception {
     ProductCatalog catalog = new XmlProductCatalog(tempfile);
     assertEquals(0, catalog.numberOfProducts());
  }

  @Test
     public void initializedWithPopulatedCatalog() throws Exception {
        populateCatalogWithProducts(2);
        ProductCatalog catalog = new XmlProductCatalog(tempfile);
        assertEquals(2, catalog.numberOfProducts());
  } 
}
```

当JUnit开始执行这个测试类时，会在每个测试之前用新的File 对象来初始化私有字段tempfile。没问题。问题在于第二个测试，当 initializedWithMissingCatalog 开始执行时，系统的临时目录中已经存在一个空的catalog.xml 文件，这与测试的假设大相径庭。这意味着无论XmlProductCatalog 是否正确地处理了文件缺失，测试都能通过。不妙。

解法：

一般来说，能不用物理文件就尽量不用；比起操作字符串或其它类型的内存块来说，文件I/O 会使得测试变慢。说到这，当你处理测试中的临时文件时，你需要记住临时文件并不是随时保持临时性的。例如，如果你用`File#createTempFile` API获得一个临时文件，文件就和你手中的其它文件引用一样，不再是暂时性的了。除非你明确地删除它，否则文件在测试结束后哪也不会去。相反，它就是一个待在原地的幽灵文件，有可能造成看似不稳定的测试失败问题。

于是针对临时文件的处理，我们归纳出几条简单的指导方针：

- 在@Before中删除文件。

- 如果可能的话，使用唯一的临时文件名。

- 要弄清楚文件是否应该存在。

关于第一条，程序员有时会用 File#deleteOnExit 将临时文件标记为删除，然后快乐地继续工作。那样做是不够的，因为文件在JVM进程退出之前并没有真正地被删除。相反，你需要在每个测试方法使用文件**之前**就删除它们，咱们就给setup增加一条语句：

```java
@Before
public void prepareTemporaryFile() {
   String tmpDir = System.getProperty("java.io.tmpdir");
   tempfile = new File(tmpdir, "catalog.xml");
   tempfile.delete();
}
```

其次，当文件名和位置不需要那么精确的时候，推荐使用File#createTempFile。你能够确保在每次测试时得到唯一的文件名，这样你就不必担心从另一个测试继承来一个幽灵文件。本例中setup方法将修改如下：

```java
@Before
public void prepareTemporaryFile() {
  tempfile = File.createTempFile("catalog", ".xml");
}
```

再者，当你不关心文件系统中是否存在物理文件时，默不作声即可。但当你在意时，你就应该明确表达出来。

```java
@Test
public void initializedWithMissingCatalog() throws Exception {
    withMissingFile(tempfile);
    ProductCatalog catalog = new XmlProductCatalog(tempfile);
    assertEquals(0, catalog.numberOfProducts());
}
private void withMissingFile(File file) {
    file.delete();
}
```

明确对执行环境的期望，除了能确保缺失的文件不存在以外，还有助于表达测试的意图。



### 沉睡的蜗牛

缓慢的测试是影响可维护性的主要障碍，因为不论是添加新功能还是修复问题，程序员在修改代码时都需要反复地运行测试。文件I/O并非缓慢测试的唯一来源。通常对测试套件执行时间带来的更大冲击是源自大量的Thread#sleep 调用，它允许其他线程先完成工作，然后再对预期结果和副作用进行断言。沉睡的蜗牛非常容易发现：查找Thread#sleep调用以及留意异常缓慢的测试。

例子展示了对Counter对象的测试，该对象负责产生唯一的数字。显然，即使多个线程同时调用Counter，我们也希望这些数字是独一无二的。

```java
@Test
public void concurrentAccessFromMultipleThreads() throws Exception {
  final Counter counter = new Counter();
  final int callsPerThread = 100;
  final Set<Long> values = new HashSet<Long>();
  Runnable runnable = new Runnable() {
     @Override
     public void run() {
        for (int i = 0; i < callsPerThread; i++) {
           values.add(counter.getAndIncrement());
    } }
  };
  int threads = 10; 
  for(inti=0;i<threads;i++){
     new Thread(runnable).start();
  }
  Thread.sleep(500);
  int expectedNoOfValues = threads * callsPerThread;
  assertEquals(expectedNoOfValues, values.size());
}
```

测试所做的工作，先是建立几个将要重复地访问计数器的线程，然后启动所有线程。由于测试无法预知线程是如何被调度的，以及何时会结束，于是我们调用了Thread#sleep，令测试在每次断言之前先等待半秒钟。

尽管500毫秒听起来有点夸张，但其实并不是的。就算睡上(sleep)300毫秒，在我的电脑上测试大约也会有10%的几率失败——那正是我们讲过的**脆弱的测试**！现在，500毫秒的sleep仍然不可靠；有时需要更久的时间才能让全部线程完成工作。一旦是因为500毫秒的sleep还不够久而导致了测试失败，程序员就会延长sleep。毕竟，我们不喜欢脆弱的测试。还有另一个问题：它相当地慢。 尽管500毫秒听起来不多，但是积少成多啊，尤其是当测试代码中遍布着许多Thread#sleep的时候。

解法：

替换掉了奢侈和不可靠的Thread#sleep。最根本的不同在于我们使用了java.util.concurrent包中的同步对象，使得测试能够立即知晓工作线程的任务结束了。

```java
@Test
public void concurrentAccessFromMultipleThreads() throws Exception {
  final Counter counter = new Counter();
  final int numberOfThreads = 10;
  final CountDownLatch allThreadsComplete = new CountDownLatch(numberOfThreads);
  final int callsPerThread = 100;
  final Set<Long> values = new HashSet<Long>();
  Runnable runnable = new Runnable() {
    @Override
    public void run() {
      for (int i = 0; i < callsPerThread; i++) {
          values.add(counter.getAndIncrement());
      }
      allThreadsComplete.countDown();
      }
  };
  for (int i = 0; i < numberOfThreads; i++) {
     new Thread(runnable).start();
  }
  allThreadsComplete.await(10, TimeUnit.SECONDS);
  int expectedNoOfValues = numberOfThreads * callsPerThread;
  assertEquals(expectedNoOfValues, values.size());
}
```

该方案基于一个简单的想法，即每个工作线程结束后应当通知测试线程，从而解决了这个坏味道。只要测试线程得到这个信息，测试就可以立即继续自己的工作，基本上避免了随意添加Thread#sleep带来的不必要等待。方案建立在Java API中的标准同步对象`CountDownLatch`上。CountDownLatch有效地确保仅当指定的线程数量触发了锁才能执行测试。如果你的系统要处理线程，那么给自己一次机会，投入工具类`java.util.concurrent`包的怀抱吧。

### 像素级完美

**像素完美**是基本断言和魔法数字的特例。把它放在这个坏味道分类中，是因为我觉得它在计算机绘图领域频繁出现，那里的人们经常会说在测试中遇到困难。坏味道出现的场景包括，测试要求期望和实际产生的图像完美地匹配，或者期望在产生的图像中，指定坐标处的像素包含某种颜色。这样的测试是出了名的脆弱，通常很小的输入变化都会导致测试失败。

例子允许用户操作屏幕上的图表，在画布上拖放长方形并用各种线段连接起来。对象图所产生的图表，其中包含各种长方形和线段。

```java
public class RenderTest {
  @Test
  public void boxesAreConnectedWithALine() throws Exception {
    Box box1 = new Box(20, 20);
    Box box2 = new Box(20, 20);
    box1.connectTo(box2);
    Diagram diagram = new Diagram();
    diagram.add(box1, new Point(10, 10));
    diagram.add(box2, new Point(40, 20));
    BufferedImage image = render(diagram);
    assertThat(colorAt(image, 19, 12), is(WHITE));
    assertThat(colorAt(image, 19, 13), is(WHITE));
    assertThat(colorAt(image, 20, 13), is(BLACK));
    assertThat(colorAt(image, 21, 13), is(BLACK));
    assertThat(colorAt(image, 22, 14), is(BLACK));
    assertThat(colorAt(image, 23, 14), is(BLACK));
    assertThat(colorAt(image, 24, 15), is(BLACK));
    assertThat(colorAt(image, 25, 15), is(BLACK));
    assertThat(colorAt(image, 26, 15), is(BLACK));
    assertThat(colorAt(image, 27, 16), is(BLACK));
    assertThat(colorAt(image, 28, 16), is(BLACK));
    assertThat(colorAt(image, 29, 17), is(BLACK));
    assertThat(colorAt(image, 30, 17), is(BLACK));
    assertThat(colorAt(image, 31, 17), is(WHITE));
    assertThat(colorAt(image, 31, 18), is(WHITE));
  } 
}
```

首先，测试难以验证。我是说，我怎么知道**(23, 14)**处应该有一个黑色像素？此外，不难看出这个测试是多么容易打破。任何图表边界宽度的变化，例如线段的精确位置稍有偏移，测试就会打破。这是不好的，因为测试检查的是图表中两个相连的长方形是否也可见地连在一起，而长方形的**样子**不该影响测试。

如果我们测试长方形的总渲染宽度，我们可以用魔法数字来对付这个问题，用类似boxContentWidth + (2 * boxBorderWidth)的变量组成断言，来包容边界宽度的变化。但是对于两个长方形之间的线段，我们的做法没有那么显而易见。



解法：

用适当的抽象层次来表达测试，这是编写它们的目标之一。如果你对两个长方形相互连接感兴趣，你不该考虑像素或坐标。相反，你应该考虑**长方形**，以及它们是否**相连**。简单吗？这就是说，不要用**基本断言**，而是应该将背后的细枝末节隐藏到自定义断言中，那里才是适合的抽象层次。同时也意味着，相对于精确的值与值比较，断言中反而需要进行**模糊**匹配——一种实际的算法。

将检查两个长方形是否连接的细节委托给自定义断言。自定义断言会操心如何检查（最好是以灵活和健壮的方式）。

```java
public class RenderTest {
   private Diagram diagram;
   @Test
   public void boxesAreConnectedWithALine() throws Exception {
      Box box1 = new Box(20, 20);
      Box box2 = new Box(20, 20);
      box1.connectTo(box2);
      diagram = new Diagram();
      diagram.add(box1, new Point(10, 10));
      diagram.add(box2, new Point(40, 20));
      assertThat(render(diagram), hasConnectingLineBetween(box1, box2));
   }
   private Matcher<BufferedImage> hasConnectingLineBetween(
         final Box box1, final Box box2) {
      return new BoxConnectorMatcher(diagram, box1, box2);
   }
   // rest of details omitted for now
}

```

主要区别在于，将大量看似任意坐标上的像素颜色测试替换掉，代之以口语化的自定义断言——在两个长方形之间应该有一条连接线段。可读性变得好多了，而且这个特殊的测试需要的维护工作更少。然而，复杂性没有完全消除掉。我们仍然需要告诉计算机如何辨别两个长方形是否相连，而那正是神秘的BoxConnectorMatcher的职责。BoxConnectorMatcher的实现细节看似与要介绍的这种测试坏味道没啥关系。毕竟，这是一个特殊情况的方案。但是因为许多程序员似乎都对图形相关代码的可测性失去了信心，因此我觉得仔细看看其实现还是有必要的。

接下来，用自定义的匹配器来检查两个长方形之间的连接。即使长方形的相关位置变动了一个像素，这种智能断言也不会被打破。

```java
public class BoxConnectorMatcher extends BaseMatcher<BufferedImage> {
   private final Diagram diagram;
   private final Box box1;
   private final Box box2;
   BoxConnectorMatcher(Diagram diagram, Box box1, Box box2) {
      this.diagram = diagram;
      this.box1 = box1;
      this.box2 = box2;
   }
   @Override
   public boolean matches(Object o) {
      BufferedImage image = (BufferedImage) o;
      Point start = findEdgePointFor(box1);
      Point end = findEdgePointFor(box2);
      return new PathAlgorithm(image).areConnected(start, end);
}
   private Point findEdgePointFor(final Box box1) {
      Point a = diagram.positionOf(box1);
      int x = a.x + (box1.width() / 2);
      int y = a.y - (box1.height() / 2);
      return new Point(x, y);
   }
   @Override
   public void describeTo(Description d) {
      d.appendText("connecting line exists between "
            + box1 + " and " + box2);
   } 
}
```

BoxConnectorMatcher的精华都在matches()方法中，首先分别找出两个长方形的任意一个边缘点，然后委托给PathAlgorithm (路径算法)对象，来判定两个坐标是否在图中相连。

接下来是PathAlgorithm的实现。警告：这是一段冗长的代码，我不打算细致地解释。

```java
public class PathAlgorithm {
   private final BufferedImage img;
   private Set<Point> visited;
   private int lineColor;
   public PathAlgorithm(BufferedImage image) {
      this.img = image;
	 }
   public boolean areConnected(Point start, Point end) {
      visited = new HashSet<Point>();
      lineColor = img.getRGB(start.x, start.y);
      return areSomehowConnected(start, end);
   }
   private boolean areSomehowConnected(Point start, Point end) {
      visited.add(start);
      if (areDirectlyConnected(start, end)) return true;
      for (Point next : unvisitedNeighborsOf(start)) {
         if (areSomehowConnected(next, end)) return true;
      }
     return false;
   }
   private List<Point> unvisitedNeighborsOf(Point p) {
     List<Point> neighbors = new ArrayList<Point>();
     for (int xDiff = -1; xDiff <= 1; xDiff++) {
       for (int yDiff = -1; yDiff <= 1; yDiff++) {
         Point neighbor = new Point(p.x + xDiff, p.y + yDiff);
         if (!isWithinImageBoundary(neighbor)) continue;
         int pixel = img.getRGB(neighbor.x, neighbor.y);
         if (pixel == lineColor && !visited.contains(neighbor)) {
           neighbors.add(neighbor);
         }
       } 
     }
     return neighbors;
   }
   private boolean isWithinImageBoundary(Point p) {
     if (p.x < 0 || p.y < 0)  return false;
     if (p.x >= img.getWidth()) return false;
     if (p.y >= img.getHeight()) return false;
     return true;
  }
  private boolean areDirectlyConnected(Point start, Point end) {
    int xDistance = abs(start.x - end.x);
    int yDistance = abs(start.y - end.y);
    return xDistance <= 1 && yDistance <= 1;
  }
}
```

PathAlgorithm的实现其实就是深度优先搜索，从起始坐标开始，遍历每个颜色符合的相邻坐标，直到走完所有相连的点，或者遇到与终止坐标直接相邻的点。

这种测试极其脆弱，因为即使很小的和不相关的输入变化——是否是另一个图像，或图形对象的渲染方式——都足以影响输出、打破测试，谁让你非要精确地检查像素坐标和颜色来着。同样的问题在采用**golden master**（捕获并保存当前的处理结果，未来的运行结果将会与保存的结果进行对比，从而发现预期之外的变更。又称Approve Test或标杆比对测试法）技术时也会遇到，其做法是事先将图像录制下来，并手工检查其正确性，以后再进行测试时就将渲染出的图像与之进行比对。

在上述方案中，我们可以在适当抽象层次上针对表达意图的图像输出来编写断言——针对高层概念而非像素坐标。但你也看到了，这种自定义断言实现起来并不容易。无论如何，考虑到像素完美的极度脆弱性时，这样做还是物有所值的。



### 参数化的混乱

先看参数化测试模式的典型起始点。

```java
public class RomanNumeralsTest {
  @Test
  public void one() {
    assertEquals("I", format(1));
  }
  @Test
  public void two() {
    assertEquals("II", format(2));
  }
  @Test
  public void four() {
    assertEquals("IV", format(4));
  }
  @Test
  public void five() {
    assertEquals("V", format(5));
  }
  @Test
  public void seven() {
    assertEquals("VII", format(7));
  }
  @Test
  public void nine() {
    assertEquals("IX", format(9));
  }
  @Test
  public void ten() {
    assertEquals("X", format(10));
  }
}
```

包含一堆只有一行的测试方法，仅仅是输入和期望输出值有所不同。被测代码是个工具类，负责将数字格式化为罗马数字。这种情形正是参数化测试模式的用武之地，其降低代码行数的手段主要是去除样板代码(boilerplate)。使用参数化测试模式和JUnit的Parameterized test runner(测试运行器)重写了相同的测试。

```java
@RunWith(Parameterized.class)
public class RomanNumeralsTest {
   private int number;
   private String numeral;
   public RomanNumeralsTest(int number, String numeral) {
      this.number = number;
      this.numeral = numeral;
	}
   @Parameters
   public static Collection<Object[]> data() {
      return asList(new Object[][] { { 1, "I" }, { 2, "II" },
            { 4, "IV" }, { 5, "V" }, { 7, "VII" },
            { 9, "IX" }, { 10, "X" } });
	}
	@Test
  public void formatsPositiveIntegers() {
    assertEquals(dataset(), numeral, format(number));
  }
  private String dataset() {
    StringBuilder s = new StringBuilder();
    s.append("Data set: [").append(this.number);
    s.append(", \"").append(this.numeral).append("\"].");
    return s.toString();
	}
}
```

可以用类似@Parameters 方法中的方式来组装一个失败消息，用于描述整个数据集，使得JUnit在匿名测试失败时能表达出该信息。

### 方法间缺乏内聚

内聚是构造良好的面向对象代码的主要属性。简而言之，内聚意味着一个类只代表单一**事物**，单一抽象。内聚是好事——我们希望高内聚——而**内聚**也是一种代码坏味道。本节标题中的“**方法间**”指的是，我们通过观察一个类中各方法之间的共性来确定内聚度。尽管有各种手段来计算方法内聚度，但常见公式的分母都是基于一个大概的想法，即完美内聚意味着一个类的每个字段都被每个方法所使用到。

代码是处理**拆分会计 (split accounting)**的，用于将某条目的成本分散到多个会计组中。例如，新的数据库服务器将被三个不同部门使用，因此你可以将其价格分拆到三个不同的预算中。代码用三个领域对象来处理拆分会计操作：Account、Split和BudgetCategory。操作本身用Transaction对象来表示。清单中的测试类在@Before方法中为这些领域对象初始化一个夹具(fixture)，然后测试类有几个测试方法工作在那些对象上。

```java
public class SplitsTest {
   Account account;
   Split split;
   BudgetCategory bc1, bc2, bc3, bc4;
	 // ...
   @Test
   public void fromSplits() throws Exception {
      List<TransactionSplit> fromSplits = new ArrayList<TransactionSplit>();
      fromSplits.add(createSplit(bc3, 1200));
      fromSplits.add(createSplit(bc4, 34));
      Transaction t = createTransaction(split, account);
      t.setFromSplits(fromSplits);
      assertTrue(transactions(t).size() == 1);

   }

   @Test
   public void toSplits() throws Exception {
      List<TransactionSplit> toSplits = new ArrayList<TransactionSplit>();
      toSplits.add(createSplit(bc1, 1200));
      toSplits.add(createSplit(bc2, 34));
      Transaction t = createTransaction(account, split);
      t.setToSplits(toSplits);
      assertTrue(transactions(t).size() == 1);
   }
}
```

坏味道是由于测试类的夹具包含了多个BudgetCategory 对象，然后测试方法只用到其中一部分。这明显与每个测试都工作在相同夹具上的想法背道而驰；各个测试方法缺乏内聚。从更大角度来看，一个过度复杂的夹具会使程序员更难理解发生了什么。更多的字段意味着更多变化的事物充斥着你的大脑。单看其中一个测试，其实也很难找出它用到了哪些字段，因为不够明显。我们反而留下疑问，“哪个category也是bc3来着？”这个问题源于一个基本的代码坏味道：糟糕的命名。尽管测试方法缺乏内聚并不会令你完全不能对夹具进行良好地命名，但往往会相当困难。

例如，每个测试都具有一个输入和一个输出，但由于每个测试都有这么一对儿对象，你就不得不按照emptyInput、 inputWithOneThing、inputWithThreeIdenticalThings等来命名。这样，你就不能简单地看着夹具说，“没错。这两个是一起的，而剩下的与这个测试无关。”



解法：

我们已经拿出了一个对策——将类分拆。此外，更好的典型方案是强制各测试使用相同的夹具对象。要将一个测试类分解为两个，我们需要识别哪部分夹具是所有测试共享的，哪些是个别测试独有的。

在该类其余的测试中，没有哪个用到两个以上的BudgetCategory 对象，而且bc1和bc2总是一起出现，要么bc3和bc4一起出现。你应该寻找这类群组，找出自然边界从而将类分拆成两个或三个更小、更专注的——内聚的——测试类。

有时你需要的就是创建另一个测试类，将某些方法和夹具对象搬移过去，然后把手擦干净。通常，测试依赖于相同的工具方法，而我们不喜欢重复。这时，我们也需要将那些工具方法移到单独的类中，如此一来，分拆后的测试类都可以使用它，或者提炼一个公共基类来提供这些工具。

```java
public class TestSplitsAcrossDifferentBudgetCategories extends AbstractSplitsTestCase {
   private BudgetCategory incomeCategory, standardCategory;
   @Before
   public void setup() throws Exception {
      incomeCategory = new BudgetCategoryImpl();
      incomeCategory.setIncome(true);
      standardCategory = new BudgetCategoryImpl();
      standardCategory.setIncome(false);
	}
  @Override
  protected List<BudgetCategory> budgetCategories() {
    return asList(incomeCategory, standardCategory);
  }
  // tests omitted
}
```

现在夹具变得多么简单。这个测试类中用到了哪些budget category以及相互之间的区别都一目了然。将所有公共部分搬移到抽象基类AbstractSplitsTestCase中

```java
public abstract class AbstractSplitsTestCase {
    protected Account account;
    @Before
    public void initializeAccount() throws Exception { ... }
    /**
      * Concrete test classes implement this to give the utility
      * methods access to the BudgetCategory objects in use.
      */
    protected abstract List<BudgetCategory> budgetCategories();

    protected List<Transaction> transactions(Transaction t) throws ModelException {
      Document d = ModelFactory.createDocument();
      for (BudgetCategory bc : budgetCategories()) {
        d.addBudgetCategory(bc);
      }
      d.addAccount(account);
      d.addTransaction(t);
      return d.getTransactions();
    }
    protected TransactionSplit createSplit(BudgetCategory category, int amount) throws InvalidValueException { ... }
    protected Transaction createTransaction(Source from, Source to)
      throws InvalidValueException { ... }
}
```





## 单元测试代码的可信赖性

### 注释掉的测试

测试代码被注释掉了时，却不知原因，令人感到困惑。测试的@Test注解注释掉时，也会产生同样的问题。代码编译通过，并且看起来像个测试，但是JUnit不会将其当成测试来运行，因为注解不见了。

当你发现测试（或其一部分）被注释掉了，你其实看到的是死代码。或许它曾经具有意义和目的，但如今要么不存在了，要么丢失了，你无法想象注释掉的代码为什么会在那儿。



解法：

1. 尝试理解并验证它的目的。如果你找到了，取消注释并重构测试，这样它能更好地沟通意图。

2. 否则，删掉它。

### 有歧义的注释

测试用例包含了几个步骤，正如那些仅存的单行注释和用来分割代码块的空行所示。第一段我们创建一个基本账户，然后向账户注册一笔债务，然后验证账户的信誉随着时间超过截止日期而由好变差。

```java
@Test
public void pastDueDateDebtFlagsAccountNotInGoodStanding() {
   // create a basic account
	Customer customer = new Customer();
	DeliquencyPlan deliquencyPlan = DeliquencyPlan.MONTHLY;
	Account account = new CorporateAccount(customer, deliquencyPlan);
   // register a debt that has a due date in the future
	Money amount = new Money("EUR", 1000);
	account.add(new Liability(customer, amount, Time.fromNow(1, DAYS)));
   // account should still be in good standing
	assertTrue(account.inGoodStanding());
   // fast-forward past the due date
	Time.moveForward(1, DAYS);
   // account shouldn't be in good standing anymore
	assertFalse(account.inGoodStanding());
}
```

简单吗？是的。除了两个注释是误导的，很可能对于测试的实际行为给出了错误的印象。

首先，第一段代码据说创建了一个“基本”账户，但代码实际上却用特定的拖欠计划创建了一个企业账户 (`CorporateAccount`)。那不是我所认为的“基本”。其次，尽管我们实际上将时间快进到截止日期当天，后一个注释却说我们将时间模拟为“超过截止日期”。通常，**处于**当天与**超过**截止日期是不同的。你注意到了那个细节吗？



解法：

1. 将注释替换为更好的变量和方法名。

2. 从被注释的代码段中抽取一个方法，并妥善命名。

这种注释——描述代码行为——的基本问题在于，代码实际上应当负责沟通自己的意图。如果你感觉你需要一个注释来解释代码行为，你仍有重构的余地。或许你没有妥善地对变量命名。或许代码段应当封装在一个具有描述性名字的私有方法内。

怎样写好注释？简言之，好的代码注释解释**为什么**，而非**做什么**。

每当你碰到一个解释代码行为的注释，那就是代码坏味道。代码应当足够可读，从而没有注释的必要。有一些情况下，真的需要注释。在那种情况下，注释解释着某个代码块的**缘由**。

例如，一个注释在解释复杂for循环的行为，它就意味着坏味道，而另一个类似的for循环却可能具有良好有效的注释，它在解释循环有多丑陋，因为那是一段性能上至关重要的代码，你还没有找到一个在不牺牲性能的前提下改进可读性的方式。

每当你发现自己在写注释，问问自己到底在描述“做什么”还是“为什么”。然后重新考虑你是否该写注释，还是重构被注释的代码。

有时程序员在方法中添加注释，以看得见的方式使得代码段互相分离。这个考虑可以有，因为最终目的还是为了使代码更加容易阅读。但这种方式值得商榷。

这种情况下，我通常建议用空行来替换注释——至少空行不会像注释那样有时会腐坏。我的一个同事习惯于从方法中删除所有空行，因为那些是代码坏味道。如果你需要用空白字符来区分步骤的话，就表明那个方法太大了。尽管那听起来有点极端，但他是有道理的。那个方法可能太大了，你真的应该考虑重构，可能会是抽取一至两个方法。

### 永不失败的测试用例

不能失败的测试不具有价值，出了事情它决不警告你。永不失败的测试比没有测试还糟，因为它给你虚假的安全感。

检查是否抛出期望的异常，或许这是最常见的存在永不失败的测试的上下文。另一种常见的永不失败的测试就是没有断言。

```java
@Test
public void includeForMissingResourceFails() {
	try {
		new Environment().include("something that does not exist");
	} catch (IOException e) {
		assertThat(e.getMessage(),
			contains("something that does not exist"));
	} 
}
```

这个测试用例的结果是这样的：

1. 如果代码如期工作并抛出异常，那个异常就被catch代码块捕获，于是测试通过。
2. 如果代码**没有**如期工作，也就是没有抛出异常，则方法返回，测试通过，我们并未意识到代码有任何问题。

解法：

增加对JUnit中fail()方法的调用1，使得测试起作用了。现在除非抛出期望的异常，否则测试就失败。

```java
@Test
public void includeForMissingResourceFails() {
   try {
      new Environment().include("something that does not exist");
      fail();
   } catch (IOException e) {
      assertThat(e.getMessage(),
      contains("something that does not exist"));
   }
}
```

此外，JUnit4引入的一个新特性是@Test注解的expected属性。这个属性向JUnit声明你期望测试方法会抛出指定类型的异常，否则测试应当失败。那么就可以去掉了整个try-catch代码块和容易遗漏的fail()调用。

```java
@Test(expected = IOException.class)
public void includingMissingResourceFails() {
   new Environment().include("somethingthatdoesnotexist");
}
```

除了宣讲“不要犯错”，防止偶然地写一个永不失败的测试，最好的方法就是养成运行测试的习惯，或许是临时修改被测代码来故意触发一次失败，从而看到它失败。

### 轻率的承诺

正如欧美国家的政客，为了在选举中领先，政客们在几乎所有电视频道中辩论和接受采访，做出不会兑现的承诺——总有好借口，但总是太过轻率。

轻易承诺的潜在意思是测试用例做得比说得少——或根本没做啥。通常有三类表现：

1. 测试无所事事。
2. 测试实际上没有**测试**任何东西。
3. 测试名不符实。

第一种可以说是最公然地辜负了程序员的信任。

```java
@Test
public void filteringObjects() throws Exception {
    //Array array = new Array("joe", "jane", "john");
    //Array filtered = array.filter(new Predicate<String>() {
    //  public boolean evaluate(String candidate) {
    //    return candidate.length() == 4;
    //  }
    //});
    //assertEquals(new Array("jane", "john"), filtered);
}
```

这个清单中见到所有的测试内容都注释掉了。基本上，这个测试方法是空操作，应当忽略掉。实际上，我们的测试框架会运行这个测试并报告成功，潜在地误导着程序员，他在IDE中看到测试标注为通过，还以为测试名称所描述的功能已经存在了。换句话说，这像是**注释掉的测试**，但是更糟！

而且，当我们扫视这个注释掉的测试，我们无法忽视它。代码写出来然后注释掉，这总有理由的——但我们不知道。这个测试应该通过吗？是否某人在做实验之后忘记了去掉注释？我们不知道。

尽管不常见到整个注释掉的或者无所事事的测试，我们第二轻易承诺的原型例子更为常见。

``` java
@Test
public void cloneRetainsEssentialDetails() throws Exception {
	Document model = ModelFactory.createDocument();
	model.schedule(new Transaction("Test", date("2010-01-20"), 1));
	model = model.clone();
	Transaction tx = model.getScheduledTransactions().get(0);
}
```

乍看没什么问题。它短小、相当平衡，而且具有清楚、可理解的名字——克隆体包含基本的细节(clone retains essential details)。但是仔细一看，发现这个测试实际根本没有**检查**克隆体是否包含基本的细节。没有assertTrue、assertEquals、assertThat——没有任何断言。那意味着无论克隆体的行为如何，这个测试都会通过。具体来说，只有当运行代码时抛出了异常，测试才会注意到克隆体没有按要求工作。

有人叫这种测试为**正常路径测试(Happy Path)**，因为它们往往不会失败，总是表示一切正常。

第三个例子描述如下。它或许代表了最常见的轻易承诺病理。

```java
@Test
public void zipBetweenTwoArraysProducesAHash() throws Exception {
    Array keys = new Array("a", "b", "c");
    Array values = new Array(1, 2, 3);
    Array zipped = keys.zip(values);
    assertNotNull("We have a hash back", zipped.flatten());
}
```

通读这个清单，你看到轻易的承诺了吗？测试的名字表明两个数组对象之间的Zip (压缩)~~操作后会产生一个哈希~~~~值~~操作后会产生一个哈希值。但测试只检查了zip方法返回了不为空的展平(flatten)数组。这与zip方法返回哈希值非常不同，你觉得呢？

这些测试的例子，都是说一套做一套，会让可怜的程序员长出白头发的，他还以为所述的行为存在并且正常工作，然而现实却恰恰相反。



解法：

首先，我们为什么要注释掉它？或许我们开始编写，然后半途意识到步子迈得太大了，注释掉它，换个测试的写法，却忘记回来充实注释掉的测试。或许我们尝试的一些事情破坏了测试，可能为了想要快速恢复回去，于是决定注释掉而不是删除它。

无论什么原因，都有更好的选择。头号原则就是删除代码，而不是注释掉。你有版本控制，对吧？而且，现代IDE具备本地历史，我们可以撤销变更，不会到达版本控制仓库。

一个空测试也比注释掉好得多，因为它是清楚的测试占位符——针对目前缺失、破碎或未验证的行为。更好的方法用JUnit的 `@Ignore` 注解来标记占位符，更加明确地指出那些测试还没有恰当地实现，且所描述的行为还不存在。更好的是拿起旧笔和纸。除了清晰地分开已实现与要实现的，纸笔不会拘泥于文字记录——你可以画草图，这是最自然的方式去描述你的想法。

对于没有断言的测试，解决方案很简单：确保你断言了一些事情，那么**写测试时就从断言开始**。

当你先从输入最终的断言来开始写测试，你很难会从结果测试中偶然地移除断言。此外，从断言开始有助于专注在测试的本质上。具体哪个是我们要在测试中检查的行为？从断言开始还能帮助你避开第三个轻易承诺的例子——测试检查的远没有名字中表明的那样彻底。毕竟，当你只有测试名字和断言语句，几乎不可能看**不**到它们之间的冲突。

另一个窍门是最初将测试名字留空，或者比如叫做TODO()，直到你完整地刻画出了测试。一旦你确切找出要检查的行为，就很容易命名测试。

这里的关键，以及所有这些窍门，就是使程序员(你)尽量简单地看到和注意到测试没有并做到它所声称的样子。考虑到这一点，仅仅保持测试小而专注已经是一个巨大的帮助了。

### 降低的期望

程序员往往注重细节。我们往往也会偷懒，经常找最简单的做事方式。这没错——很多时候我们应该做最简单的事情——但是有时我们过分了，结果搬起石头砸了自己的脚。因为采取了简化方式，这个测试坏味道就叫做**降低的期望**，代价就是降低了确定性与精确度的标准。这种捷径常常能帮你走得更快，也足以造成新增代码与程序的输出或行为有所不同。长期来看，这种测试由于不够精确，会造成一种虚假的安全感。

下面的例子受困于之前讲过的分割逻辑，以及降低的期望。看看你能否认出来。

```java
public class ComplexityCalculatorTest {
           private ComplexityCalculator complexity;
           ...
           @Test
           public void complexityIsZeroForNonExistentFiles() {
              assertEquals(0.0, complexity.of(new Source("NoSuchFile.java")));
           }
           @Test
           public void complexityForSourceFile() {
              double sample1 = complexity.of(new Source("test/Sample1.java"));
              double sample2 = complexity.of(new Source("test/Sample2.java"));
              assertThat(sample1, is(greaterThan(0.0)));
              assertThat(sample2, is(greaterThan(0.0)));
              assertTrue(sample1 != sample2);
           }
}
```



解法：

`ComplexityCalculator`计算指定源文件的圈复杂度，对于它的行为，清单中的测试类定义了期望。

首先，它已经很明显地体现了分割逻辑。测试在处理魔法数字，因为数字等于或大于0.0的原因隐藏在两个源文件中，引用路径为：test/Sample1.java 和 test/Sample2.java。这块不再赘述。

再看看中的`complexityForSourceFile()`。注意是如何用三个断言来验证复杂度计算得正确。首先，我们验证两个源文件的结果都大于零，然后验证两个结果互不相同。假设这个测试通过，我们可以断定在计算背后一定有**一些**逻辑。有趣的“三角法”导致稍微模糊的正确性断言，这正表现出降低的期望。基本上可以说，只要实际计算不为零而且两个源文件的结果不同就可以了。换句话说，那个测试对于变化来说极其健壮；以至于测试本该失败时也不会失败。

对于降低的期望，明显的解决方案就是提高门槛，使测试的期望更具体。你应该明确两个源文件的复杂度计算结果。简单的变更能将五行变成两行，如下：

```java
@Test
public void complexityForSourceFile() {
   assertEquals(2, complexity.of(new Source("test/Sample1.java")));
   assertEquals(5, complexity.of(new Source("test/Sample2.java")));
}
```

### 平台偏见

很久以来，计算机这个词指的就是大家使用的某一个具体产品。然而，程序员愈发需要为他们的软件考虑多个平台和操作系统。尽管Java曾经定下了“编写一次，随处运行”的调子，但因为不是所有事情都能够抽象，我们还是发觉自己在编写特定于平台的功能。软件产品涉及多个平台，测试同样也会。**平台偏见**测试坏味道可以描述为无法平等地应对所有平台。事实比那更复杂，但或许一个例子能表达得透彻。

下面的例子是针对底层操作系统，检查`Configuration`类是否恰当地识别出默认的下载目录。

```java
@Test
public void knowsTheSystemsDownloadDirectory() throws Exception {
	String dir = Configuration.downloadDir().getAbsolutePath();
	Platform platform = Platform.current();
	if (platform.isMac()) {
		assertEquals(dir, matches("/Users/(.*?)/Downloads"));
	} else if (platform.isWindows()) {
		assertThat(dir.toLowerCase(),
			matches("c:\\users\\(.*?)\\downloads"));
	}
}
```



解法：

最引人注目的是，当运行在不同平台时，if-else 将执行不同的断言。换句话说，在任何特定的平台上，只有一个分支会得到执行。

有几个理由说明这是个问题。首先，如果在Linux上运行会怎样？当运行在非Mac OS X或Windows平台时，清单中的测试就装聋作哑。其次，当运行测试时，我们只检查所在平台上的正确行为。这意味着无论其他平台上的行为是否遭到破坏，测试都将通过。

使那些针对特定平台分支可见的一种方式，是将其分割为多个测试，每个平台一个。

```java
public class TestConfiguration {
   Platform platform;
   String downloadsDir;
   @Before
   public void setUp() {
      platform = Platform.current();
      downloadsDir = Configuration.downloadDir().getAbsolutePath();
   }

   @Test
   public void knowsTheSystemsDownloadDirectoryOnMacOsX() throws Exception {
      assumeTrue(platform.isMac());
      assertEquals(downloadsDir, matches("/Users/(.*?)/Downloads"));
   }
  
   @Test
   public void knowsTheSystemsDownloadDirectoryOnWindows() throws Exception {
   assumeTrue(platform.isWindows());
   assertThat(downloadsDir.toLowerCase(), matches("c:\\users\\(.*?)\\downloads"));
   } 
}
```

注意我们是如何添加守卫语句的，它使用了`org.junit.Assume#assumeTrue() `假设性API，当测试没有运行在指定平台时，就中断执行。当该假设性API失败，JUnit并不会执行余下的部分。不幸的是，JUnit会将这些测试标记为通过，尽管我们的确不知道功能在那些平台上是否正确。

即使特定于平台的测试现在对外部可见，还有第二个和更多的基本问题：只有其中的一个测试会执行。为了全部运行三个测试，需要三个不同的计算机。这个问题源于被测代码的设计，而我们的测试坏味道强调了重构的需要。

我们应该尽量重构代码，这样能一个测试接一个测试来替换掉Platform，允许我们在任何平台上运行测试。

```java
@Test
public void knowsTheSystemsDownloadDirectoryOnMacOsX() throws Exception {
   String downloadsDir = new MacOsX().downloadDir();
   assertEquals(downloadsDir,
                matches("/Users/(.*?)/Downloads"));
}
@Test
public void knowsTheSystemsDownloadDirectoryOnWindows() throws Exception {
   String downloadsDir = new Windows().downloadDir();
   assertThat(downloadsDir.toLowerCase(),
              matches("c:\\users\\(.*?)\\downloads"));
}
```

注意我们直接实例化了具体的Platform子类，而非通过间接的Platform.current()，然后根据Platform的变化来运行条件化的测试，以解决这个问题。我们需要在某处测试Platform.current()的平台检测行为，但那些复杂行为不属于处理平台实现的测试。

### 有条件的测试

现代编程语言挺有意思的。多年来我们见证了语言进化和API扩展。别的不说，我们看到了语言结构的方式由库演变成核心语言。但是当错误地使用时，更强大却并不见得好，有条件测试坏味道正是一个佐证。

下列测试代码检查负责执行系统命令的`Process`对象的行为。这个特殊的测试会检查参数是否正确地传入系统命令行。

```java
@Test
public void multipleArgumentsAreSentToShell() throws Exception {
   File dir = createTempDirWithChild("hello.txt");
   String[] cmd = new String[] { "ls", "-1", dir.getAbsolutePath() };
   Process process = new Process(cmd).runAndWait();
   if (process.exitCode() == 0) {
      assertEquals("hello.txt", process.output().trim());
   }
}
```

首先，测试用一个叫做hello.txt的文件创建了临时目录，然后列出临时目录的列表。接下来，我们有个隐藏的条件。如果进程干净地退出(退出码为0)，我们断言目录列表精确地包含我们期望见到的给定参数。

条件分支的问题在于它会等于false，随后，断言并不会执行而测试会默默地通过。例如，我们要是破坏了`Process#runAndWait()`方法，任何系统命令都将失败，返回一个非零退出码。清单的测试将兴高采烈地通过，尽管并没有什么实际上能够工作。

解法：

再次重申，当被测行为遭到破坏时，测试就应该失败。如果有什么不对劲儿，你应该大声说出来，而不是默默地承受。当测试中误打误撞产生了条件分支，你应该确保测试在每个条件分支时都有机会失败。

```java
@Test
public void multipleArgumentsAreSentToShell() throws Exception {
   File dir = createTempDirWithChild("hello.txt");
   String[] cmd = new String[] { "ls", "-1", dir.getAbsolutePath() };
   Process process = new Process(cmd).runAndWait();
   assertEquals(0, process.exitCode());
   assertEquals("hello.txt", process.output().trim());
}
```

增加了那个断言后，除非进程正常退出，否则测试将失败，这就解决了之前由条件语句造成的问题。

除了确保每个条件分支都将在适当的时候触发失败，你应该考虑将每个分支变成它自己的测试。毕竟，如果条件总是评估(evaluate)为相同的结果，它就失去了意义，那么显然软件应该期待多个潜在的执行路径。在之前的简单例子中，备选的执行路径应是命令执行失败的情况。因此，你可以增加一个新测试，检查退出码为非零。

```java
@Test
public void returnsNonZeroExitCodeForFailedCommands() {
   String[] cmd = new String[] { "this", "will", "fail" };
   Process process = new Process(cmd).runAndWait();
   assertThat(process.exitCode(), is(greaterThan(0)));
}
```

添加以后，原清单中的所有条件分支全都用测试覆盖到了，不再受同样问题的煎熬。我们最初的测试确保参数传入了底层的命令行(shell)，并且有测试看到当系统命令失败时，退出码发出失败信号。









## 可测试性的设计

略，请阅读正版原书

## 附录：Java单元测试的基本语法

略，请阅读正版原书

## 附录：测试替身（Test Double）

略，请阅读正版原书

## 附录：利用其他JVM语言如Groovy编写自动化测试

略，请阅读正版原书



