title: python中的比较运算
tags:
categories:
    - programming
    - python
---

python语言对于各种类型都可以比较。

例如int类型，当进行大小比较时，其实是调用了__cmp__函数，或者可以显式调用cmp(),返回值为{-1, 0, 1}，表示小于，等于，大于。

对于str类型,list类型,,class类型等，其实是调用__eq__, __ne__, __gt__,__ge__,__lt__, __le__等函数，分别对应==, !=, &gt;, &gt;=, &lt;, &lt;=等.

可以根据需要覆盖这些函数达到重载运算符的目的。**注意，这里有个坑，就是__eq__, __ne__是两个运算函数**，而不像Java里面，只有一个Object.equals()函数。

<!--more-->

笔者在使用unittest库做TDD编程时，就遇到这种情况。对某个class重载了==运算符，并且测试中会对含有该class实例的list进行assertListEqual。结果明明相等的两个list，却assert失败。经过查看unittest源码，发现其中对list逐个比较时，使用了不是==，而是!=。而!=并没有重载，所以出现了不可预知的问题，即a==b和a!=b都为True。
<div></div>
对于list/tuple类型，默认按顺序逐个比较元素，然后比较首个不同的值。

对于set类型，运算符&gt;,&lt;分别表示子集和超集，因此有可能出现集合a&lt;b与a&gt;b同时为false的情形，比如：
<pre>&gt;&gt;&gt; a=set('123')
&gt;&gt;&gt; b=set('234')
&gt;&gt;&gt; a&lt;b
False
&gt;&gt;&gt; a&gt;b
False
&gt;&gt;&gt; b&lt;a
False
&gt;&gt;&gt; b&gt;a
False
&gt;&gt;&gt; a==b
False
&gt;&gt;&gt;</pre>
对于dict类型，先比较长度。然后再从双方最小key开始，看两边对于相同key有没有不同的value，没有的话说明两个dict相等。当碰到不同key-value，相比较key，相同的话再比较value。

当碰到不同类型进行比较时，往往退化为类型名称的比较。

对于浮点数的比较，我们知道，无论哪种语言，只要才用IEEE浮点数表示，就意味着不是所有浮点数都能精确表示出来。

一般的比较浮点数，都是用阈值来判断，即abs(a - b) &lt; threshold，来认为两者足够接近。

此外，还有一种方式，就是比较两个浮点数之间允许有多少个其他可以精确表达的浮点数存在，相当于相对误差，即abs ( (int&amp;)f1 - (int&amp;)f2 ) &lt; absDelta

参考 [http://hi.baidu.com/kanif/item/91a1605567e202c59e266793](http://hi.baidu.com/kanif/item/91a1605567e202c59e266793)

参考[http://www.flatws.cn/article/program/python/2011-03-16/17012.html](http://www.flatws.cn/article/program/python/2011-03-16/17012.html)