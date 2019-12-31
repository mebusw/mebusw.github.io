title: Spotify的新型敏捷矩阵组织：部落、分队、分会和协会
tags:
  - organization
  - scaling
  - spotify
  - less
categories:
  - agile
  - scaling
---

![](http://res.uperform.cn//spotify-engineering-culture-part1.jpeg?imageView/2/w/500/h/270/q/85/format/PNG) 

组织导入敏捷往往更加关注形式和标准化，然后却发现不同团队的接受程度不同。 这里介绍了Spotify 的大规模敏捷之路。Spotify是继Pandora之后，国外第二大音乐媒体网站，在从6个人扩张到1200人的过程中，它使用一种了新型的矩阵组织来扩展：部落、分队、分会和协会。 

![](http://res.uperform.cn//spotify-less-2.png)

<!--more--> 
之前@申导 在NSN工作时，诺西的敏捷组织也有些类似这样。每个DA(开发部门，负责系统中一大领域)就是一个部落Tribe,大约80人左右。每个Scrum Team就是分队Squad, 大约5-9人，开发测试跨职能团队。横向会有一些技术领域或测试能力的Competence Center，正如分会Chapter，人们在跨团队之间组成相近领域的小组进行交流和协作，比如某两个团队可能都工作在同一个component上，那么其中的某几个人都是这个component的贡献者和Chapter成员。至于大部门之间，一定也少不了协作，同样需要虚拟专家团队为持续集成系统共同努力，或者对Chrous操作系统的兴趣小组，或者教练社区，这些都可以称为协会Guild。 这种大规模敏捷的扩展方式，也被称为"LeSS". 

![](http://res.uperform.cn//spotify-less-3.png)

借用@窦涵之 之前画的一张图。

![](http://res.uperform.cn//spotify-less-4.png)

这是矩阵组织，但也不完全是。这个和我们常用的矩阵结构是不同类型的。
大多数矩阵组织中,相似技能的人“扎堆儿”到一起形成职能部门,接着“被分配”到项 目中,并且“汇报”给职能经理。而这里的矩阵偏重于**交付**。鼓励团队有自己个性化的实践，自发地传染和授粉行为。但每个团队需要与整体目标对齐，避免局部优化。一致性与灵活性之间寻求平衡。

![](http://res.uperform.cn//spotify-less-5.png)
![](http://res.uperform.cn//spotify-less-6.png)


团队的约束和边界在于产品方向和策略，以及短期的迭代的目标。内部开源模型，其实就是集体代码所有制，尽量消除模块团队之间的壁垒。

![](http://res.uperform.cn//spotify-less-7.png)

![](http://res.uperform.cn//spotify-less-8.png)


大型组织内的团队需要高自治(Autonomy)与高度联盟(Alignment)。 

![](http://res.uperform.cn//spotify-less-9.png)

固定时间盒的release train发布模型，利用feature toogle未完成的特性。做模块解耦这样增加分别发布的灵活性。 

![](http://res.uperform.cn//spotify-less-10.png) 
![](http://res.uperform.cn//spotify-less-11.png) 

频繁地发布与降低发布难度相辅相成。运维部门更多关注如何能帮助研发团队“自行发布”。 

![](http://res.uperform.cn//spotify-less-12.png)
![](http://res.uperform.cn//spotify-less-13.png)


在人员培养上，更加着重激励，关注人们为什么不满意。鼓励团体作战，与他人协作找到更好的解决方案。

![](http://res.uperform.cn//spotify-less-14.png)
![](http://res.uperform.cn//spotify-less-15.png) 



更多参考： 

<http://bobjiang.com/wp-content/uploads/2014/02/Scaling-Agile-at-Spotify-v7.pdf>
<https://labs.spotify.com/2014/03/27/spotify-engineering-culture-part-1/>
<https://labs.spotify.com/2014/09/20/spotify-engineering-culture-part-2/>
[LeSS](www.crosstalkonline.org/storage/issue-archives/2013/201305/201305-Larman.pdf)
[SAFe](scaledagileframework.com)
<http://www.scrum-breakfast.com/2013/10/scaling-scrum-safe-dad-or-less.html>