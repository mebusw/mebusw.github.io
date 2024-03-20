title: 估算是一种，很难的东西，如影~随行~
date: 2012-05-07 20:12:46
tags:
	- estimating
categories:
    - agile
    - engineering	
---


最近Uncle Bob发表了新的博客 [Why is estimating so hard?](http://blog.8thlight.com/uncle-bob/2012/04/20/Why-Is-Estimating-So-Hard.html)。



Bob大叔首先抛出一个问题，如何将著名的葛底斯堡演说的237个单词以固定字体和固定行宽写在一张书签上。如果人工执行这个任务，假设每秒钟处理一个单词来寻找合适的断句点，估计5分钟内就可以完成，而且实际花费时间也和估计的差不多。然而，如果要编写程序来做，要花多久？而且是在知晓算法、没有意外情况、没有绊脚石、无需备份和恢复功能的情况下，编写程序要花多久时间？


<!--more-->

Bob大叔提醒说：程序只不过是遵循某个过程的具体指令，而这个过程是已知的。在动手写程序之前给出3个估算，最佳情况、最差情况和正常情况。根据Bob大叔的统计，大部分人需要花上30-45分钟，也有人用了15分钟，还有人用90分钟。这样，很多人之前的估算与实际花费相差悬殊。其中一个原因，他们基于手工任务看似简单来进行估算的。
Bob大叔回忆某个下午和Kent Beck采用测试驱动开发来结对编程写这个算法。他们估计这需要10-15分钟，结果花了30分钟却毫无进展。在被迫接受这个体验后思考，为什么这算法这么难？为什么把如此简单和直观的过程写下来这么难？
其实，人类是目标导向的，在分解文字时，人类不会遵循一个过程，而是不断评估输出，然后调整做法直到正确为止，因此会预估5分钟之内完成。而过程是盲目的，它不管输出是否正确。如果过程错了，那输出结果也会错。人类不了解过程，不了解过程的难度如何。人类不是电脑，做事的时候不会遵循过程，所以无法比较过程任务和手工任务的复杂性。这就是估算为什么难，而且经常犯错的一个原因：任务看上去简单，人们基于这个表面现象来估算，之后却发现写出过程实际上是多么复杂。人们估算不准是因为估算了错误的东西。
回到分解长字符串的例子。每次分解一行，记录下分解位置和选择这个位置的原因。将其概括为三个不同的场景：
如果单词长于10个字符，在第10字符处断词。
如果第11个字符是空格，在第11字符处断词。
从第10个字符向回查找，如果找到一个空格，就在该处断词。
这些场景仍然需要被组成一个过程，但是至少知道这个过程有几个部分组成，从而使估算更容易。
这个故事的寓意是任务看似容易被人类解决，却经常被描述为复杂的过程。所以估算时，确保不要被简单的表面现象所迷惑。深入进去，尝试列举出过程所包含的场景数量。
博文显示估算失真有多容易。人脑不善于回答抽象问题，往往替换实际问题为一个直觉问题。而直觉在寻找答案时，如同博文所说，以期望结果的产生来考虑问题，不关注未知或可能出错的东西，而是关注那些能够理解的东西。
博文引发大量讨论。有人认为分解过程为小的片段并无价值，有价值的是知道哪类问题是困难的和为什么困难。也有人认为多练习有助于提高估算准确度，或者让有经验的人而不是新手估算。然而经验最可以依靠，却仍然可能出错，除非项目与之前完全一样。
除了博文所讨论的原因，还有人认为，团队水平、办公室政治、企业缺乏变更控制，也都成为导致估算不准确的原因。
更多的人吐槽：在实际中，估算往往发生在没有明确需求可以参考的时候，更不用说之后不断变化的需求、未知因素、代码基础中隐藏的陷阱。因此固执地遵循最初的时间表也使得估算看起来是不准确的。而且开发人员面对来自于客户和经理的压力时，往往倾向于低估时间表。
InfoQ的读者们，你们对于估算有什么样的经验？什么时候估算得准确，什么时候又不够准确呢？

原文： 

<http://www.infoq.com/cn/news/2012/05/estimate-is-difficult>