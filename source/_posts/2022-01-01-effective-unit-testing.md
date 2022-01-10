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

## 何为优秀与代码坏味道

可读性、可测性、可信赖性

<!--more-->





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



## 单元测试代码的可测性







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









## 可测试的设计

## 附录：Java单元测试的基本语法

## 附录：测试替身（Test Double）

## 附录：利用其他JVM语言如Groovy编写自动化测试

## 更多连载

