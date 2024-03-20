title: TDD中变形动作的优先顺序  -  Transformation Priority Premise
tags:
  - tdd
  - tpp
  - transformation
categories:
  - agile
  - engineering
---

在TDD循环中，重构(Refactoring)是不改变行为而改变内部结构的动作，保持测试常绿。而变形(Transformation)是改变内部实现来使测试由红变绿。这些变形的变化使代码形式从特殊specific到一般generic。

Uncle Bob的一篇文章。<span style="text-decoration: underline;">http://blog.8thlight.com/uncle-bob/2013/05/27/TheTransformationPriorityPremise.html</span>
中用了bowling game kata 和 Prime Factor kata来解释。
<!--more-->

然后，基于变形的复杂度，Uncle Bob列举出来并认为它们有如下优先顺序。

&nbsp;

1.  <span style="color: #3c3c3c;">({}–&gt;nil)</span><span style="color: #3c3c3c;"> </span><span style="color: #3c3c3c;">     </span>无代码=》NULL
2.  (nil-&gt;constant)   nil=》常量
3.  (constant-&gt;constant+)     常量=》测试更复杂常量
4.  (constant-&gt;scalar)     常量=》单变量
5.  (statement-&gt;statements)     语句=》更多非条件语句
6.  (unconditional-&gt;if)   非条件语句=》IF语句，分解执行路径
7.  (scalar-&gt;array)   单变量=》数组
8.  (array-&gt;container)   数组=》容器
9.  (statement-&gt;recursion)   语句=》递归
10.  (if-&gt;while)    IF语句=》WHILE语句
11.  (expression-&gt;function)     表达式=》函数或算法
12.  (variable-&gt;assignment)    变量=》赋值
结论是，要规避TDD常见的僵局(Impasse)，即某个新test必须对代码进行巨大变化才能满足，且测试顺序的选择非常重要———那就要尽量选择高优先级（简单的）变形来满足测试，或是选择能由高优先级满足的新test case来继续，当看起来需要低优先级(复杂的)变形时，回退回去，看看还有没有更简单的test。

他用了word wrap kata来解释这一模式。其中，为了使

`assertThat(wrap("word word", 6), is("word\nword"));
`

通过，Bob故意用

`return s.replaceAll(" ", "\n”);

//(expression->function) transformation

`

，结果陷入困境。

然后Bob尝试认为此案例没有更好的方案，于是换了另一个测试

`assertThat(wrap("longword", 4), is("long\nword"));
assertThat(wrap("longerword", 6), is("longer\nword"));
`

然后用 (null-&gt;constant) 和(expression-&gt;function)  得到

`return s.substring(0, length) + "\n" + s.substring(length);`

TDD困境主要还是在于，如果低优先级的复杂(expression-&gt;function)变形，步子更大更死板，看似简洁，实则隐藏了细节，更难“演进”实现。
个人任务，第一个case同样可以用 (null-&gt;constant) 和(expression-&gt;function)  得到类似的方案。
Bob的这个思路是对的，但是例子以及背后的理论证明尚不完善。

另外一篇关于用TPP开发排序算法的文章。<span style="text-decoration: underline;">http://blog.8thlight.com/uncle-bob/2013/05/27/TransformationPriorityAndSorting.html</span>