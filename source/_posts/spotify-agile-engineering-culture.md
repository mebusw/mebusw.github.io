title: Spotify的敏捷工程文化
date: 2014-10-07 18:03:10

tags:
  - organization
  - scaling
  - spotify
  - less
  - culture
  - lean
  - fail

categories:
    - agile
    - scaling

---

Spotify是继Pandora之后，国外第二大音乐媒体网站。上一篇
[Spotify的新型敏捷矩阵组织：部落、分队、分会和协会](http://www.jackyshen.com/2014/02/07/spotify-new-agile-organization-with-tribe-chapter-squad-guild/)出了以后，很多人对Spotify的敏捷组织形式非常感兴趣。这里@申导 抢鲜给大家带来第二部分，关于Spotify的敏捷工程**文化**。

第一部分主要提到基于敏捷原则，将人按照小分队Squad的方式来组织，松耦合但紧密联盟(Align)。通过内部代码开源，鼓励“异花传粉”。通过架构解耦、特性开关等手段来做到频密的小规模发布。借助自助的发布和部署服务，减少人们之间的任务交接。所有一切都关于“人”，因此组织更加要关注激励、信任与社区文化，而非等级结构或控制。



第二部分讲的是关于失败，用Spotify的创始人Daniel的话说就是，“我们要比其它人更快地犯错误”。因为失败意味着学习，因此更快地失败就意味着更快地**学习**，更快地改进。


![](http://res.uperform.cn//spotify-engineering-culture-part2.jpeg?imageView/2/w/500/h/270/q/85/format/PNG) 

<!--more-->

把人关在笼子里是比较安全，但很难学习到东西，人也不会觉得快乐。而探索世界虽然可能受更多的伤害，但更加快乐，更快地成长，至于伤口，总会愈合的。在一个`“对失败友好的环境”`中，鼓励大家面对失败，庆祝失败，而不仅仅是避免失败。

![](http://res.uperform.cn//spotify-less-21.png)


失败不单纯是失败，而是是为了学习。在几周一次的回顾会议中，不要过多关注“谁犯的错？”，而是更多关注“我们学到了什么？”和“我们将要做出哪些改变？”，从而避免将来犯同样的错误，针对产品也针对过程。这样才能做到**持续改进**，改进需要自底向上的驱动，也需要自顶向下的支持。

![](http://res.uperform.cn//spotify-less-22.png)


失败也是有代价的，得想办法控制成本。比如通过架构解耦，将缺陷控制在有限范围内，不影响其他组件，快速修复出问题的那一部分。再比如利用“灰度发布”，一开始只将新功能推给有限的用户，确认稳定后，再快速分发给大众用户。这样，即使新功能出了问题，影响的人群和时间也都是有限的，也就更有底气去做试验、学习。相反，“如果一起尽在掌控，你就落后了”。

![](http://res.uperform.cn//spotify-less-23.png)


关于产品开发，Spotify借鉴了精益创业的思想。产品开发最大的风险来自于构建了错误的产品。因此，在实现一个新点子之前，可以先针对有限人群做访谈、试用原型，看看新功能对人们的影响(Impact)是什么，人们的行为会有什么样变化。下一步才是构建MVP(最小可行产品)，然后通过例如A/B发布的形式分析用户行为数据，再对产品优化调整。
 
![](http://res.uperform.cn//spotify-less-24.png)


管理者经常会问，“我们什么时候发布？”然而，100%的可预测性会扼杀创新，关注对用户的影响比关注团队产能意义更大。固定的交付时间显然在某些情况下有意义，特别是配合市场推广或者第三方集成的时候，但是要尽可能推迟决策。减小了遵循计划的压力，就更加可能关注交付价值，让人们更加投入于工作。

![](http://res.uperform.cn//spotify-less-25.png)
![](http://res.uperform.cn//spotify-less-26.png)

在Spotify，鼓励人们有10%的Hack Time来尝试一些新的想法，甚至利用整周的Hack Week来真正地做出些够酷的东西来。人们天生就是创造者，那就给他们一些时间来创造吧。

![](http://res.uperform.cn//spotify-less-27.png)


在`“对失败友好的环境”`中，很多事情没有固定的正确答案。与其无休止的争论，不如看一看“现在的假设是什么?”、“我们学到了什么？”、“接下来要尝试什么？”，让现实数据来驱动决策。

![](http://res.uperform.cn//spotify-less-28.png)


在一个消除浪费的**精益**文化中，人们会保留有用的实践和工具，同时快速地停止做那些无用的事情。

![](http://res.uperform.cn//spotify-less-29.png)


大型项目意味着更大的风险，尽量将工作分解为更小的组成部分。一定要做大型项目的话，那就可视化整个项目、每日同步、每周演示，尽早地进行集成，尽早反馈，尽早暴露风险。同时，让产品负责人与技术负责人组成领导力小组，从整体上进行把握。

![](http://res.uperform.cn//spotify-less-30.png)


随着团队的成长，烦恼也随之而来。团队随时会陷入混乱，但过多的约束也会让组织滋生官僚。作为管理者要在混乱与官僚两者之间寻求平衡，找到`“最小可行的官僚”`。

![](http://res.uperform.cn//spotify-less-31.png)


消除浪费的艺术，在于可视化并且经常进行讨论。因而团队除了回顾会议，还建立了改进看板(Improvement Board)，展示当前的障碍和后续行动。就某项改进，借助Toyota Improvment Kata，可以分别对现状、长期目标、短期目标，行动项队列进行分析，利用不断的短期胜利逐步接近最高标准(Definition of Awesome)。

![](http://res.uperform.cn//spotify-less-32.png)
![](http://res.uperform.cn//spotify-less-33.png)
![](http://res.uperform.cn//spotify-less-34.png)


健康的文化可以修复破损的过程。文化如此重要，以至于没有人会“拥有文化”。可以建立专注于文化的角色例如教练协会、Boot Camp新兵训练营、分享经验的故事会（@申导 曾经工作过的NSN也早就建立过这些实践了），来保持文化的健康。

![](http://res.uperform.cn//spotify-less-35.png)
![](http://res.uperform.cn//spotify-less-36.png)
![](http://res.uperform.cn//spotify-less-37.png)


文化不是一句口号，而是每个人的态度和行为汇集在一起的表现。每个人都是文化的一部分，每个人都要以身作则地践行所期望的文化。

![](http://res.uperform.cn//spotify-less-38.png)




更多参考： 


<https://labs.spotify.com/2014/03/27/spotify-engineering-culture-part-1/>
<https://labs.spotify.com/2014/09/20/spotify-engineering-culture-part-2/>
[LeSS](www.crosstalkonline.org/storage/issue-archives/2013/201305/201305-Larman.pdf)
[SAFe](scaledagileframework.com)
<http://www.scrum-breakfast.com/2013/10/scaling-scrum-safe-dad-or-less.html>