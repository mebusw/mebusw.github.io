title: J.P.摩根运用LeSS框架实施大规模敏捷
tags:
  - jpmorgan
  - less
  - scaling
  - scrum
categories:
  - agile
  - scaling
---

顶尖金融服务企业中的大型组织是如何采用大规模Scrum框架(LeSS)的？

## 背景

J.P.摩根的全球核心处理技术集团由Simon Cooper领导，是一个遍布全球的3000多人的组织。2013年集团决定要改变为(真正的)Scrum，并采用Large-Scale Scrum(LeSS)框架，这意味着在组织设计要做出相应改变。

之前他们曾零散地采用了"Scrum-But"及各种敏捷工程技术，主要是在开发团队中，但现有的权力和组织结构并无显著改变，与业务的交互也是一样——仍然是“合同谈判”而非“客户协作”。仍然存在负责交付“合同”的研发项目经理、团队主管、单一职能的专家角色，以及单独的组件团队和职能团队（比如测试团队）。

<div class="clear" style="color: #000000;"></div>

年初Craig与Simon Cooper及其直接下属的经理做了一次有力的对话，随后他们就决定将组织设计转变为基于LeSS的Scrum框架。

这次领导力对话认清了组织中目前“合同游戏”的动态，它阻碍了组织提高敏捷性和改善以客户为中心的特性交付。

这次领导力对话也针对真正的特性团队进行了探讨——跨组件和跨职能团队，它可以端到端地交付客户特性，不存在移交、依赖或延迟。这需要消除组织内的藩篱，比如职能部门和组件团队，以及相应的经理角色。

<div id="lowerFullwidthVCR" style="color: #000000;"></div>
<!--more-->

管理者们乐于接受这些想法，因为他们认为有可能提高敏捷性，简化计划和协调工作，更多关注价值，减少交付的问题。现在他们意识到要想转变为Scrum框架，就需要调整组织结构及相应的学习，而不是之前想的那样“Scrum只是团队实践”。

核心证券处理集团内的变化

在通往Scrum的路上，他们决定让Mike Goldverg麾下的证券组织首先进行重组。这意味着证券团队不得不第一个吃螃蟹来考虑影响深远的组织变化。集团决定了以下的优先级：

*   理清部门所支持的产品间的联系，识别出产品负责人，并授权他在每个迭代(Sprint)中决定发布日期、内容和优先级。
*   建立特性团队，关闭按职能划分的部门。
*   消除一线经理角色。
*   改变剩余经理的心态和行为，由命令与控制向教练与指导转变。
*   识别和培养熟练的程序员作为老师，直接与团队一起工作来辅导极限编程(XP)技术，比如TDD。
*   由自组织的特性团队(feature team)自己来做重要的决定，比如选举Scrum Master。

## 传统组件团队和相应的计划

证券集团之前是按照架构组件的传统方式来组织的，每个组件有一名经理即负责人。组件负责人习惯于与多个业务单元一起做决定，主要是作为年度计划周期的一部分。由于以客户为中心的特性通常跨多个组件，那么为了交付端到端的以客户为中心的特性，多个组件负责人需要共同商定时间表和优先级。自顶向下的协调做计划（出现在各种事件中，但实际经常难以意识到）通常很耗时，涉及多轮谈话和谈判，因为不同的业务单元（和特性）会由于组件团队的可用性和优先级以及“他们的”小算盘而竞争。

## 识别更大的以客户为中心的产品

作为采用Scrum的一部分，人们意识到以客户为中心的解决方案或产品涉及多个组件的共同交付，因为以客户为中心的特性通常是跨组件的。

因此，作为Scrum转变的一部分，传统的组件团队（及相应的经理角色）将被消除，代之以更大的多组件产品，更为端到端地来考虑以客户为中心的特性。例如，交易处理产品。

## 识别真正的产品负责人（PO）

转变为以特性为中心的产品可以简化计划的制定和优先级排列，因为在不同的管理团队之间减少了所需的沟通。谁来排列优先级？不同于传统的组件负责人或研发项目经理，我们寻找业务方面的产品负责人来支持敏捷开发的协作游戏，研发和业务人员在其中紧密的协作。

通常，我们很难找到合适的产品负责人，因为几十年来传统模型中业务方面不会参与到开发中，除了每年或许来一次“定义明年的需求“。

关键是找到资深的、受人尊重以及积极的——通常他们明白传统模型带来延迟和僵化——业务方面的产品负责人。这次请高级运营主管参与寻找合适人选。

在证券运营组织内，找到满足全面特征和行为的人来担任产品负责人是个挑战（证券部门内有多个大型产品）。这是由于运营集团是按照筒仓(silo)来组织的，按照业务流程进行分割。因为产品通常会支持许多业务流程，因此没有明显的合适级别的人选可以跨大型产品来对优先级做决定。

解决方案是建立小的产品负责人团队，从研发及运营中选取有意愿、聪明和有经验的人。指定并将权力授予了一位对构建新产品有经验的高级运营主管。

有几个新产品部门稍微大一些——有着10个以上的特性团队。在那种情况下，新的（单一）产品负责人难以有效地与所有团队打交道，因此需要分而治之。不再像传统方式按照架构组件来分割，我们按照客户关注的领域来分解，这在LeSS中称为需求领域(Requirement Area)。例如，在证券处理产品中，我们引入了市场管理(4个团队)和合规控制(4个团队)两个需求领域。

然后，联合每个需求领域(遵循LeSS大框架)，我们引入领域产品负责人(Area PO)，他会与某一个领域中的几个团队一起工作。

建立这个团队是一个重大的重组，将责任从传统的项目经理和组件负责人身上转移出来，令业务方面的人更多地参与，并强调高级领导者的参与，以使LeSS能够工作。

## 生长出特性团队

在之前的“Scrum-But”情况下，人们认为他们在做“Sprint”，用各种分析团队、组件团队和测试团队来组成Scrum，在类似瀑布过程中相互交接工作。但是这只是局部优化，基本上是在现有传统组织之上叠加了Scrum方法。在“Scrum-But”情况下，交付一个以客户为中心的特性涉及许多团队之间的复杂协作，大量交接，重叠工作和严重的多任务切换。导致交付时间长，更多的缺陷，开发人员不满意，最终客户失望，于是降低了交付的业务价值。

转型为能够完成端到端特性的真正跨职能Scrum团队，Mike Goldverg走访了他在全球的关键分支，介绍了LeSS中的自设计团队工作坊技术。（更多参见ScrumAlliance中关于LeSS文章， [How to Form Teams in Large-Scale Scrum? A Story of Self-Designing Teams](http://www.scrumalliance.org/community/articles/2013/2013-april/how-to-form-teams-in-large-scale-scrum-a-story-of)）

每个分支花了不到三个小时，从单独的职能和组件团队变为跨职能和跨组件的特性团队[feature team](http://featureteamprimer.org/feature_team_primer12.pdf)。每个新团队都具有混合的领域、技术和职能技能。

这会是——也仍然是——对于新团队心态的困难变化，因为现在他们的职责是交付“完成”的客户特性，而不是仅仅做一小部分工作，或“为头衔而工作”。

调整特性团队更为困难的部分是在于，从集中式决策和专家组转变为分布式角色和跨职能团队。

例如，一个团队成员之前是单一专业的信息架构师（IA）。在新Scrum团队中，他被团队成员鼓励向其他成员教授他的IA技能（来提高灵活性，降低“关键人”风险）,并且也自行领取任务作为第二专业。但他只想做IA的任务——局部优化。他擅长此道，但实际上成为了整体产出的瓶颈，降低了灵活性，由于没有把他的知识教给其他人而增加了风险。几个月内他在其他组织找到一个传统IA角色的工作。

同时，一些以IA技能（如数据建模）为第二专业的其他成员现在可以工作并在该领域内提高其技能，有助于加速交付客户特性，降低关键人风险。

采用LeSS之前，证券团队被要求采用某个核心构建组件。例如，所有的数据存储交互都使用一个内部的专有的框架，将应用程序从数据存储功能中抽象出来。该API层的代码不公开，由某个专门团队所有。结果，任何团队发现bug或需要变更的话，都要追着专门团队来优先安排工作然后等待（通常很久）下个发布周期。

但是，以特性团队采用LeSS后，以及更多内部开源或集体代码所有制方式，采取了更加进步的立场。一些核心公共组件仍然由专门团队构建，但与产品特性团队建立合作关系。代码库是内部开源的，任何人都被鼓励贡献和发展核心组件。全面的自动化测试会运行预提交，确保所有团队都能在主干上有信心地工作，不会破坏关键功能。核心团队有一个组件守卫(component guardian)来培训其他开发者，并关注代码和设计的质量。结果，消除了等待这种精益浪费，和对旧有实践的一些失望。

绝大多数团队成员拥抱了变化。一个团队成员之前是专门的测试人员，两个Sprint内，她也做了一些普通的编程，提交了一些简单的Java变更（通常是通过结对编程），因此发展了第二技能，另外也对测试有长期帮助。现在她的团队具有了灵活性，一个成员不仅能测试，也能执行开发任务，使他们能比之前更快地交付，因为在任务上互相帮助可以带来了灵活性。

与此相关的是在HR政策上也有所改变，由于消除了多种特定于职能的头衔，比如业务分析师、测试者等。相反，遵循着Scrum指南，新的头衔是更加宽泛的（产品）开发者，人们被鼓励发展多种技能。

结果，集团内的许多开发者都说这是他们工作过的最开心和最有生产力的环境。

## 消除一线经理的角色

某些情况下，一些最好的开发者被“提升“为一线经理角色或“团队主管”。尽管之前设想的是团队主管仍然有50%的时间做技术工作，但实际上他们大多数的时间都被传统的日常管理任务消耗掉了，比如：

![](http://res.uperform.cn//less-jpmorgan1.png)

这意味着他们不能亲自花时间为客户做出创造价值的工作（开发软件特性），然而讽刺的是，他们很强的动手技能却往往是他们被提拔的原因。

由于真正的Scrum团队是没有团队主管的自组织团队，这个角色就不存在了。

曾经的团队主管被鼓励加入特性团队成为普通成员，许多人很享受这个机会能回去全职工作在他们最擅长的地方：分析、编码和测试！

为了帮助消除职业生涯的顾虑，管理层制定了一个强有力的沟通和补偿方案：现有经理被鼓励转变为实际的价值创造者，而这样做会得到将会有丰厚的报酬。

有趣的是，就算是一些曾是优秀开发者（但后来做了管理工作）的执行总监（在银行中相当高的职位），也返回到实际开发中，那是他们最有成就感的工作。这些变动得到公开的认可和丰厚奖励。

另一方面，一些试图返回团队的管理人员不能（或不愿）转变为基于协作的团队行为，仍然试图指挥其他团队成员或试图做决定，而那些决定现在交给了来自业务的产品负责人。在少数情况下，那些人最终离开了组织。

集团内一个副总裁在采用LeSS之前曾是一线经理，在新模式下作为一位团队成员成长起来。如今回到价值工作，不再背负管理责任的重担，他获得了团队同事和产品负责人的尊敬；他鼓励学习型文化，在特性团队中承担起交付产品关键特性的重担。结果，他被提拔为执行总监，但仍然是一名实际动手的团队成员。

宽泛的组织目标是建立一种文化，对实际的长期开发者给予丰厚报酬，而不是创造更多的经理职位。

## 资源线教练浮出水面

其他的技术经理被鼓励成为资源线教练，从命令与控制思维转变到教练与指导思维。没有了一线和职能经理，组织明显地扁平化了。结果，每个资源线教练都能负责（从HR的层级视角来看）高达100个雇员。

![](http://res.uperform.cn//less-jpmorgan2.jpg)

理论上，资源线教练的特征、行为和职责如下：

*   用教练与指导思维替代命令与控制思维和行为。
*   不再负责项目和特性交付。特性团队自管理来交付由产品负责人排列好优先级的“完成”条目。
*   帮助团队最大化地产出客户满意度和产品质量。
*   个人发展集中于：

    *   建立和维护精益思想和他们的技术与领域技能。
    *   帮助他们的团队专注于通过停止与修复(Stop and Fix)行为，以及改进实验来持续地改进。
    *   实地查看并帮助(Go See and Help)，离开单独的办公室，与团队坐在一起。
    *   引导与命令；影响与强制；指导与评估。

这在传统银行的文化中不啻为一场地震，过去命令与控制的层级中经理们习惯于做决定、给出命令、并且负责“履行合同”。

我们知道了为什么之前的“Scrum-But”并没有真正地改变文化，因为旧式的经理结构和专家职位并没有改变。但是这次更接近真正的Scrum变革中，管理层真正地改变了结构和政策，然后我们看到了更加深刻的文化改变。简而言之，真正要改变文化和行为首先需要改变系统。

并非所有资源线新教练都能自然地融入新角色。有些人喜欢过去的职责并确实在其中成长——包括资金和相应的财务计划、交付优先级，以及利益干系人管理。由于转型到Scrum，这些职能与他们不再有关，这些职责现在属于业务方的产品负责人。

在某些情况下，可以支持不适应新变化的经理们转到其他集团中，那里仍然遵循传统的、按照命令与控制来管理的瀑布开发方式。

## 团队来做重要决定

采用接近真正的Scrum之前，无论是非Scrum还是“Scrum-But”的情况，技术经理通常都会指派团队主管或项目经理来指挥团队。但是在Scrum中，自管理团队是扁平的，团队被鼓励做出重要的团队相关的决定——比如招聘和辞退——为他们自己。

下面照片所示的例子中，在马尼拉分支的Scrum特性团队较晚（但渴望）采纳LeSS，他们指出全职服务型领导者Scrum Master的必要性，并向Matt提出这个障碍。他们组织了一场对话，团队可以访谈和选举（或叫“招聘”）全职Scrum Master，而不是在传统命令与控制的管理系统中指定Scrum Master。

集团中大多数的重要决定都已类似方式来制定，由自治的自管理团队来做出重要决定。

![](http://res.uperform.cn//less-jpmorgan3.jpg)

## 结论

在证券部门发生了某些根本的组织变革，接近了真正的Scrum组织。

我们见到特性团队的产生可以减少工作交接、上下文切换、延迟、关键人风险、僵化和单一技能的瓶颈。大多数都与消除精益浪费有关。

消除一线经理角色使得熟练的开发者返回到直接产生价值的工作中，在那里他们可以专注于下一批客户特性，并进行更多的技能承传。

对于小的紧急需求，交付周期得到度量并有所改善。

对于大型需求，发布燃烧图给进度和发布计划带来了透明性。每个Sprint中软件都会集成到产品中，因此模糊和不可预测的合并、集成和测试阶段都已成为过去式。

现在有了业务方产品负责人的参与，基于检验与适应来做响应式的计划，工作在更小粒度的需求上，集团现在可以花更多时间在最具业务价值的工作上。

资源线教练学着支持和指导，而不是发号施令。

团队被授权做出重要决定，无需上级的审批。

对于最有价值的成功要素：客户满意度来说，现在衡量它还为时过早。这些深层次的结构变化可能会花上几年才能变得稳定，并有效地融入新文化中。但现在已经有一些令人鼓舞的早期信号：敏捷性（灵活性，变更成本降低）提高和质量改善。

最后我们引用新加坡证券团队某位成员最近的评论：“我很享受，我无法回到过去的工作方式了。团队很高兴，投入到进化和改善产品中，能够专注于向客户交付价值。”

**查看英文原文：**[Large Scale Scrum (LeSS) @ J.P. Morgan](http://www.infoq.com/articles/large-scale-scrum-jomorgan)