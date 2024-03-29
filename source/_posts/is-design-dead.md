title: 设计已死？ (Is Design Dead?）
categories:
  - agile
  - engineering
tags:
    - design
---

对于很多初步接触极限编程（Extreme Programming, XP）的同学来说，XP似乎宣告了程序设计的死亡。不仅限于很多设计行为被嘲笑为“冗余的前期设计”（Big Up Front Design），而且连像UML，灵活的框架（Framework），甚至连模式（patterns）这些设计技巧都被轻视乃至被完全忽视。实际上，XP中包括很多的设计，只是不同于以往软件开发流程中的做法。XP通过允许进化的实践技巧使演进式设计（evolutionary design）成为一种可行的设计策略。它还为设计人员（Designers）提供了新的挑战与技巧，令他们去学习如何使设计简单，如何利用重构保持设计的整洁，如何在一个演进的形式下使用模式。

<!--more-->

——————很帅的分割线——————


作者：[Martin Fowler](http://www.martinfowler.com/)


2004年5月发表


这篇文章是我为XP2000大会上的基调性发言所准备的，这文章的原始版本已经作为大会的一部分发表。


——————很帅的分割线——————


XP对很多软件开发方面的所谓常识（common assumptions）都提出了挑战。其中争议最大的就是它拒绝在前期设计阶段投入主要的精力，并且更倾向于一种演进式的方针。对于XP的批评者来说，这分明就是退回了“编了改”（code and fix）时代——经常讽刺为堆烂代码。对于XP的粉丝来说，这就成为了拒绝一切设计技巧（像UML），原则，还有模式的宣言：前期设计个毛啊，如果你跟着你的编码感觉走，一个好的设计就出来啦。

&nbsp;

我发现我正处在这些争论的中心。我职业生涯中的大部分时光都在与图形设计语言（像统一建模语言 UML，我是它的先行者）和模式打交道。而且我确实还写过UML和模式的书。那么，我投向的XP革命的怀抱意味着我放弃了我所写的那些题材么？意味着我已经从我的头脑中洗去了之前的那些“反革命（counter-revolutionary ）”论调么？

&nbsp;

嗯……我不是个喜欢卖关子的人，给大家一个简短的答案：No。长的呢，请听我慢慢讲来。

&nbsp;

**规划式（Planed design）和演进式设计(Evolutionary design)**

&nbsp;

我将在这里描述两种软件开发中的设计流程。估计其中比较常见的就是演进式设计(Evolutionary design)。首先，演进式设计意味着系统的设计随着系统的实施而进行。设计就是开发过程的一部分，并且程序变了，设计也随之改变。

&nbsp;

一般的使用中，演进式设计意味着一场灾难。设计以一堆即兴的决定（ad hoc decisions）而组成，每一个决定都使我们的程序越来越难以应对变化。你或许会从多个方面来说这根本就是没有设计，不过一般来说它确实是由一个贫乏的设计导致的。就像Kent说的那样，设计是用来帮助你在很长的一段时间里更容易应对软件的各种修改。而一个贫乏的设计带给我们的是，你难以有效的应对各种变化。随着时间的推移，设计越来越难以满足要求。这不单导致了软件更加难于应对变化，还使得Bug更容易产生，更难以被找到和安全的解决。这就是“编了改”（Code and fix）噩梦：虫虫（Bug）们的生长导致了随着项目的推进，修改它们的成本将会呈指数级增长。

&nbsp;

规划式设计（Planed design）则相反，它包含从其他各种工程角度得来的一些主张。如果你想造个狗屋，你只需要找点木材然后心里有个大概印象就可以了。那么如果你想盖个摩天大楼的话，你肯定不能用同样的方法，否则你还没盖到一半的时候它就塌了。所以你开始画工程图，在就像我老婆位于波士顿市中心的单位那样的工程办事处中把它搞定。随着她完成设计，她就解决了所有的问题，一些用数学分析的方法，更多的使用建筑法则。建筑法则是一堆以往建筑方面的有效经验积累出来的规则（有些是基于数学的）。当设计搞定了之后，她所在的工程公司可以将设计交付至令一家公司去搞实施。

&nbsp;

规划式设计也遵循同样的方式。设计人员事先考虑那些重大问题。因为他们不做软件的实施，所以他们不写代码，他们只做设计。所以他们可以用设计工具，比如UML，帮助他们摆脱编码细节而更多的关注抽象层面。当搞定了设计，他们可以把它交付给另一个独立的小组（甚至另一家公司）去实施。因为设计人员们从很高的层面去思考，所以可以避免一系列局部决定导致的混乱局面。程序员可以遵从设计指引的大方向去实现一个良好实施的系统。

&nbsp;

从70年代开始，规划式设计方针已经普遍运用，而且很多人都接触过。比起通常以“编了改”形式进行的演进式设计，它体现了很多方面的优势。但是其中存在着一些错误。首先就是你不可能对开发阶段即将遇到的所有问题进行预判，从而不可避免的在开发的时候就会发现一些对设计产生的质疑。那么如果设计人员搞定了设计之后就去了别的项目组，怎么办？开发人员开始迁就设计进行编码，混乱如此产生。即使设计人员没有闪人，仍然需要时间去理清设计问题，改变设计图，然后再修改编码。时间的压力和一些不按规矩办事图省事的同学还是会导致混乱的产生。

&nbsp;

此外设计人员和编码人员两者常常有些“文化”问题。设计人员之所以成为设计人员是因为他们技能优秀而且经验丰富，但是他们花了那么多时间去搞设计从而就没有时间再搞编码了。软件开发业使用的技术和工具高速变化，如果你不再编码那么你失去的不仅仅是这些技术最新的变更，还有实际从事编码人员对你的尊重。

&nbsp;

在建筑领域，也有实施者和设计者之间的这种矛盾，但是在软件业，这种矛盾体现的更为激烈。因为，建筑业和软件业有一个关键的区别。建筑业中，设计人员和实施人员的技能领域界限很明确。但是软件业中则不是。在高度设计的环境中工作的开发人员需要很优秀的技能，优秀到足以质疑设计人员的设计，特别是设计人员一天不如一天知道开发平台的具体细节的情况下。

&nbsp;

即便我们说这些都可以被解决。我们或许可以搞定设计与开发之间的情绪问题，我们或许可以找一堆各方面都很优秀的设计人员，并且组建一个严格遵守流程的团队在改变代码的同时改变设计图。问题仍然存在：需求变更。需求变更是我在软件项目中遇到的第一大头疼问题。

&nbsp;

解决需求变更的其中的一个方法是在设计中加入灵活性，从而当需求变更时你可以很快的改好。Ok，这是在你的设计预期中发生了需求变更的情况。设计的确可以应对一定范围的变化，但是也只能覆盖可以预判的变化范围，然而对于不可预判的变化则难以覆盖（甚至会起反效果）。那么你必须非常充分的理解需求去预判所有的可变因素，个人认为这真的非常难。

那么我们来说就算其中有些需求问题可以归咎于没充分理解需求，所以有些人专注于需求分析过程，希望通过获取理解的更充分的需求来避免以后对于设计的冲击。但是即使这样也不一定是解决问题的方法。很多未预期的需求变更来自于业务本身的变化。所以根本没法避免，无论你多么注意你的需求分析流程。

&nbsp;

那么规划式设计似乎是不可能的。它的确面临着巨大的挑战。但是我不倾向于说规划式设计次于通常以编了改方式进行的演进式设计。比起编了改，我确实更支持规划式设计。我只是发现了规划式设计所面临的问题，并且在寻求一个新的方向。

&nbsp;

**XP使抚平曲线可行的措施 **

&nbsp;

XP因为诸多原因受争议，但是这些争议中的其中之一便是XP是提倡演进式设计而不是规划式设计。如我们所知，演进式设计因为其诸多的即兴设计决定（ad hoc design decisions）与伴随而生的混乱局面，从而难以被采纳。

&nbsp;

这个争议的核心问题与软件变化曲线相关。变化曲线表现为：随着项目的进行，为单一变化所付出的代价承指数增长。这个曲线通常简短的用一句话代表“系统分析时花一块钱可以做出的改变，在投入实施后则需要1000倍的代价”。这句话印证了很多项目仅有一些即兴的流程而缺乏设计环节导致的问题，但是这种指数式增长的趋势却函待我们解决。这种承指数趋势增长的曲线意味着演进式设计不可行。同时也表达了规划式设计一定要小心的进行，因为任何错误都有可能导致同样的指数上升曲线降临在规划式设计的身上。

&nbsp;

XP的基础假设是它可以将变化曲线抚平以支持演进式设计的开展。XP使抚平曲线成为可能，同时也利用了这条平整的曲线。这是XP实践做法的耦合中的一部分：特别是你如果不采取抚平曲线的措施便无法对平整的曲线加以利用。这也是XP所产生争议的来源。很多人不懂得XP如何将曲线抚平便批评XP利用变化曲线的做法。通常这种非难来自批评者自身的经历，他们没有采取抚平曲线的做法便开始利用变化曲线。结果他们被搞得焦头烂额，此后再看到XP，他们就跟看见火一样。

&nbsp;

使抚平曲线可行的措施包含很多部分。其中核心的包括：测试措施，持续集成。离开测试所带来的安全性，那么XP的其他措施几乎无法实现。持续集成使团队保持同步，所以你可以进行更改而且不用担心与其他成员整合所导致的问题。这两种措施结合对于变化曲线所带来的影响是巨大的。在ThoughtWorks工作的这段时间再一次给予我这方面的印证。你需要实现所有的这类措施以完全终结你对XP的质疑。

&nbsp;

重构也有同样的效果。严格按照XP建议的那样重构他们代码的人会发现他们比那些松散随意的所谓重构代码的人在效果上有重大的区别。这正是当Kent告诉我如何确实的重构时我所经历到的。最后，这个巨大的变化驱使我写了关于它的整整一本书。

&nbsp;

Jim Highsmith，在他出色的XP概要中，用了一系列参照物进行类比。其中之一是规划式设计，另一个是重构。传统的项目实施方针中规划式设计起主导作用是因为我们假设我们不能在稍后改变我们的主意。然而当变化的成本降低后，我们可以将更多的设计当做重构推后再做。我们没有完全抛弃规划式设计，但是我们找到了两种设计方针的平衡。对于我来说，当我应用重构之前，我就好像只用了一只手来做设计。

&nbsp;

这些使抚平曲线可行的措施包括持续集成，测试，重构提供了使演进式设计可行的新环境。那么还有一件事我们没有搞清楚，那就是平衡点在哪里。我确信，尽管外观如此，但是XP绝不仅是测试，编码，然后重构。在开发前我们仍然为设计留有空间。一些发生在整体编码以前，更多的时候发生在某个迭代周期即将为某个特定任务开始编码之前。但是前期设计与重构之间是有一个新的平衡点的。

&nbsp;

**简单性的价值 **

&nbsp;

两条XP最伟大的口号就是“做可能有效的最简单的东西”和“你不会需要他”（简称YAGNI）。两条都宣告着XP的简单设计实践原则。

&nbsp;

YAGNI通常被解释为：你不应该在今天为一个只会明天才会需要的功能添加任何代码。表面来看它很简单。问题就来源于那堆架构，可重用组件，灵活的设计。这些东西很难建立。你用了额外的前期代价去建立它们，并且寄希望于在后期收回前期的成本。这种在前期构建灵活性的做法被看做有效的软件设计的关键。

&nbsp;

尽管如此，XP建议你不要为第一个需要这个功能的情况建立一个灵活性的组件，而是让这些结构在必要的时候自己成长。如果我今天需要建立一个Money类，它只需要处理加法操作而不是乘法操作，那么我就只添加加法操作进入这个类。尽管我确定我在下一个迭代周期需要乘法，也明白它多容易实现，并且觉得它能很快的被加上，我还是会把它放在下一个迭代周期中。

&nbsp;

这其中的一个原因是经济考虑。如果我必须考虑下一个迭代周期才会用到的功能，那么意味着我不能全力完成这个周期的功能。发布计划表明这个功能需要在这个周期完成，那么在这期间去做未来才应该完成的事情就是违背开发人员与客户达成的共识。这可能对这个周期的任务准时交付造成风险。就算这个迭代周期不存在风险，那也是客户来决定什么样额外的工作应该被完成——仍然也不一定包含乘法功能。

&nbsp;

这种经济负面影响是伴随我们预判错误而来的。尽管我们很确定这个功能是怎么运作的，但是我们还是有可能搞错。特别是当我们还没有搞懂详细的需求的时候。提早完成错误的工作比起提早完成正确的工作来说是一种更大的浪费。同时，极限编程专家（XPerts）通常认为我们搞错的时候比对的时候多。（我同意这个观点）

&nbsp;

采用简单设计的第二个原因是复杂的设计比简单的设计难以理解。所以说添加复杂因素使任何对于系统的修改变得困难起来。这增加了我们往系统中添加复杂设计的时点与我们真正需要他们的时点之间的成本。

&nbsp;

如今这些建议给很多人以一种胡说八道的印象，而他们这么想是对的，想象一下还没有XP措施并且软件变化成本曲线上扬的传统开发方式吧。无论如何，当规划式设计和演进式设计之间的平衡被打破时，YAGNI成为了最好的实践措施（没有之一）。

&nbsp;

综上所述，你不需要去为一个以后才会需要的功能花费精力。就算它的成本是零，你仍然不需要添加它，因为尽管他的添加成本是零，但是它增加了我们修改的成本。不管怎么说，当你使用XP或者其他一些可以降低变化成本的措施时，你应该灵活的按此约束自己。

&nbsp;

**这地球儿上啥才是简单的呢？ **

&nbsp;

那么我们希望我们的代码尽可能的简单。这好像是不用争辩的，毕竟有谁希望把事情搞得很复杂呢？但是我们需要一个问题的解答：什么才是简单？

&nbsp;

在XPE中，Kent给出了关于一个简单系统的四个标准。排序如下（重要在前）：

&nbsp;

n  运行过所有测试

n  表达所有意图（Reveals all the intention）

n  无任何重复（No duplication）

n  最少的类和方法

&nbsp;

运行过所有的测试是一个挺简单的标准。无任何重复这条也挺直观的，尽管很多程序员需要一些指导才能做到。表达所有意图这条最讨巧，它到底意味着什么？

&nbsp;

这条最基本的价值体现在代码的声明上。XP倾注了很大的价值放在代码的可读性上。在XP中“耍小聪明的代码”（clever code）是一种被众人鄙视的做法。但是一个人意图清晰的代码往往对于另一个人来说就可能是耍小聪明的代码。

&nbsp;

Josh Kerievsky 在他XP2000的报告中给出了一个很好的例子。他提到了估计是最广为人知的XP代码——JUnit。JUnit使用了装饰模式（decorators）以为测试用例添加可选功能，比如并发同步还有批处理代码。通过将这些代码以装饰模式封装，可以保证本来的测试代码比不这么做更加简洁。

&nbsp;

但是你必须问问自己，最终代码是不是足够简单。对于我来说是的，但是我很熟悉装饰模式。但对于很多人来说理解它很困难。同样的，JUnit还使用了插入式方法（pluggable method），我注意到对于很多人来说一开始接触就除了迷茫就是迷茫。那么我们可不可以下这样的结论，JUnit的设计对于有经验的设计人员来说是简单的，但对于没什么经验的人来说是复杂的。

&nbsp;

对于终结重复来说，XP的“一次，就一次”（Once and Only Once）和实效性程序员（Pragmatic Programmer）的DRY（Don't Repeat Yourself）都是很好很强大的建议。遵循这些可以让你受益很长时间。但是这些不是全部，并且如何简化我们的代码仍然是很难追寻的。

&nbsp;

最近我接触了一个很可能是过度设计（over-designed）的系统。它被我们重构后，很多灵活性方面的功能都被移除了。其中，我注意到一个程序员说：“重构一个过度设计的系统比重构没有设计的系统容易多了。”所以说设计简单点固然好，但是复杂一点也不至于死人嘛。

&nbsp;

我听过这方面最好的建议来自Bob大叔（Robert Martin）。他建议不必太过在意设计是不是达到了最简。反正最终你可以，或者可能，或者说有意愿去重构它。所以说，我们可以下这样的结论，重构的意识远远重要于知道什么是最简单的事情。

&nbsp;

**重构是否与YAGNI冲突 **

&nbsp;

这个主题来自于最近的XP邮件列表，有必要再次提及以便我们认清设计在XP中的角色。

&nbsp;

这个问题基本上来说是这样的，重构需要些时间，但是不增加任何功能。然而YAGNI表示我们应该为当下而做设计，而不是为了以后做设计。它们是有冲突的吗？

&nbsp;

YAGNI认为你不必为当前阶段不需要的复杂性埋单，这是实现简单设计的其中一个措施。重构需要被用来保持设计的简单性，所以你有必要在你认识到有更简单的方法时重构。

&nbsp;

简单设计既利用了被XP的抚平的变化曲线，同时又可以反过来起到进一步抚平的作用。只要你使用了测试，持续集成，还有重构，你就可以高效的应用简单设计措施。但是你在应用简单设计的同时必须保证变化曲线的平整。除非你预判正确，否则任何非必要的复杂性都会在各方面增加你系统变化的难度。人类其实是不擅长预测的，所以我们还是应该专注于简单性。尽管我们不会在第一时间做到最简，但是我们需要通过重构来最终接近并达到这一目的。

&nbsp;

**模式与XP**

&nbsp;

JUnit的列子不可避免的让我谈到了模式。模式与XP之间的关系很有意思，而且也是一个很普遍的问题。Joshua Kerievsky曾论证过模式在XP中未受到足够的重视，并且他的论证很充分，我就不重复了。但是需要注意的是很多人认为模式是与XP冲突的。

&nbsp;

这个论题的中心在于模式常常被过度使用。世界上传说级的程序员太多了，刚刚读“四人帮”（GOF，Gang of four）的时候，他们曾经在32行代码中用了16种模式。我记得一天晚上，享受着啤酒，和Kent一起翻看一篇叫做“不是模式：23个简单技巧”时，我们琢磨，那些东西与其用策略来实现还不如用一堆if。这个玩笑说明，模式常常被过度使用，但是模式始终不是坏东西。关键是你怎么用它们。

&nbsp;

其中的一个说法是：简单设计原则的影响会引导你使用模式。很多重构行为印证了这点，即便你对模式没概念，只要你遵循简单设计原则你会不知不觉的使用模式。即便真的是这样的，但是这真的是最好的方法么？当然还是你心理大概有个方向并且通过一本书来帮助你解决问题比你去发明一个什么做法要好些。我确实仍然在我想到一个模式的时候就去看看四人帮（GOF）。对于我来说一个有效率的设计关注的是我们必须知道一个模式的价值所在以及是否值得一用。同样的像Joshua建议的那样，我们需要渐渐熟悉如何让模式融入设计。XP只是对于模式的应用与他人不同，但绝对没有否认它们的价值。

&nbsp;

但是当读取一些邮件的时候，我有一种相同的感觉，那就是很多人认为XP不鼓励用模式，尽管讽刺的是这些建议者中有很多人也是使用模式的引领者。是因为他们已经超脱了模式了吗，或者因为模式已经太深入他们的思维方式致使他们意识不到它了?我不知道其他人的答案，但是我觉得模式仍然是至关重要的。XP或许是一种开发过程，但是模式是设计知识的脊梁，是无关你使用的开发过程的宝贵知识。只是不同的开发过程会按照不同的方法使用模式。XP既强调在你需要模式之前不要使用它，也同时强调通过一个简单的实现进化你使用模式的方法。但是模式仍然是需要获得的关键知识。

&nbsp;

我对于极限人（XPers）使用模式的忠告是：

&nbsp;

n  为学习模式投入一些时间。

n  关注在开发过程中的什么时候该使用模式。（别太早）

n  在一开始关注如何以最简的形式实现模式，然后再加入复杂性。

n  如果你使用了一个模式，然后你又发现它不值得使用，毫不犹豫的把它搞掉。

&nbsp;

我认为XP应该多强调一些对于模式的学习。我不确定如何把它加入到XP的措施中，但是我确信Kent会想到好办法的。

&nbsp;

**培育架构 **

&nbsp;

听到软件架构（software architecture）我们想到什么？对于我来说，架构表达了一个意思，那就是系统的核心元素，最难去改变的那部分。一个基础，系统剩余的部分必须构建于其上。

&nbsp;

当你使用演进式设计的时候，架构扮演了一个什么角色？XP的批评者们又要说，XP忽视架构，XP只是赶紧去编码然后相信重构可以解决一切设计问题。有趣的是他们对了，这很可能是个弱点。当然，一些巨猛的XP专家——Kent Beck， Ron Jeffries，还有Bob Martin正在越来越多的把精力放在避免前期架构设计上（up front architectural design）。别把数据库牵扯进来直到你真正需要它。先用文件存然后在未来的一个迭代周期中重构成数据库。

&nbsp;

相对来说我被认为是一个胆小的极限人，对于这个看法我表示不同意。我认为一个扁平化的前期架构是有必要的。就像那些初期决定的如何划分应用层次，如何与数据库打交道（如果你需要），为了与Web服务器交互制定什么样的方针。

&nbsp;

最主要的是我认为很多这类领域属于我们这几年一直在学习的模式。随着你对模式知识的增长，你应该有一个合理的关于如何使用模式的初步想法。我的观点与反对任何前期设计的人们的关键区别是我的这些架构设计不希望为项目添加任何顽疾，而他们的观点则是团队尽管知道一些早期决定有可能带来一些错误，他们还是应该有勇气去面对它们。有个人告诉我一个故事，一个项目，快部署了，决定不用EJB了，希望把EJB从系统中移除。这是一个不小的重构，他们在晚些时候做到了。项目中维护的平整变化曲线不仅使这个重构成为可能，并且让它显得相当值得一做。

&nbsp;

如果这个事情用另一种方法做会怎样？如果你决定不用EJB，晚些时候再添加它好不好？你应不应该不用EJB直到你发现缺了它不行？这是一个包含很多因素的问题。的确不用复杂的组件增加了简易度并提高了效率。不管怎么说，总之除掉点什么东西总比添加他们要容易。

&nbsp;

所以我的建议是：开始的时候看看架构大概是个什么样。如果你看到大量的数据被N个用户访问，那么第一天开始就用数据库吧。如果你看到了复杂的业务逻辑，那么用领域模型吧。当怀疑是不是在简单性方面有错误的时候，就遵从YAGNI大神的旨意吧。同时准备在你一见到某部分架构没发挥作用的时候立即简化你的架构。

&nbsp;

**UML与XP**

&nbsp;

我与XP打交道后遇到的所有问题中对我和UML的联系改进最大的问题是：UML和XP是不兼容的么？

&nbsp;

这些不兼容包括几点，XP的确在很大程度上不重视图形表示。尽管官方说法是“如果它们有用就用”，但这里有个潜台词就是“真正的极限人不用图形”。而且像Kent那样的人对于图什么的不太感兴趣就更加为这点提供了有力论据，我的确没见过Kent自愿站出来画个设计图。

&nbsp;

我想这个问题有两个原因。一个是一部分人觉得图有用而一部分觉得没用。危险的是那些认为有用总想那些认为没用的去画，而反之亦然。其实画图的人和不画图的人们应该彼此接受。

&nbsp;

另一个问题是设计图容易让人联想到一个复杂的过程，那种花了巨多时间而且还毫无帮助，甚至还会带来灾难的复杂过程。所以比起XP专家们说的“只在必须的时候用（，废物）”这种话，我觉得我们更应该灵活的使用图并且避免任何副作用。

&nbsp;

以下是我对于使用图的一些建议：

&nbsp;

首先时刻想着你为什么画图。画图最重要的价值在于沟通。一个有效的沟通在于选择表达重要的事而忽略最不重要的。这种选择就是使用UML的关键。不要把所有的类都画出来——只画重要的。每个类不要画出所有的属性和方法——只画重要的。不要为每一个用例和场景画时序图——只……你懂的。代码是最好的传达意图的途径，代码也是保持代码同步最容易的途径。完全通过图来传达意思是理解意图的大敌。

&nbsp;

一般来说图是在开始编码之前开展设计用的。你常常有一种印象就是做这样的事是违背XP的，但其实不是这样的。很多人说当你遇到一个棘手的任务时，还是有必要在开始的时候大家一起很快的搞个设计。那么，当你这样搞的时候注意：

&nbsp;

n  保持简短。

n  别尝试表达所有的信息（只关心最重要的）

n  不要把设计结果当做最终设计，而只是一个草稿。

&nbsp;

最后一点值得扩展。只要你搞前期设计，你不可避免的会发现某一方面的设计是错误的，并且只有在编码的时候才会发现。这不是问题，发现了就修改你的设计吧。麻烦往往来自于人们觉得自己搞定了设计，然后他们就不将代码中的反馈应用回设计中去了。

&nbsp;

改变设计不一定意味着改变设计图。画个图帮助你理解意图，之后就把这图扔了是非常有道理的。画了有用的图，那就很值得了。他们没必要留着当老古董，好的UML图不是古董。

&nbsp;

很多极限人使用CRC cards（译注：Class，Responsable，Collaboration Cards 一种图形化设计方式）。这不与UML冲突。我常常CRC与UML混起来用。使用任何对于你手中工作有价值的技术吧。

&nbsp;

UML的另一个用处是可持续维护的文档，它的通常形态是保存在一个设计工具中的模型。人们这样做出于希望可以为系统维护一个文档来帮助人们工作，其实在实践中这完全帮不上忙。

&nbsp;

n  使这些图随时更新很花时间，一方面人们渐渐将更新工作放在一边

n  令一方面这些图一般要么藏在某个设计工具中，要么就藏在一捆厚厚的文件中，所以根本就没人看。

&nbsp;

基于这些已经发现的问题，我对于持续维护文档的建议是：

&nbsp;

n  只在你可以轻松的保持图更新的情况下使用图。

n  把图放在任何人都一眼可以看到的地方。我喜欢把它们贴墙上，并鼓励人们用笔在墙上的图上做些简单的修改。

n  时刻注意人们是不是还在用它，如果没有，扔掉。

&nbsp;

UML最后一个方面的用途是作为工作移交文档，就像一组给另一个组移交工作时那样的文档。XP的观点是写这样的文档跟前述的差不多，它的业务价值由客户来决定。我们可以再一次这样说，UML在这里是有用的，有选择性的画图可以帮助人们沟通。时刻记住代码才是包含详细信息的地方，图只是体现概要与重要问题的载体。

&nbsp;

**隐喻论**（**On Metaphor**，译注：Metahpor 即System Metahpor，极限编程在设计期的一种手法，存有诸多争议）

&nbsp;

好吧，我估计也得在公共场合说说这事——我始终还是没搞清楚“隐喻”这玩意是怎么回事。我看到它挺管用，并且在C3项目中发挥了很好的作用，但是这并不代表我知道怎么去建立它，更不用说解释怎么去建立它了。

&nbsp;

XP的隐喻措施是建立在 Ward Cunninghams 的命名系统方案上的，也就是说你列出一系列广为人知的名称当做描述业务领域的词汇库。这样一个命名系统可以为你命名系统中的类和方法提供帮助。

&nbsp;

我也曾经通过领域概念模型建立过一套命名系统，那是我和一堆领域专家们用UML或者类似的东西建立的。你需要很小心的去做这件事，你必须把概念的集合尽量缩小，并避免任何技术问题掺和进来。但是如果你建立了这样一个系统，那么你会发现你可以用这些领域专家可以明白的领域词汇帮助他们与开发人员的沟通。领域模型并不完全匹配类设计，但是足以作为整个领域的一些公共词汇。

&nbsp;

现在我没有看到任何不能使用系统隐喻来建立这套词汇系统的理由，C3就用系统隐喻把薪资体系用一个流水线来表达过。然而我也没看到基于领域模型的命名系统是什么坏主意。我也不希望放弃一个对我来说很管用的获取命名系统的方法。

&nbsp;

人们常常批评XP至少得做点大体的设计，极限人常常答复：“Metaphor就是啊。”。但是我始终没有看到Metaphor被解释的有说服力过。这的确是XP需要跨越的一道坎，极限人们仍需努力。

&nbsp;

**你长大后想当个架构师（Architect）吗？ **

&nbsp;

最近的十年间，“软件架构师”（software architect）一词变得流行起来。对于个人来说这是一个很难使用的术语。我老婆是一个建筑工程师（structural engineer）。工程师与建筑师（architect）之间的关系有点……有趣。我比较喜欢的就是“建筑师擅长3个B：灯泡（bulbs），灌木丛（bushes），鸟（birds）”。意思是：建筑师画出这堆好看的图片，但是必须去确保它们能够站住脚的是工程师。结果就是我尽量避免使用软件架构师这样的名词，毕竟连我老婆都不能给予我的职业以尊重的话，我又怎么才能混得下去呢。

&nbsp;

在软件业，架构师（architect）意味着很多。（在软件业很多名词都意味着很多。）一般来说，它代表一种权威，就像有人说“我不仅是个小程序员——我是个架构师”。这个或许可以翻译成“我是个架构师了——我如此重要就不应该再做编码了”。问题就转变成了，当你想锻炼自己的技术领导力的时候是否应该把你自己从普通编码中抽离出来。

&nbsp;

这个问题产生了非常大的情绪。我看到很多人对于他们不再能成为架构师这个角色感到非常愤怒。“在XP中竟然没有企业级架构师这样一个位置。”，这就是我常常听到的哀号。

&nbsp;

就设计这个角色本身来说，我也不认为XP不重视有经验或者好的设计技能。确实有很多XP的支持者——Kent Beck，Bob Martin，当然还有Ward Cunningham——我从他们身上学了好多关于设计的知识。尽管他们为人熟知的技术领导者角色的确发生了变化。

&nbsp;

举例来说，我来介绍我们ThoughtWorks的技术领导者之一：Dave Rice。Dave已经经历了N多软件生命周期并且非官方的在技术层面上罩着一个50人左右的项目。他领导者的角色意味着他花了好多时间和全部的同志们在一起。他会在同志们需要帮助的时候和他们工作在一起，并且四处寻找谁需要帮助，他坐的地方就是我们的旗帜。作为ThoughtWork的长期合作者，他可以坐在任何办公室中，只要他喜欢。他曾经和Cara（我的发布经理）合用一间。结果在过去的几个月里他又搬了出来，和我们的程序员们一起在开放办公区工作（或者XP喜欢用的“作战室”）。这对他很重要，因为这样他可以看到事态的进展，并且随时可以在必要的时候做点什么。

&nbsp;

熟悉XP的人们会发现我正在描述XP中的一种角色：教练（Coach）。的确，XP做的文字游戏之一管技术领导者叫教练。意思很清楚：XP的技术领导力应该体现在培训程序员并且帮助程序员做决定上。这是一个需要良好的沟通能力同时也需要良好的技术能力的角色。Jack Bolles在XP 2000大会上表示：“独孤求败”以后将越来越难有立足之地。合作与培训是成功的关键。

&nbsp;

在一个会议晚宴上，Dave和我与一个XP反对者有一个谈话。随着我们讨论我们以往都做过什么，我们发现我们在方针上有很多共同点。我们都喜欢适配，迭代开发。都认为测试是很重要的。我们开始猜测他为什么会这么强烈的反对XP。他说了类似这样的一段话“我最不希望程序员们重构并且瞎搞我的设计”。一切都清楚了，就像Dave之后跟我说的那样“如果他不信任他的程序员，那当初为什么还雇佣他们？”。XP中有经验的程序员能做的最重要的就是：把尽可能多的技能传授给新手程序员。我们采用教练培训程序员们如何做重要的决定，而不是找个架构师独自决定所有重要的事。就像Ward Cunningham指出的那样，通过释放分发他的技能，带给项目组的比“独孤求败”更多。

&nbsp;

**可逆性**

&nbsp;

XP 2002大会上Enrico Zaninotto发表了一个吸引人的讲话，讨论了敏捷方法与精益生产间的捆绑。他的观点是这两个方针的关键方面是它们都通过减少过程中的不可逆性来踢走复杂性。

&nbsp;

在这个观点中阐述道：决定的不可逆性是复杂度的主要来源。如果你可以容易的改变你所做的决定，那么它们是否正确就不那么重要了——这可以让你的生活更简单些。演进式设计的结果就是设计者需要思考他们怎么去避免他们决定中不可逆的部分。比起立即做出正确的决定，宁可找个方法等等再做决定（当你已经获得了更多的信息时）或者做那种以后逆转起来没多大难度的决定。

&nbsp;

这种支持可逆性的决定使敏捷方法在源代码控制系统上放置了很多关注，并且把所有的东西全放到这样一个系统上去。尽管这并不保证可逆性，特别是那种长期决定，但它的确提供了一个基础，为团队带来了信心，尽管它很少被使用。

&nbsp;

为可逆性做设计也意味着一个让错误更早体现出来的流程。迭代开发的价值之一就在于快速的迭代可以允许客户看到系统的成长，并且如果在需求分析时期出现错误，也可以在修改的成本高到不可能修改之前发现并修改它。这种快速发现的做法对于设计也很重要。这意味着你需要先搭起个架子来，这样潜在的问题区域会在一次次的测试中暴露出问题。它还意味着做一个试验来看未来的修改有多难也是值得的——尽快的在分支系统上搞一个用完即扔的原型。一些团队已经有尝试在原型上做变更来预测变更的难度的报告。

&nbsp;

**设计的意愿**

&nbsp;

尽管我在这篇文章中专注于多种技术实践，但是有一件事是最容易遗忘的，那就是人的因素。

&nbsp;

为了正常的工作，演进式设计需要一种力量来使它整合。这种力量只能来自于人——一个有决心使设计质量保持在高水准的人。

&nbsp;

这种意愿不必来源于所有人（尽管如果是的话那样最好），通常在团队里仅有一两人负责整合设计。这就是通常叫“架构师”的那个人手底下的活。

&nbsp;

这个职责意味着对代码持续的监控，看看有没有什么地方正在变乱，然后在事态失去控制前尽快采取行动纠正它。设计的保证者不一定是修正问题的人——但是他们必须确保有人去搞定这个问题。

&nbsp;

设计意愿的缺失看来是演进式设计失败的主要原因。就算人们无论多么熟悉我在这文章里所说的那些事情，如果缺了意愿，那么不会有任何好的设计。

&nbsp;

**难以重构的部分**

&nbsp;

我们可不可以用重构处理全部的设计决定，或者说，有没有一个普遍的问题很难被后期添加？这时，XP的正统说法告诉我们，所有的事情都是只要你需要就容易被添加的，所以YAGNI总是灵光的。我怀疑是否会有例外。一个在是否能后期添加存在争议的好的例子就是国际化。这是不是一个后期添加会很痛苦，所以应该在一开始就考虑进去的事情呢？

&nbsp;

我可以很快想象一定有一些事可以归入这个分类，可是现实是我们掌握的数据太少了。如果你必须去添加什么功能，像国际化，在后期你往往非常清楚它所花费的工作量。你不清楚在需要它之前添加它并且维护它日复一日所付出的工作量。你也不清楚你是否搞对了正确的需求，然后还是要重构。

&nbsp;

YAGNI认为很多这种潜在需求最终被发现是不需要的，或者至少不是按你期待的那种形式被需要的。通过不做它们，你会节省很大的工作量。尽管将一些简单解决方案重构为你真正需要的那样需要一些工作量，但是这种重构仍然比构建所有存在问题的功能的工作量要少。

&nbsp;

另一个需要我们记住的问题是你是否知道如何做它。如果你做过几次国际化，那么你会知道应用何种模式。那样你八成能把它做对。这样的话添加预测性的结构或许比你刚接触这个问题就开始预测更好。所以我的建议是：如果你知道如何做这件事，那么你就处在一个可以判断这件事是现在做还是以后做的高度。那么如果你没做过这件事，你不仅无法计算它的成本，估计你也无法做好它。这样你就应该稍后再添加它。如果你已经添加，并且吃了些苦头，那么你大概就知道你晚些添加这个功能可能感觉好的多。因为你的团队经验更丰富了，你对领域更熟悉了，你也更熟悉需求了。往往你在这个时候往回看的时候，你会有一个完美的认识。过早的添加它可能比你想象的还要让你难受。

&nbsp;

这就关联到一个阶段排序的问题。在 Planning XP 中，我和Kent公开的指明了我们意见的不同。Kent喜欢使业务价值成为决定阶段排序的唯一因素。Ron Jeffries在一开始是反对的，不过现在也同意了。我还是不太确定。我相信在业务价值与技术风险间是有平衡点的。这会驱使我至少将一些国际化工作提前来做来转移风险，但是也只是第一个版本发布时需要国际化时才应该这样做。让一个版本尽快发布是至关重要的。如果第一版发布不需要一些复杂性，那么在发布之后再添加它们也是值得的。一个交付后并且可运行的代码的力量是巨大的。它可以集中客户的注意力，增加信任，还是一个巨大的信息来源。做任何可能使发布提前的事。尽管在第一版发布后再添加一些功能可能更费事，但是还是尽快发布比较好。

&nbsp;

任何新技术都很自然的有其提倡者不确定一些边缘状况的情况。很多极限人被告知演进式设计不可能应对一些特定问题，但结果发现其实是可能的。这种对“不可能”的攻克引导了一种类似的情况可以被克服的信心。当然我们也不能永远如此推断，但是直到XP社区碰到一个分界并且跨越不了为止，我们永远不知道这些分界在哪里，并且尝试跨越这些其他人认为的潜在分界是正确的。

&nbsp;

（Jim Shore最近的一篇文章讨论了一些情况，包括国际化，并且最终证明了像这样的潜在边界不再是一种障碍。）

&nbsp;

**有设计吗？**

&nbsp;

演进式设计的难题之一是非常难以说清到底设计了没。设计和编码混合在一起的风险就是可能没设计就编码了——一旦出现这种情况，演进式设计就可能脱轨并失败。

&nbsp;

如果你在开发小组，那么你可以通过代码来判断是不是有设计。如果代码越来越复杂，越来越难搞，那么就是设计没有做足。但杯具的是这个观点比较主观。我们没有一个可靠的标尺来给我们一个客观的衡量设计好坏的方法。

&nbsp;

如果对于技术人员来说这种设计的“透明性”是个难题的话，那对团队中的非技术人员来说简直是要叫苦连天了。如果你是一个经理或者客户，那你怎么分辨软件是否有个好的设计？这对你来说很重要，因为一个缺乏设计的软件以后修改的成本会很高。的确没有一个简单的答案，但是有一些提示。

&nbsp;

n  听听技术人员说些什么。如果他们在抱怨修改的复杂，那么重视这种抱怨并给予他们时间去修改。

n  时刻注意有多少代码不再使用了。一个不断健康重构的项目会不断的删除一些不好的代码。如果没有代码被删除，那么这就是一个标志着没有足够的重构的信号——会导致设计的腐化。当然，就像任何其他的标尺一样，这个标尺也可能会被误用，但是一个好的技术人员会力挺任何标尺，轻视任何主观因素。

&nbsp;

**那么设计挂了吗？**

&nbsp;

绝对没有，但是设计的本质改变了。XP的设计寻求的是如下技能：

&nbsp;

n  一种持续的欲望去尽可能的保持代码的干净与简洁。

n  重构技能，那样你就可以自信的在你需要的时候来做出改进。

n  对于模式的良好认知：不光是如何使用它们，还包括理解什么时候使用它们并且如何使它们融入。

n  设计的同时留意未来的变化，要知道你现在做的决定有可能不得不在未来做出改变。

n  了解如何与那些必须理解设计的人做沟通，用代码，图，还有以上的一切：言语。

&nbsp;

这是一堆吓人的技能选择，但是成为一个好的设计者一直是比较难的。XP根本就没有让这事简单些，至少对我来说不是。但是XP的确给予我们一个新的方法去思考高效的设计，因为它已经让演进式设计再次成为了一种貌似可行的策略。而且我是演进的大粉丝——否则谁知道我会成为什么呢？

&nbsp;

**声明**


过去的几年里，我从很多好兄弟那里捡到或者“偷”到了好多好主意。而这些都沉默在我记忆的阴暗面里了。但是我的确记得我从Joshua Kerievsky那里弄到不少好主意，还有Fred George 和 Ron Jeffries也给我不少好建议，还有，我不能忘记还有多少好主意不断地Ward 和 Kent 那里涌出来。

&nbsp;

我永远感谢那些问我问题还有揪出我的错别字的人。虽然我实在懒得弄个列表出来，但是这的确包括：Craig Jones, Nigel Thorne, Sven Gorts, Hilary Nelson, Terry Camerlengo。

&nbsp;

载请附带连接，注明出处 http://www.cnblogs.com/Zetazzz/archive/2011/01/06/1926990.html

原文：http://martinfowler.com/articles/designDead.html

&nbsp;

&nbsp;