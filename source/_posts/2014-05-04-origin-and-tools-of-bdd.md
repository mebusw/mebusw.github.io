title: 关于BDD的起源和工具
tags:
  - atdd
  - bdd
  - cucumber
  - easyb
  - groovy
  - JVM
  - robotframework
categories:
  - agile
  - engineering
---

![](http://arnauld.github.io/incubation/jbehave-get-started/bdd-cycle-around-tdd-cycles.png)

过去的几十年间，人们曾用过测试先行的方式，但20世纪90年代才真正提出测试驱动开发的方法。

BDD源自一些TDD实践者在寻求更好的词汇来描述在TDD循环中测试的意图。

英国人Dan North在2006年发表了《Better Software》的文章，首先意识到了TDD思想和"测试”一词会误导人们。Dan将这种风格的TDD命名为行为驱动开发，成功地将测试先行编程推进了一步。

由于test一词并没有抓住指定期望行为的精髓，它反而承载了太多的含义。相反，社区开始谈论specifications(需求说明，简称specs)和行为，而非测试与测试方法。

今天的BDD语境和领域远远超出了代码——最引人注目的是将BDD提升到需求层面，与业务分析和需求行为结合起来。

<!--more-->

通过将优秀的TDD实践者的良好习惯正式化，测试驱动开发衍生出了行为驱动开发。优秀的TDD实践者由外向内地思考，他们先编写一个失败的客户验收测试，用于从客户视角描述系统。作为BDD实践者，我们小心地以例子的形式编写验收测试，使任何团队成员都能够理解。我们遵循这个过程，编写例子来从业务干系人那里获得反馈，在动手之前就能了解我们是否在构建正确的东西。

作为寻找更好词汇的副产品，BDD社区编写了大量好用的替代品，创造出许多类似JUnit的测试框架。当你真正在指定预期行为时，这些工具除了帮助你避开测试的浑水，往往还能强调测试意图并将语法淡入幕后。

常见的BDD(又称ATDD)框架工具，其中最早和最流行的莫属基于Python的RobotFramwork框架([robotframework.org](robotframework.org))了，大约在2007年左右由诺基亚开源。最初是秉承关键字驱动理念，很快也进化出了given-when-then的词法。
而几年来另一颗冉冉升起的是基于Ruby的Cucumber框架([http://cukes.info/](http://cukes.info/))，在Java、C#、nodeJS上也不乏模仿者。

另类JVM语言如Groovy、JRuby和Scala都以简洁语法为荣，许多测试框架的作者都利用了这种优势。其中一个基于Groovy语言的easyb框架([http://easyb.org](http://easyb.org))体现了BDD框架中流行的`given-when-then`词汇。

``` [groovy]
scenario "New lists are empty", {
    given "a new list"
    then "the list should be empty"
}
scenario "Lists with things in them are not empty", {
    given "a new list"
    when "an object is added"
    then "the list should not be empty"
}
```

注意到两个场景读起来好像都和自然语言差不多。这些场景大纲(scenario outlines)清晰地沟通了意图。Easyb另人愉快地在自由与结构之间平衡。但大纲不能帮助计算机检查需求是否得到满足。你需要更详细地描述场景。

``` [groovy]
scenario "New lists are empty", {
     given "a new list", {
     list = new List()
 }
     then "the list should be empty", {
     list.isEmpty().shouldBe true
} }

scenario "Lists with things in them are not empty", {
     given "a new list", {
     list = new List()
 }
     when "an object is added", {
     list.add(new Object())
 }
    then "the list should not be empty", {
     list.isEmpty().shouldBe false
 }
}
```

看这些细化场景的given-when-then步骤，你会看到每个步骤的含义是用通俗英语描述的，而每个步骤要传达的技术细节是用代码来执行的。

BDD工具不仅支持关键字驱动，同样也都支持数据驱动，或称参数化测试。例如，将where块中变量a, b, c值的两种组合：[5, 1, 5] 和 [3, 9, 9]代入expect 块进行计算。

``` [groovy]
def "computing the maximum of two numbers"() {
   expect:
     Math.max(a, b) == c
   where:
     a << [5, 3]
     b << [1, 9]
     c << [5, 9]
}
```

各种BDD工具介于ATDD和TDD之间，是否采用，以及采用哪个工具，还是要依赖你的技术栈和上下文。最重要的是，参与其中的人的因素。

欲知更多详细的内容，敬请期待@申导 翻译的《Effective Unit Testing》一书，2014年即将出版。

![](http://res.uperform.cn//effective-unit-testing.png?imageView/2/w/200/h/200/q/85/format/PNG)