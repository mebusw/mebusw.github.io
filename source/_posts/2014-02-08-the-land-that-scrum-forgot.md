title: Scrum忘记的土地- The Land that Scrum Forgot
tags:
    - time code

categories:
    - agile
    - scrum
---

Scrum is a starting point. In fact, it’s a great starting point. But, as a framework rather than a full-blown methodology, Scrum is deliberately incomplete. Some things—such as the best technical practices to use—are left for individual teams to determine. This allows a team to create the best fit between their project and environment and an assortment of technical practices.

Scrum是一个起点。事实上,这是一个很好的起点。但是,作为一个框架,而不是一个成熟的方法论,Scrum故意是不完整的。一些措施——例如使用最好的技术实践——留给每个团队自己来决定。这允许一个团队在他们的项目和环境和各式各样的技术实践之间创建最适合自己的方式。

<!--more-->

**（这是ScrumAlliance的推荐文章，Mike Cohn转载Uncle Bob的文章，以下中文是youdao.com的自动翻译，非人工翻译。） **

&nbsp;

While that selection of practices should belong to the team or organization rather than to a group of methodologists, the benefits of some practices are becoming so compellingly obvious that they warrant consideration by any Scrum team. But, too many Scrum teams become complacent after achieving some early productivity gains with Scrum. And they stop seeking ways to improve. Many fail to try the technical practices necessary for long-term success. In the following article, Robert Martin (perhaps better known as "Uncle Bob") tells us why so many Scrum teams fail to sustain the promise of their early successes.

而选择的实践应该属于团队或组织,而不是一群方法学家一些实践变得如此咄咄逼人地明显的好处,他们考虑任何Scrum团队。但是,太多的Scrum团队变得自满实现Scrum的一些早期的生产率增长。和他们停止寻求改善的方法。许多人没有尝试为长期成功所必需的技术实践。在接下来的文章中,罗伯特·马丁(也许更好的被称为“鲍勃叔叔”)告诉我们为什么那么多Scrum团队无法维持他们早期的成功的承诺。

<span style="line-height: 1.5;">Bob’s article is the first in a series of articles we will publish in this newsletter. Because Scrum is a starting point with deliberate gaps to be filled by knowledgeable teams, we are looking to those outside the core Scrum community to provide advice to those of us within it. What do leading agile thinkers from outside the Scrum world think our teams need to know or do? I’ve asked a few to share their thoughts with us. And who better to start with than Bob Martin?</span>

鲍勃的文章是第一个在一系列的文章,我们将发布在这个通讯。因为Scrum是一个起点与深思熟虑的差距由知识渊博的团队,我们希望核心Scrum以外的社区提供建议的人。什么主要敏捷思想家Scrum以外世界认为我们的团队需要知道或做什么?我问过一些与我们分享他们的想法。谁比Bob Martin开始?

&nbsp;

**Scrum忘记的土地**

By Bob Martin


What goes wrong with so many Scrum projects? Why does the velocity start out high, but then precipitously decline? Why do some Scrum teams eventually give up on Scrum? What’s going wrong?

所以很多Scrum项目哪里出问题了?为什么开始速度高,但随后急剧下降?为什么有些Scrum团队最终放弃Scrum ?什么错了吗?


As someone who has been called in to rescue Scrum teams from this kind of demise, I can tell you that the problem is not that the teams are losing motivation. Often the problem is that the software that the teams are producing is getting harder and harder to work with.

作为一个被称为拯救Scrum团队从这种消亡,我可以告诉你,问题不是团队正在失去动力。通常的问题是软件的团队生产正变得越来越困难。


Scrum makes you go fast! That’s a good thing. Often the first sprint concludes with some working features. Managers and customers are happy. The team has worked a miracle and is happy too. Everybody is happy, and Scrum is seen as a great success.

Scrum让你去快!这是一件好事。通常第一个sprint结尾有些工作特性。经理和客户是快乐的。团队工作一个奇迹,也很开心。每个人都快乐,Scrum是视为一个巨大的成功。


The same thing happens the next sprint, and the sprint after that. Velocity is high. The system is coming together. Feature after feature is working. Expectations have been set. Plans are made. Enthusiasm for Scrum soars. Hyper-productivity has been achieved!

同样的事情也发生接下来的冲刺,冲刺。速度高。系统是一起来的。功能特性是工作后。预期。计划。Scrum飙升的热情。Hyper-productivity取得!


One of the reasons for this hyper-productivity is the smallness of the code base. Small code bases are easy to manage. Changes are easy to make; and new features are easy to add.

这种hyper-productivity的原因之一是渺小的代码库。小的代码很容易管理。变化很容易做,很容易添加新特性。

&nbsp;

&nbsp;

But the code is growing fast; and when a code base gets large, it can be very difficult to maintain. Programmers can be significantly slowed down by bad code. Teams can be reduced to near immobility by the sheer weight of a badly written system If care is not taken soon, the hyper-productive scrum team will succumb to the disease that kills so many software projects. They will have made a mess.

但是代码正在快速增长;当代码库越来越大,它可以很难维护。程序员可以显著减慢了糟糕的代码。团队可以降低至接近静止的一个写得很糟糕的系统如果不小心很快,超高效的scrum团队会屈服于疾病,杀死了很多软件项目。他们会搞得一团糟。

&nbsp;

&nbsp;

“Wait,” I hear you say. “I thought Scrum was supposed to empower the team? I thought the team would take all necessary measures to ensure quality. I thought the an empowered Scrum team would not make a mess.”

“等等,”我听你说。“我以为Scrum团队应该授权?我认为团队将采取一切必要措施,以确保质量。我认为一个授权Scrum团队不会制造混乱。”

&nbsp;

&nbsp;

That’s certainly the goal. The problem is that empowered teams are still human, they do what they are incented to do. Are they being rewarded for quality? Or are they being rewarded for productivity? How much recognition is the team getting for good code quality? How much are they getting for delivering working features?

这当然是目标。问题是,授权团队仍然是人类,他们被鼓励做什么。他们被奖励的质量吗?还是被奖励的生产力?多少识别代码质量的团队越来越好吗?他们得到交付工作特性多少钱?

&nbsp;

&nbsp;

There’s your answer. The reason scrum teams make messes is because they have been empowered and incented to make one. And a Scrum team can make a mess really, really fast! A Scrum team is hyper-productive at making messes. Before you know it the mess will be “so big and so deep and so tall, you can not time it up. There is no way at all.”

这是你们的答案。scrum团队使混乱的原因是,因为他们已经授权和被鼓励。和Scrum团队可以制造混乱真的,真的快!Scrum团队是超高效的在制造混乱。在你知道之前的混乱将会“那么大,那么深,那么高,你不能把它收拾干净。没有办法。”

&nbsp;

&nbsp;

And when that happens, productivity declines. Morale goes down. Customers and managers get angry. Life is bad.

当这种情况发生时,生产力下降。士气会下降。客户和经理生气。生活是不好的。

&nbsp;

&nbsp;

So how can you incent a Scrum team to not make a mess? Can you simply tell them not to make a mess? We’ve tried that. It doesn’t work. The incentives for going fast are based on tangible deliverables. But it’s hard to reward a team for good code If we don’t have some way to objectively measure it. Without an unambiguous way to measure the mess; the mess will be made.

所以你怎么能激励一个Scrum团队不拉屎吗?你能简单地告诉他们不要制造混乱?我们已经试过了。它不走了。激励要快是基于有形的可交付成果。但是很难奖励团队好的代码如果我们没有办法客观测量它。没有一个明确的方法来衡量混乱,混乱。

&nbsp;

&nbsp;

We need to go fast, and we need to stay time so we can keep going fast. How can we incent the team to achieve both goals? Simple. We measure both and reward them equally. If the team goes fast but makes a mess, then there is no reward. If the team stays time but goes slow, then again, there is no reward. If the team goes fast and stays time, then there is a reward!

我们需要快,我们需要保持干净,这样我们就可以继续快速。我们怎样才能激励团队实现这两个目标?简单。我们测量和奖励他们同样。如果团队快速但一团糟,那么就没有回报。如果团队保持清洁,但缓慢的情况下,再一次,没有奖励。如果团队快速和保持清洁,然后有奖励!

&nbsp;

&nbsp;

We can measure messes by implementing engineering disciplines and practices like Test Driven Development (TDD), Continuous Integration, Pair Programming, Collective Ownership, and Refactoring; i.e. the engineering practices of eXtreme Programming (XP).

我们可以测量麻烦通过实施工程学科和实践测试驱动开发(TDD)、持续集成、结对编程、集体所有权,和重构,即极限编程(XP)的工程实践。

&nbsp;

&nbsp;

It is usually best to start with TDD simply because a code base without tests is a mess no matter how time it might otherwise be. This is a bold claim, but it is based on a solid rationale from a much older and more respected discipline: accounting. It is just as easy for an accountant to make a mistake on a spreadsheet as it is for a programmer to make a mistake in a program. So how do accountants prevent errors? They enter everything twice.

通常是最好的开始与TDD仅仅因为没有测试的代码库是一团糟不论多么干净,否则可能会。这是一个大胆的声明,但是它是基于一个坚实的理由更年长、更受人尊敬的学科:会计。它只是作为一个会计容易犯错误在一个电子表格作为程序员犯错误是在程序。那么,会计师如何避免错误?他们进入一切两次。

&nbsp;

&nbsp;

Accountants practice Dual Entry Bookkeeping as part of the GAAP1 (Generally Accepted Accounting Principles). Accountants who don’t hold to the GAAP tend to wind up in another profession, or behind bars. Dual Entry Bookkeeping is the simple practice of entering every transaction twice; once on the debit side, and once on the credit side. These two entries follow separate mathematical pathways until a final subtraction on the balance sheet yields a zero. A set of books that is not supported with dual entries would be considered a mess by the accounting community, no matter how accurate and time those books were.

会计实践双重记账法GAAP1的一部分(一般公认会计原则)。会计人员不坚持公认会计准则往往在另一个职业,或身陷囹圄。双重记账法是进入每笔交易的简单练习两次,一次在借方,一旦在信贷方面。这两个条目按照单独的数学途径,直到最后一个减法在资产负债表上产生一个零。一套书籍,不支持双条目会被视为会计社会一片混乱,无论多么准确、干净的这些书。

&nbsp;

&nbsp;

TDD is Dual Entry Bookkeeping for software, and it ought to be part of the GAPP (Generally Accepted Programming Practices). The symbols manipulated by the accountants are no less important to the company than the symbols manipulated by the programmers. So how can programmers do less than accountants to safeguard those symbols?

TDD是双重记账法为软件,它应该是新闻出版总署的一部分(公认编程实践)。符号操纵的会计是不重要的公司比符号操纵的程序员。所以程序员如何做不到会计维护这些符号?

&nbsp;

&nbsp;

Programmers who practice TDD create a vast number of automated tests that they keep together and run as a regression suite. This is something you can measure! Measure the coverage. Measure the number of tests. Measure the number of new tests per sprint. Measure the amount of defects reported after each sprint, and use that to determine the adequacy of the test coverage.

程序员TDD实践创建大量的自动化测试,他们团结在一起,作为回归套件运行。这是一些你可以测量!测量范围。测量数量的测试。测量每个sprint的新测试数量。每个sprint测量缺陷的数量后报道,并以此来确定适当的测试覆盖率。

&nbsp;

&nbsp;

The goal is to increase your trust in that suite of tests until you can deploy the product based solely on whether that suite passes. So measure the number of “other” tests you feel you need perform, and make shrinking that number a priority; especially if they are manual tests!

他们的目标是增加你的信任测试套件,直到你可以部署套件的产品完全基于是否通过。所以测量数量的“其他”测试你觉得你需要执行,优先考虑减少这一数字,特别是如果他们是手工测试!

&nbsp;

&nbsp;

A suite of tests that you trust so much gives you an immense amount of power. With it, you can refactor the code without fear. You can make changes to the code without worrying about breaking it. If someone sees something they think is unclear or messy, they can time it on the spot without worrying about unintended consequences.

一个测试套件,你信任给你大量的权力。使用它,您可以重构代码而不用担心。您可以更改代码而不用担心打破它。如果有人看到一些他们认为是不清楚或混乱,他们当场就可以清洗而不用担心意想不到的后果。

&nbsp;

&nbsp;

Undocumented systems, or systems where the documentation has gotten out-of-sync with the production code, are messy. The unit tests produced by TDD are documents that describe the low level design of the system. Any programmer needing to know how some part of the system works can reliably read the unit tests for an unambiguous and accurate description. These documents can never get out of sync so long as they are passing.

未登记系统,或系统的文档已经失去同步生产代码,是混乱的。单元测试由TDD的文档描述的低水平设计系统。任何程序员需要知道一些系统的一部分作品可以可靠地读取单元测试一个明确和准确的描述。这些文件不能离开只要他们通过同步。

&nbsp;

&nbsp;

Measure the size of your tests. Test methods should be on the order of five to twenty lines of code. The total amount of test code should be roughly the same as the amount of production code.

测量你的尺寸测试。测试方法应该在5到20行代码的顺序。测试代码的总量应该大致相同的生产代码的数量。

&nbsp;

&nbsp;

Measure test speed. The tests should run quickly; in minutes, not hours. Reward fast test times.

测量测试速度。测试应该运行得很快,在几分钟内,而不是时间。奖励快速测试时间。

&nbsp;

&nbsp;

Measure test breakage. Tests should be designed so that changes to the production code have a small impact on the tests. If a large fraction of the tests break when the production code is changed, the test design needs improving.

测量测试破损。测试应该是这样设计生产代码有一个小的变化对测试的影响。如果测试失败的大部分生产代码改变时,测试设计需要改进。

&nbsp;

&nbsp;

Measure Cyclomatic Complexity. Functions that are too complex (e.g. cc &gt; 6 or so) should be refactored. Use a tool like Crap4J2 to pinpoint the methods and functions that are the worst offenders and that have the least test coverage.

度量圈复杂度。太复杂的函数(例如cc &gt; 6左右)应该重构。使用工具像Crap4J2确定的方法和函数最严重的罪犯和最少的测试覆盖率。

&nbsp;

&nbsp;

Measure function and class size. Average function size should be less than 10 lines. Functions longer than 20 lines should be shrunk. Classes longer than about 500 lines should be split into two or more classes. Measure your Brathwaite Correlation3, You’d like it to be greater than 2.

度量函数和类的大小。平均函数大小应该小于10行。函数超过20行应该缩小。类超过约500行应该分成两个或更多的类。测量你Brathwaite Correlation3,你想它是大于2。

&nbsp;

&nbsp;

Measure dependency metrics. Ensure there are no dependency cycles. Ensure that dependencies flow in the direction of abstraction according to the Dependency Inversion Principle4, and the Stable Abstractions Principle5.

测量指标的依赖。确保没有周期的依赖。确保依赖流抽象的方向根据依赖性倒置Principle4,抽象Principle5和稳定。

&nbsp;

&nbsp;

Use a static analysis tool like FindBugs6 or Checkstyl37 to locate obvious programming flaws and weaknesses. These tools can also find and measure the amount of duplicate code.

使用一个静态分析工具如FindBugs6或Checkstyl37定位明显编程缺陷和弱点。这些工具还可以找到和测量重复代码的数量。

&nbsp;

&nbsp;

Implement Continuous Integration. Set up a build server like Hudson8, Team City9, or Bamboo10\. Have that server build the system every time a developer commits some code. Run all the tests on that build and address any failures immediately.

实施持续集成。建立一个像Hudson8构建服务器,团队City9或Bamboo10。每次都有服务器构建系统开发人员提交一些代码。在构建和运行所有的测试地址任何立即失败。

&nbsp;

&nbsp;

Measure the number of commits per day. This number should be larger than the number of developers on the team. Encourage frequent commits.

每天测量的数量提交。这个数字应该比团队中的开发人员的数量。鼓励频繁提交。

&nbsp;

&nbsp;

Measure the number of days per month that the continuous build fails. Reward months with no failures. Measure the amount of time failures remain unaddressed.

测量每个月的天数,持续构建失败。奖励几个月没有失败。测量的时间失败仍然没有得到解决。

&nbsp;

&nbsp;

Story tests are high level documents written by business analysts and testers. They describe the behavior of the system from the customer’s point of view. These tests, written in a tool like FitNesse11 or Cucumber12, are requirements that execute. When these tests pass the team knows that they are done with the stories that they describe.

故事测试高水平由业务分析人员和测试人员编写的文档。他们描述系统的行为从客户的观点。这些测试,写在一个工具FitNesse11或Cucumber12等要求执行。当这些测试通过团队知道他们完成他们描述的故事。

&nbsp;

&nbsp;

Measure done-ness by running story tests in your Continuous Integration system and keeping track of the story tests that pass and fail. Use that as the basis for velocity and the progress of the team. Enforce the rule that stories are not done until their corresponding story tests are passing. And never let passing story tests break.

故事通过运行测试测量done-ness在持续集成系统和跟踪的故事通过和失败的测试。用这个作为速度的基础和团队的进步。执行规则,故事才做相应的测试是通过故事。测试失败,从不让过往的故事。

&nbsp;

&nbsp;

Practice Pair Programming. Measure the time spent pairing vs. the time spent programming alone. Teams that pair stay timeer. They make fewer messes. They are able to cover for each other because they know each other’s domains. They communicate with each other about designs and implementations. They learn from each other.

结对编程实践。测量时间配对与所花费的时间独自编程。团队对保持清洁。他们更少的混乱。他们可以互相掩护,因为他们知道对方的领域。他们对设计和实现相互通信。他们互相学习。

&nbsp;

&nbsp;

And after all this measuring, how do you reward? You post big visible charts of the metrics in the lunchroom, or in the lobby, or in the project room. You show the charts to customers and executives, and boast about the team’s focus on quality and productivity. You have team parties to celebrate milestones. You give little trophies or awards. For example, one manager I know gave shirts to everyone on the team when they passed 1000 unit tests. The shirts had the name of the project and the words “1,000 Unit Tests” embroidered on them.

毕竟这个测量,你怎么奖励?你文章大可见图指标的餐厅,或者在大厅,或者在项目的房间。你向客户显示图表和高管,吹嘘团队的关注质量和生产率。你有团队聚会庆祝的里程碑。你给小奖杯或奖项。例如,我认识的一个经理把衬衫给团队中的每个人都当他们超过1000单元测试。的衬衫有名称绣项目和“1000单元测试”字样。

&nbsp;

&nbsp;

How do you keep a Scrum Team from losing productivity? How do you make sure that hyper-productivity doesn’t veer headlong into a quagmire? You make sure that the team is not hyper-productively making a mess! You make sure they are practicing the disciplines that produce data that can be measured. You use that data to measure the quality of the code they are producing; and you provide incentives for keeping that code time.

你怎么阻止一个Scrum团队失去生产力呢?你如何确保hyper-productivity不转向在泥潭?你确保团队不是hyper-productively搞得一团糟!你确保他们正在实践的学科产生可以测量的数据。您使用这些数据来测定它们产生的代码的质量,和你提供激励保持代码的清晰。