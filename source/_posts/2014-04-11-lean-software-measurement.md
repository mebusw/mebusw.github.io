title: 精益软件度量
tags:
  - lean
  - metrics
categories:
  - agile
  - lean
---

[![](http://jackyshen.com/wordpress/wp-content/uploads/2014/04/屏幕快照-2014-04-11-下午12.09.02-300x222.png)](http://jackyshen.com/wordpress/wp-content/uploads/2014/04/屏幕快照-2014-04-11-下午12.09.02.png)

(题图感谢@杜伟忠)
该书由Thoughtworks的张松编著，试图回答敏捷转型中常被问到的”度量“问题。该问题常常隐藏着高级管理层的期待，但也包含敏捷怀疑者的抵触。作者从敏捷实践、团队成长、项目质量与价值等角度列出了一些手段，有一些大家都耳熟能详了，比如代码质量或故事点等。
然而，某些问题并不是那么容易回答的，或者只能用定性而非定量手段；或者反馈速度较慢。还有些问题，例如资源利用率(或人天成本等)，作者恐怕也难以给出通用的答案。

此外，度量要耗费工作量，却并不产生价值；而且容易因为绩效考核的原因而走样，所以选择度量手段也需要斟酌。

<!--more-->

度量一定来自于组织的目标。书中作者从持续改进的角度来探讨：
价值
价值命中率
满意度
效率
响应速度
团队产能
质量
内部质量
外部质量
能力
个人
团队
组织

对于价值的响应速度的度量，首先强调了Definition Of Done的作用，只有完整执行了开发活动的特性才能认为是”完成“，从而具备度量的意义和准确性，将反馈和风险提前。
对于待开发特性的评估，可以参考《Agile Planning》里面对于市场价值和优先级分析的部分。对于已开发特性，用户使用数据是个不错的办法（互联网产品早就在使用了）。
不断尝试就是加速反馈，而需要尽快淘汰不靠谱的想法从而降低尝试的成本。

提高响应速度，作者指出要减少浪费和WIP。如果每个工作单元的粒度差不多，则系统可以顺畅地运行，减少等待和瓶颈。根据排队论，在负载很重的系统中，较小的工作单元可以较快的通过系统(指完成一个个的开发活动)。因此将大型需求拆分成独立的且大小相近的特性是必要的。可以通过价值流图、累积流图等手段来进行可视化。

至于估算，无论是功能点还是故事点，都没有完美的方式。但无论如何，持续地讨论和沟通是促进估算越来越精确的关键因素。
减少手工劳动、降低系统的规模和复杂度、减少中断和多任务切换，都是减少浪费、提高产能的因素。

作者从减少技术债的角度来度量内部质量，用到的无非是代码质量工具和持续集成。

外部质量则提高产品可靠性、用户满意度等。这些可能都是在产品发布后才能得到数据。
对于开发过程，无论是防范缺陷(加强沟通、降低复杂度以及预防性开发活动)还是更早地发现缺陷(增加保障性开发活动)，都是在提高反馈速度。然而，这些度量定性居多，难以定量，特别是不具备横向可比性。

最后，对个人不仅要重视技术能力，还要注重主动能力、社交能力。我认为在招聘时就要把好关，后天的培养最多是挖掘潜力。
团队能力，我觉得敏捷开发的纪律性是非常重要的。
至于组织，还是和管理者所创造的文化息息相关，领导如果自己就缺乏方向感，只是耍政治手腕，不能够以身作则，那么支持性环境只是空话。