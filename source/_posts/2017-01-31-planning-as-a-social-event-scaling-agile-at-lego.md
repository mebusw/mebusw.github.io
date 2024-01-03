title: 以社交活动的方式做计划-乐高公司的规模化敏捷
date: 2017-01-31 11:21:56
tags:
    - scaling
    - planning

categories:
    - agile
    - scaling

---


Henrik Kniberg & Eik Thyrsted Brandsgård <br/>
2016年12月<br/>
原文授权链接: http://blog.crisp.se/2016/12/30/henrikkniberg/agile-lego


翻译&审校：
李洁(Jerry Li) 何强 龚正 姚宇宏(Ella Yao)  陈婧(Elina Chen)      申健(Jacky Shen)<br/>
统筹&出品：[申健(Jacky Shen)](http://www.jackyshen.com)<br/>
2017年1月<br/>
中译文链接：<http://www.jackyshen.com/2017/01/31/planning-as-a-social-event-scaling-agile-at-lego/>



> — “什么？一个150人的团队会议只要~~（2天）~~1天？”
> 
> — “对啊！每两个月一次。运行得非常好。”
> 
> — “但是为什么这样？怎么做到的？”



![](/media/LEGO-Agile-1.png)

# 背景
乐高数字解决方案部门（DS）由20个左右的团队组成，负责处理孩子和家长手中各种设备- 普通电脑，平板电脑，各种app应用，可穿戴设备，虚拟现实设备等等进行通讯。我们同时也在展望未来的产品开发，如何去拥抱新的技术，如何将传统的玩法与酷炫技术，例如增强现实结合起来，或者找一种能够将一个物理模型“扫描”进游戏的方法。绝大部分的团队在丹麦的比隆，但是我们在印度也有一部分团队。

<!--more-->

![](/media/LEGO-Agile-2.png)

乐高，从他的核心来说，并不是一个数码公司。他是一个有着80年历史和17000员工，高效的进行设计、生产以及营销实体玩具的公司。像其他公司一样，乐高也在尝试适应快速发展的数字世界，一个频繁变化的世界，而敏捷开发正在逐渐成为主流。

在乐高，数字解决方案部门正处于这个变化的前沿。我们也非常乐意与大家分享我们这一段十分有趣的过程。


# 问题
回到我们只有5个左右的开发团队的时候，计划和工作同步是十分可控的。团队和产品负责人能够仅仅通过相互交谈就搞清楚需要做什么。但是当我们成长为15-20个团队的时候，事情开始变得十分痛苦。

乐高公司作为一个整体有着非常好的投资组合和预算管理流程。投资框架是按照年度来沟通（项目Y需要X个小时），同时真实的内容决策是与财务决策解耦合的，所以各个部门可以灵活地投入人力。

所以在2014年，我们在顶层有着非常稳定的投资管理流程，而在团队级别，我们有15-20个团队在运转Scrum和Sprint。问题在于中间- 项目集层面！

![](/media/LEGO-Agile-3.png)

我们尝试使用Scrum并且用敏捷的方式工作，但是在其之上，我们基本上是一个矩阵型组织结构来做项目。作为一个产品负责人来说，你几乎把你所有的时间都花费在了各种会议中，以及协调团队之间的工作以便完成工作，但是最终你还是无法得到你需要的，因为别的团队有其他的优先级安排。

作为平台的产品负责人，你可能有10个人过来问你要一些东西，但是你又无法处理所有的请求。所以，你如何决定哪件事情更重要呢？有些时候，不同团队可能会制作一样的东西- 例如，嘿，在我们已经有的五旋转木马之外在添加一个过山车会不会很酷？好东西不能太多啊，或者？

总的来说，我们一直在苦苦挣扎于：

* **跨团队同步** - 如何能让各个团队向着同一个方向前进而不是成为互相的羁绊
* **客户合作** - 如何设定切合实际的期望并且在不过度承诺的情况下满足客户
* **发布计划** - 如何在跨多个迭代，多个团队，多个产品的情况下进行计划和安排优先级
* **平台开发** - 如何保证我们对未来投资，而不是仅仅是完成一堆一次性解决方案

当我们接近2014年结束的时候，我们决定尝试一些不同的东西。


# 改变
在2015年1月，我们开始勇敢地彻底调整。我们将整个DS部门演变为团队嵌团队的形式，引入了一个共享的迭代节奏，对于依赖管理和信息同步进行去中心化，同时每8周在房间里进行一次敏捷计划活动。这个改变产生了很多积极影响，不仅对于DS部门，并且也影响了与我们合作的其他部门。

现在我们来看看经过一年半的尝试我们最终得到了什么效果。

***声明***：*这仍然是一个正在进行中的旅程，不是一个已经完结的过程。我们仍在继续改进这个模型 — 例如， 在2016年第3季度，我们将大房间的全员迭代计划活动从2天缩减为1天。持续不断更新文章是很吸引人的，但是这样下去它将永远无法完成，那么我们就说这个文章是2016第2季度的写照吧。:o)*

# 大房间计划之旅
这是星期三早上，你被邀请到这个神秘的活动中，就是人们一直讨论的“PI计划活动”。你知道它代表着“产品增量计划活动”，是一种每8周发生一次的计划事件。你步入一个大体育馆，看到了这些：

![](/media/LEGO-Agile-4.png)

只是开玩笑…你看到是这样的：

![](/media/LEGO-Agile-5.png)

环顾四周，你看到所有DS交付团队都在那里，所有的经理，许多客户和利益干系人，以及一些像你一样的好奇访客。一份大致议程摆在桌子上。

![](/media/LEGO-Agile-6.png)

# 演示时间！
几分钟后，伴随着音乐响起，一个快进的5分钟的视频开始展示在过去的两个月发布的一些亮点部分，并且响起庆祝的掌声。

![](/media/LEGO-Agile-7.png)

这并不能取代团队通过Sprint评审和用户测试等日常反馈。但它提供了一个很好的概览和激发灵感。它提醒所有人我们这么做的目的。


# 闪电演讲
下一步，一位经理站起来几分钟，分享一些进行中的和将来的重要事项的想法.。接下来其他人站起来做一些鼓舞人心的讲话，例如数字儿童安全、一场即将到来的黑客马拉松、平台战略，以及当前的其他热点话题。有人演示使用新平台发布的东西是多么容易，另一个人运行一个有趣而愚蠢的Kahoot(译者注：一个游戏化学习平台)谜题。

![](/media/LEGO-Agile-8.png)

上午过半，全体会议结束了，当人们喝着咖啡闲逛时，他们把每一个环节的评分都写在一个反馈板上.。为每个主题谈话的价值和有趣程度打出1-5分。

![](/media/LEGO-Agile-9.png)

> — Joe:“看，大多是4分和5分！比上次好多了。”
> 
> — Lisa:"除了那第四个话题。啊！“
> 
> — Joe:“是的，这大概只跟十个人有关。我都睡着了。”

你可以看到一些小的评论，以帮助改善下一次的演讲。有几个人在争论我们是否应该进行闪电演讲，还是下次直接开始计划会议会更好。

引导者转向你。“全体会议就像定时炸弹，得保持简短和直达重点，要不然人们就会走神。而当正确进行的时候，它可以是一个真正的能量激发，它能够让人们感受到上下文语境，这有助于计划活动。”


# 分组讨论
下一步：分组讨论。当每个团队在同时工作，讨论在接下来的几个月交付的关键的事情时，会议室被各种嗡嗡声点燃。一个高层次的计划，不用太详细，因为许多意想不到的东西会在未来8周内发生。

每个团队都有自己的可移动白板和计划板。大多数团队成员都站在白板会前面进行讨论，与此同时一些人四处漫游，与其他团队和利益干系人进行同步。有些人则站在咖啡和零食桌旁讨论其他随机主题。

![](/media/LEGO-Agile-10.png) 

你注意到一些团队和角色有团队T恤或特殊的帽子，以便在人群中更容易发现对方.。印度的团队在会上有团队代表，，他们不时地和房间里的人们沟通，又不时地与印度的其他团队成员沟通。

乍一看这样的会议十分混乱，但能量水平相当高。尽管有些人似乎有点压力或无聊，但是大多数看起来度过了有趣且高效的时光。在现场有大量的自发对话和笑声，新的连接正在形成，已有的联系得到了加强。很明显，他们以前做过这个，并且感觉到很舒适。

这部分占据了一天的大部分时间，感觉非常像一个[开放空间活动(Open Space)](http://blog.crisp.se/2016/08/30/henrikkniberg/what-is-an-unconference)（如果你去过一次的话）。就像在一个开放空间活动时，最重要的规则是两只脚定律(Law of 2 feet)。

**两只脚定律**：*如果你在现在站着的地方没有学习、没有贡献、也没有乐趣，用你的两只脚，走去其他你可以学习或贡献或有乐趣的地方。（OK我们添加了“有乐趣”的部分，这不是开放空间的正式内容。）*

你注意到引导者经常强化这个信息：这取决于你，作为一个个体，在这几天内充分利用你的时间--无论是与你的团队做计划，在咖啡机旁聊天，或讨论架构。不要等待别人告诉你到底具体该做什么或去哪里。

但也有一些限制，所以每个团队的桌子上你可以看到一个小册子来描述整个计划流程和NFR（“非功能性要求” - 细想起来是一个比较奇怪的名字......）。那包括例如法律要求，服务包更新或新的部署流程等所有的团队都需要考虑的事情。这些非功能性需求是对每个产品增量都是最新的。


# 基于拉动式的计划
你注意到一个很重要的细节 - 就是团队成员（其中的绝大部分）在主动拉取工作，而不是让工作被动强加于他们。他们决定拉取的数量是根据
以往已经交付的数据及对实际情况的直觉。为了让这能在实践中行得通，每个团队都有一个产品负责人与利益干系人进行协商并且在PI计划活动之前准备一份已经经过优先级排序的产品待办事项列表(Product Backlog)。于是产品负责人和客户决定事项的优先级，而团队则选择拉取多少。

有些团队相当独立，他们拥有自己的待办事项列表。但是其他团队，例如平台组则需要和其他人一起工作，然后从共享的项目群待办事项列(Program Backlog)中拉取一些要做的功能。

![](/media/LEGO-Agile-11.png) 

你会发现项目群待办事项列表被投影在墙面上，同时你也会注意到有各个不同组的成员组成的一小堆人站在待办事项列表前，协商着哪个待开发功能应该被哪个团队拉取。

![](/media/LEGO-Agile-12.png) 

团队都是半专业化的，每个团队有自己专注的领域，如乐高身份验证，云技术或搜索。团队倾向于拉取他们最熟悉或最不令人讨厌的功能，所以产品负责人有时需要提醒他们即使与团队的专业不匹配，也要有人能更好地拉取即使令人讨厌但是那些高优先级的功能。到现在为止，团队之间已经能够很好地进行团队间的负载均衡，以确保我们作为一个整体专注于正确的事情（而不是超级有效地构建错误的东西）。团队成员大多是[T型人才](https://en.wikipedia.org/wiki/T-shaped_skills)，这意味着他们在某种程度上是专攻一点，但同时也具备足够的广度来互相帮助。

我们曾经把产品待办列表打印在卡片上，贴在一个实体的板子上，像这样：

![](/media/LEGO-Agile-13.png) 

......但这有点繁琐，因此我们想出了一个解决办法，就是直接在产品待办列表管理工具中在线处理，直接投影在墙上。虽然直接动手少了，但由于我们比如有六个团队共同做平台开发，这样更容易跟踪直到任务结束。

看看周围，你会注意到一些团队的桌子上有大屏幕，用电子化的方式显示他们的待办列表，而其他人大多使用实体工具。


# 团队白板
每个团队都有一块可移动白板上，用于物理的团队白板。

![](/media/LEGO-Agile-14.png) 

随着计划的成型，白板会逐渐被填满。

![](/media/LEGO-Agile-15.png) 

不同部分的含义也不同：

![](/media/LEGO-Agile-16.png) 

这基本上是团队对于接下来4个迭代的高层次的计划（每个迭代是2周，因此产品增量是8周），也是敏捷计划活动的主要产出。

> — “呃...你们仅仅每8周发布一次？”
> 
> — “不，8周只是我们项目集层面的计划周期。 发布周期是完全独立的，有些团队发布更频繁，有些发布更少”。


当然，计划一直在变，这取决于每个团队环境的易变性如何。但是拥有一个计划仍然有用，因为它驱动着围绕优先级、相互依赖性、团队容量等各方面的对话。需求将永远超过容量，因此团队和客户需要共同努力，在艰难的权衡下做出决策。为了帮助计划活动，团队使用“[昨天的天气](http://www.agilenutshell.com/yesterdays_weather)”这个方法。简单来说，就是他们查看过去的PI数据，显示他们完成了多少(故事点)。这些简单的数据被用来检查现实情况，以避免过度的承诺。我们曾经因过度承诺而产生了很多问题，进而导致质量不好,错过了最后期限,以及客户和团队之间的不信任。面对不确定性，最好是开始时略少承诺，稍后再拉取更多的工作而不是过度承诺，然后不得不推迟工作的完成。

“PI目标”是团队的粗略承诺，理想的情况是以基于影响的（“我们将实现业务结果X”），而不是基于输出的（“我们将提供功能Y”）。但大家的情况总会有所不同。弹性目标是团队可能完成的事情，或希望完成，但没有足够的信心来承诺完成。

> — “但是等等，”承诺“究竟是什么意思？”

很高兴你问这个问题！承诺的PI目标意味着：

* “基于我们现在所知道的，我们确信我们可以实现这一目标。
* “我们有额外的容量处理不确定性”。那需要多少额外容量？取决于： 
	* 我们所涉及的工作量有多不确定?
	* 我们对环境（改变优先级等）有多么不确定？
	* 这个承诺有多重要？它越重要，我们可以承诺的其他目标越少。
* “我们将尽最大努力去完成我们做过的承诺，但我们不能100％确定。”
* “如果任何时候我们不相信可以完成这点,我们将尽快让利益干系人知道”。

过去，承诺通常意味着“有人代表你做出一个不切实际的许诺。解决这个问题”。它不仅伤害了产品质量和积极性，而且使计划和预测变得很困难。


# 风险板
随着计划的进行，团队开始甄别可能导致承诺失败的风险、潜在的问题比如“新许可证将无法按时交付”。这些被张贴在散布在房间里的风险板上，每个项目或主要的活动都有一块风险板。一些风险可以通过与正确的人沟通来内部解决，而其他风险则需要升级，并在第一天结束时留在板子上让管理层进行评审。

![](/media/LEGO-Agile-17.png) 

过去，风险倾向于要么被忽略，要么所有的都太快地委派给管理层（导致其变成瓶颈）。通过以自下而上的方式可视化风险，团队获得了更多自主权，并升级他们真正需要帮助的风险。有时，减轻风险的成本是巨大的，并有不成比例的影响，所以最好只是接受风险。承认和接受风险消除沮丧，并使团队安心，所以他们可以专注于交付而不是担心。


# 依赖关系白板
很快你会注意到墙上挂了一块真心巨大的白板。看起来就像是引力的中心，
因为总有一大群人围着它在议论纷纷。一开始白板上什么也没有，但是第一天结束时，白板上就挂满了一堆乱糟糟的便利贴，便利贴之间用...呃...
红色纱线连在一起？！贴纸，剪刀，纱线，什么鬼？

![](/media/LEGO-Agile-18.png) 

我们管它叫“依赖关系白板”（有时候也叫“项目集白板”）上面展示了谁、什么时候、从谁那里、需要什么。仔细看看...

![](/media/LEGO-Agile-19.png) 

每一列是一个团队，每一行是一个迭代（2周）。便利贴代表了依赖关系，从蓝色到粉色。“为了交付这些（蓝色贴纸），我们需要你们提供那些（粉色贴纸）”你会注意到会有很多讨论，都是关于什么东西在哪个迭代、会被交付到谁那里。

这不是任何人单独拥有的白板。这个板一开始由引导者放上去，但是接下来
团队就会完全自组织地工作，把他们工作的依赖关系都可视化出来给大家看，并且把这块白板当作一个握手协议，来决定谁需要去和谁对话。这个
白板是一个集中的工具，用来使去中心化的合作成为可能。

> — “那么，团队会把所有计划要交付的功能放上去吗？”
> 
> — “不，只有那些具有依赖关系的功能。我们曾经把所有的东西都放到白板上去，但这样一来白板就变得异常凌乱，大家都看不懂了。我们得出的结论是大家只关心依赖关系，所以我们只专注于这一点。独立的功能在团队各自的白板上呈现，不会在这里。”

你发现了一个明显的瓶颈（CIT那一列，企业IT组），同时看到了如何最有效地解决这个瓶颈的热烈讨论，而且也有些讨论是关于长期如何降低这个瓶颈的影响。

> — “那是什么？”你问引导者。
> 
> —  “ CIT是独立于DS的一个单独组织，所以一开始的时候，他们并没有参与计划活动。但是经过前几次PI计划活动后，很明显的是，CIT才是依赖关系最重要的部分。大家对它抱怨很多。因此我们在依赖关系白板上加了CIT这一列，并且请他们派出代表，来参加PI计划活动。”
> 
> —  “这有帮助吗？”
> 
> —  "有，帮助很大！互相指责少了，互相合作多了，'我们和他们'的说法少了，'我们和我们'的说法多了。”

CIT和DS的人在白板前面对面的讨论，关于如何最好地利用我们珍贵的瓶颈（当然，也讨论了怎么样从长期来说，减少这个瓶颈）。看起来很糟，但是大家会告诉你“以前更糟！你应该看到我们做的改进！比如说，把东西移到云上来减少依赖。虽然这个是很耗费时间的。”

依赖关系白板的底部几行明显是空着的。我们管它叫IP（“创新和计划”）。那个迭代是预留给计划外的创新、前三个迭代的溢出、还有类似于下一次PI计划会议及培训以及其他冒出来的事情。

一个工程师指出“目前，我们在达成我们所承诺这件事上已经做得好多了。我们有了容量数据（故事点等），所以我们不太可能会过度承诺了。但是，IP迭代也会像是我们的缓冲一样，即使有些事情搞砸了，我们也有空间去让事情恢复到正轨。更重要的是，承诺都是在讨论和协商后做出的，而不是被强加在我们头上的。”

> — “ 好的，那么创新做的怎样？那是IP迭代的I的部分，对吗？”他笑了。
> 
> — “这部分通常都被忽视。但已经比早期好多了。上一次PI的IP迭代中，
> 
> 我们做了一次黑客马拉松，很多又酷又有用的东西在那之间诞生了！有些团队永远都能为创新挤出时间，让其他这次IP迭代中做不到的团队羡慕不已。”

另外一件让你好奇的事情：

> — “开完会后，依赖关系白板怎么处理？”
> 
> — “收起来， 带回办公室，贴在一面墙上。”
> 
> — “ 然后呢？”
> 
> — “ 每周或者每两周我们做Scrum of Scrum。每个团队的ScrumMaster们聚集在板前讨论，并把已经解决的阻碍和依赖划掉。”

他给你看了一张照片：

![](/media/LEGO-Agile-20.png) 

> — “印度的团队呢，他们怎么看到板呢，假如白板只挂在比隆？”
> 
> — “我们还在解决这个问题。。。”
> 
> — “那下一次PI计划呢？白板怎么办？”
> 
> — “扔了。每次PI计划我们都会新建一块依赖关系白板。这会给我们带来新的视角，让我们在白板上尝试新的格式或者结构不会受之前的做法的影响。”


# 计划草案集会
在几个小时看似混乱的切分计划会议后，引导站到台上宣布计划草案集会开始了。在他总结形式的时候，大家安静下来（大部分人也已经很熟悉怎么做了）。

> — “我们会依次进行四个7分半钟的短会。每个短会上，一个团队成员
都会站在团队白板前给大家讲他们的计划，其他人可以去参加别的团队的短会，听他们的演讲。”

他会启动一个7分半钟的大型计时器，然后演讲就开始了。每个计划白板前聚集一小堆人，一起听并且讨论那个团队的计划--他们打算交付什么以及理由。依赖和风险都会被讨论，有时候，也会发现新的问题。别急，明天我们会有时间来解决这些问题。

> — “为什么是7分半钟？为什么是4个短会？”
> 
> — “我们做了很多不同的尝试，这个是目前我们觉得最好的。刚刚好
我们有足够的时间去获取到信息，而不至于觉得无聊，或者陷入到细节中去。”

![](/media/LEGO-Agile-21.png) 

7分半钟后，铃声响起，第二个短会就开始了。大家会跑去另一个团队去听他们的计划。就这样，四个短会一共是半小时。

![](/media/LEGO-Agile-22.png) 

> — “好吧，所以说每个人都可以选择参加四个团队的短会并了解他们的计划？”
> 
> — “完全正确。”
> 
> — “那项目的全景呢？知道所有团队的计划不是更有用吗？”
> 
> — “对有些人来说，是这样的。但是对绝大部分人来说，不是的。所以只需要选四个你最感兴趣的团队。假如你想知道更多，你可以在明天的休息时间去拜访更多团队。”
> 
> — “你一直都是这么做的吗？”
> 
> — “不是，过去我们会按顺序来做计划草案的分享。所有人都坐下，每个团队轮流起来给所有人介绍他们的计划，引导者用摄像头把他们的白板投影到一个大屏幕上。一旦找到合适的工具后，这个过程很顺利。但即使严格限制时间，要把所有20个团队都过一遍，也要至少一个小时的时间。”
> 
> — “哎妈这么长”
> 
> — “可不是咋滴。虽然我们尽力让他变得有趣，但还是沦落为瞌睡会！一些人会仔细听，但是大部分人都在百无聊赖的玩手机，或者趴在桌上，希望这个会议赶紧结束！大家确实关心别的团队在做什么，尤其是有所依赖的那些团队。但那最多也就是3到4个团队，他们不需要知道所有的团队在做什么。所以我们决定不再强迫大家去了解所有人的计划，而是建立一种基于拉动的方式，让他们自己选择。用集会的方式效果就好多了！”
 
你到处走动，听别人的演讲。大家讨论他们的计划、依赖、技术、风险等等。感觉真的就像是一个集会！

引导者再次过来并指出：

> — “注意到房间里能量高涨了吗？那是我们主要的反馈环，说明
我们做的是对的。假如能量很低，大家开始撤出了，那不论我们做什么
都是错的，我们需要改变形式。于是保留那些激发能量的部分，抛弃那些让人无聊瞌睡的部分。”

你注意到其实有两个引导者，两人结对，轮流上台讲话。他解释说：

> — “我们一直都会结对引导这个活动。这样，当一个人在引导的时候，另一个人可以退后一步，观察一下能量水平，然后想想有什么可以改进的。结对也可以给我们留有一些余地，假如一个人生病了，那招入一个新的引导者也会更容易。”


# 管理层评审和问题解决
第一天结束的时候，大部分人离开了，只有主管们留下。管理层评审的时间到了！

在巨型依赖关系白板旁边，风险白板排成一排，然后主管们围成一个半圈。

![](/media/LEGO-Agile-23.png) 

所有人都受平台的进度影响，所以他们从那里开始。平台产品负责人总结他们的计划，提出一个折中的艰难决定。

> — “坏消息。我们没法再这个PI里面同时交付A和B”。

但是两个任务都非常重要，缺少任何一个都可以造成广泛的不良影响，大家争论的很热烈。引导者从中帮忙调解矛盾和关注点。主管们讨论了他们手上的选项，每个选项的优缺点，最终决定先做A并推迟B。好几个团队明天都会因为这个决定而调整他们的计划。

然后，他们按个过每个风险白板。保留在白板上的风险都是团队自己无法解决的，因为它们涉及到公司的其他部门，或者超出了团队的控制。这些主管现在需要负起责任并决定怎么做，而且他们很有动力这么做，因为只有搞完了才可以回家。。。

你注意到风险白板上的列。一个主管解释说：

> — “ROAM框架在这个对话中很有用。我们每次讨论一个风险，做决定，然后把他们移到四列中的某一个列里面去：已解决（R），已认领（O），已接受（A），或者已缓和（M）。所以回家的标准很清晰。所有的风险都必须被讨论，并且移到相应的ROAM列里面去。”

![](/media/LEGO-Agile-24.png) 

过完每个风险之后，他们会决定谁负责在第二天早上给大家总结这个结果。

如果你记得那本关于公仆式领导力的书，就会意识到，这样的方式让主管们更向公仆式领导力的模式靠近。团队看到主管们承担起责任来并且帮助解决问题，这会在他们之间建立起了信任。

引导者也证实了这一点。

> — “管理层评审以前是很混乱的，也很消耗精力。首先，以前我们没有
风险白板，所以讨论通常都没有结构且耗时很长，做出的决定有时候甚至还会被忘记。后来我们用了风险白板，当时有太多风险被升级了。慢慢的，团队自己开始承担责任（当然是在主管们的鼓励下），然后因为升级的风险少了，主管们也更愿意，更有能力去承担那些风险负起责任来。”



# 第2天 - 使计划稳定
第二天吃早餐时，你问某人：

> — “为什么还需要第二天？你们不是都已经完成计划了吗？每个团队都已经制定了计划，依赖关系都已经确定了，风险都已经被处理了...现在还要做什么？”  
> 
> — “计划还需要完善。昨天我们回家时，仍然还有很多没有解决的问题。现在经过一晚上的休息，我们会继续迭代完善计划和解决问题，否则这些问题就会在下游爆发并搞砸迭代。” 
> 
> — “那么你们总是花2天时间来做计划？”
> 
> — “到目前还是这样。但是我们已经做的非常棒了，而且第2天能量相当低，因此我们未来可能会尝试只做1天。”

*后话：我们这样做了，发现用1天时间做计划确实更有效！先自己想想看。稍后再对此进行更多的介绍。*

早餐后，经理们站出来展示和分享他们对昨天形成的计划草案的反馈意见，总结管理层评审所作出的关键决策，并解释他们将如何处理升级给他们的关键风险。

出现的一个大问题是不稳定的测试环境，几个团队都已经把这件事情识别为一个大风险。负责该领域的主管得出这样的结论：“我们清楚这个问题，本次PI中，大多数任务都在优先提高稳定性——可向X团队了解更多信息。但需要花费几个月时间才能得到一个真正稳定的环境，因此到现在为止我们只能接受这个风险。”不尽如人意的消息，但是团队们称道的是问题已经被承认并且至少情况在向好的方向发展。有些团队会调整他们的计划，以考虑一段时间内测试环境仍不稳定的情况。
 
随后，团队继续采用昨天的形式做计划。较早完成计划的团队可以开始进行其他工作，那些存在大量依赖的团队继续和其他团队合作，搞清楚需要谁在什么时间做什么事情。 
 
引导者们鼓励人们就算已经完成了自己那部分计划也仍然留在现场。这些人可以继续做其他的事情，诸如编码、处理邮件之类的。 
 
> — “有时候其他依赖你们的团队会出现新问题，如果你们仍然在现场，问题就能很快得到解决。”
 
在某些情况下，部分团队成员会回家，现场留下少数代表以便为其他团队所用。
 
到下午早些时候，计划已经稳定了（或者已经到达他们的要求了），是时间进行最后的计划评审了。我们仍然采用相同的4场会话形式，团队继续可以到处游走和聆听彼此的计划。彩色乐高块被用于指示从昨天开始计划是否发生了大量变化，这就像是状态标识，可以帮助人们决定去听哪些团队的计划。
  
![](/media/LEGO-Agile-25.png)

# 信心投票
在草案计划集会顺利完成后，团队回到各自的桌子，主持人宣布进入信心投票时间。“对于达成PI目标，你有多大的信心？”每个人举起1-5根手指，1表示“毫无信心”而5表示“信心满满”。
 
![](/media/LEGO-Agile-26.png)

环顾四周，你看到大多数团队都非常有信心达成承诺。然而有少数几个团队表现出缺乏足够的信心，这时候主持人会邀请他们说点什么。总之，能量非常低，你帮不上什么忙但是想了解——这样做到有什么实际价值？

# 迷你回顾
日程最后一项是迷你回顾。每个团队都坐下来讨论计划活动本身，从上次以来改进了什么，下次应该改进什么。此外，每个团队成员对整个PI计划活动的价值进行独立地和匿名地评分（“我的时间花得有多值”），5分为最高分。 
 
之后，除了ScrumMaster和引导者，其他人都回家。ScrumMaster和引导者们在改进看板前围成半圈，一个接一个，张贴来自他们团队的个人评分，总结反馈意见和改进建议。 
 
![](/media/LEGO-Agile-27.png)

每个人都完成陈述后，他们后退一步并观察看板，找出模式和主题。大部分人都喜欢这个活动（大部分都是4或5的评分），认为时间花的值得。
 
> — “等等！这难道不都是内些开发人员吗？我曾认为开发人员是讨厌开会的？”  
> 
> — “是的，在刚开始开展计划活动的时候，我们都有过担忧。但即使在第一次，评分结果都超出了我们的预期。看起来，人们认为这是一件实际工作而非是一次会议。” 

尽管有一些2分，甚至有几个1分，但是大部分人都对下次如何改进，给出了可执行的反馈。他们喜欢在大房间内做计划的想法，他们更关注这个活动如何执行。还有对时长的关心。
 
![](/media/LEGO-Agile-28.png)
 
  
这次在迷你回顾中出现的一件事情是信心投票。这实际上有多少用处呢？有些人希望对此进行调整，另外一些人认为这是在浪费时间应该去掉。经过讨论后，他们同意进行一次试验 - “下次PI规划时我们跳过信心投票这个环节，看看我们是否会怀念它。”
 
*后话：下次PI计划时我们跳过了信心投票这个环节，正如开始猜想的那样，我们并没有怀念它。团队对计划的承诺不是外界强加的而是自主自发的，这就意味着他们有足够的信心，使得投票变得多余。再见吧，信心投票！这只是我们如何通过不断试验和调整活动形式以最大化价值产出和最小化浪费的众多例子之一。*
 
*更远的后话：在2016年第3季度，我们尝试了1天版的规划活动（同样的活动，但是把2天压缩成了1天），并且看到无论是能量还是评分上都有明显的改善。所以我们继续采用1天版。但是普遍的共识是，开始时我们可能需要2天作为垫脚石，直到我们学会如何更加有效地开展工作。*

就是这样。结束了。团队们带着各自的团队看板和风险看板回去。引导者们留下来相互做一个简短的交流，草草记下一些关于下次有哪些不同做法的笔记，然后开始收拾依赖看板和其他东西。
 
就这样，现在你已经看到了一个PI计划活动！希望你喜欢它。 

现在，让我们后退一步。

# 除了大房间计划外，还有什么变化？
太多了！尽管PI计划是我们带来的变化的重心，但明显还有更多的东西。“真正”的工作实际上是发生在PI计划活动之间的。 
  
在PI期间，团队通常以常见的Scrum仪式，例如迭代规划、每日站会、待办项梳理、迭代评审以及迭代回顾，来进行Scrum以及2周的迭代。我们也有共享的PI演示和回顾。但在此我们不想让你迷失于具体的细节。本文聚焦于PI计划活动，描述我们正在尝试实现的自下而上的去中心化协作。PI计划就像是一面反映整个过程和文化的镜子。
 
值得强调的一项活动是PI预计划，产品负责人们聚集在一起讨论，为即将到来的PI特性进行优先级排序。在每次PI计划前，我们有三次这样的预计划会议。在会上，特性从一个标题演化为可理解的特性，并按照价值、时间紧急程度以及工作量进行排序。如果想了解更多，请研究延迟成本（Cost of Delay）或者加权最短工作优先（WSJF，Weighted Shortest Job First）。如果你想真正深入了解，请查看Don Reinertsen的著作[《Principles of Product Development Flow》](https://www.amazon.com/Principles-Product-Development-Flow-Generation/dp/1935401009)。
  
![](/media/LEGO-Agile-29.png)

# PI计划活动的实际目的是什么？
用一个词来描述：对齐

PI计划是一个大房间计划活动，其目的是让团队们彼此进行对齐。
 
大房间计划的价值与你们有多少依赖直接相关。当然，最好是设计你的团队结构和架构，尽量减少依赖和避免需要进行大房间计划之类的事情。但如果你有一帮团队开发同一款产品，或者共享技术、密切相关的产品（就像我们的情况），那么大房间计划就超级有用。 
 
伴随着迭代，每个团队都有一个2周的计划范围。PI计划增加了一个更高层次的规划范围，在这里团队聚集在一起展望未来（2个月，一个“产品增量”），但不关注细节。 

![](/media/LEGO-Agile-30.png) 
 
诀窍是抵制住制定精确、详尽计划的诱惑。就算是粗略的正确也比精确的错误好。 
 
注意，产品增量，并不像听起来那样是一个大爆炸的发布版本。它是一个同步点，一个时间和地点，团队们聚集在一起，彼此对齐。这与发布周期是完全无关的。不同的团队以不同的周期发布，这取决于他们工作的性质。因此我们按节奏规划，按需发布。 

![](/media/LEGO-Agile-31.png) 
  
PI计划解决了团队间不对齐以及由此引起的挫折和浪费问题。这是一个让人们奔向同一个目标的承载平台，同步一帮某种层度上自组织团队的一个“大心跳”。
  
![](/media/LEGO-Agile-32.png) 
 
没什么能比得上面对面沟通。在PI计划期间，信息共享和决策的数量是非常惊人的。在同一时间和地点把所有人员召集在一起并给予信任和授权，使得这件事成为可能。任何你需要交流的人此时此刻都在房间里。无需发送日历邀请或者等待合适的会议时间，只需要上前沟通。 
 
如果没有计划活动，我们就需要一堆单独的协调会议、电子邮件和电子表格来完成同样的事情，这增加了等待时间以及错误理解。团队会在大房间计划活动中产生骨架，并在PI规划会议后（迭代期间）讨论工作细节。
 
PI计划的另一个好处是，它提供了彻底的透明性，揭示了大规模软件开发所固有的复杂性。这可能会吓到外部利益干系人，但他们能更好地理解和尊重团队，因为他们的工作实际上是试图驯服复杂度这个怪物。
 
那么有什么坏处呢？嗯，容易被人讨论的是成本问题，但实际上这并不是一个大问题。无论人们是否在PI计划中，都要支付相同的工资。唯一真正的开销费用是场地和食物，相比获得的价值来说这只是一笔小小的费用。 
 
一个心理学上的缺点是，如果你是一个控制狂人，你无法做到心平气和，以及得出清晰的计划。整个设计可能会让人感觉非常混乱和无组织。
  
然而，一旦你习惯了这种方式，那就会意识到会议提供了一个极好的整体概要，让你舒适地感觉到目标可以被达成，同时还允许你放大你觉得特别重要的领域。剩下的你只需要相信团队，留给他们就可以了——毕竟，他们都是非常聪明和敬业的人，否则干嘛雇佣他们。：）

# 这件事情带来了什么影响？
没有什么事情是完美的，但总的来说产生了相当积极的影响，看起来没有人想回到过去的做法。
 
* **重复劳动更少了。**团队们之间的协作更加协调，因此在重复劳动上浪费的时间更少。 
* **依赖性问题的更少了。**团队们在相互阻塞等待上浪费的时间更少了。团队们也更加顺畅地与其他部门和利益关系人合作。
* **主管们能够更快地调整优先级和解决障碍**，因为他们比以往更加解实际情况。
* 客**户的信任得到了改善**，因为他们比以往更加理解团队在做什么以及为什么这样做。
* **计划变得更容易，承诺的达成率也更高**，因为团队们和投资组合计划者们都清楚我们的实际产能和能承诺的工作量。
 
更重要的是，这**提升了团队成员们的积极性**。当困惑和浪费减少时，工作也就变得更加有趣。而受到激励的人会把工作做得更好。因此这是一个良性循环！ 
 
我们看到的另一个影响是，乐高的其他部们观摩了会议活动，特别受启发，并开始探索如何在自己的部门实施这些原则和做法。事实上，敏捷如同病毒一般在公司内部传播开来，而PI计划活动的高度透明性就像一个催化剂。
 
尽管如此，这依然是一件复杂而又艰苦的工作（我们乐于称之为“艰难的快乐”），还有很大的改善空间。但通过朝同一个目标越发对齐，获得更多授权的团队，更好地为客户设置了正确的期望，以及更好地识别了相互之间的依赖关系——这一切都让我们意识到我们已经更好地以正确的方式交付正确的东西。

# 这一切是如何开始的？
你还没走？想知道这一切是怎么开始的吗？好的，下面是背后的故事。  
九十年代后期的生活是美好的。屈指可数的几个人（我们今天称之为跨职能团队）用些GIF和HTML就能够精心打造出乐高的在线业务。但是随着数字化的提升，交付数字化解决方案所需的人员也越来越多，开发工具也变得更加高级。大约15个团队规模时，我们真的是相互交错但却又朝不同的方向使劲。 
 
最初，我们试着设置了一堆项目经理，但事实上无助于问题的解决。他们总是表现得像是事后诸葛亮。典型症状是：不断地更新计划以及向指导委员会申请追加预算或者延迟发布。
 
2009年左右我们在团队级别引入了敏捷原则，现在我们仍然坚信这些原则，但是我们真的不知道如何大规模应用敏捷。某位项目经理听说了“大规模敏捷框架”，就去参加了学习。当他回来时，他对此进行了分享，然后我们就没有再管它...已经好几年了。
 
但是突然事情有了转机。经过与一些团队的讨论后，项目经理们相信这个框架可以帮助我们。所以召集了各部门的所有主管来介绍这个框架。他们并不完全相信，但似乎它可能会有帮助，而且似乎也不太可能使事情变得更糟，那么为什么不试一下呢？因此，某位高级主管推动了这件事情，并申请了经费对整个部门120人进行培训。 
 
我们进行了三天的培训，包括一次模拟练习。第二天，我们对此的信任有了极大的提升，制作了一份项目集待办项清单，并安排了首次PI计划。这是一个嘈杂、局促和混乱的开始。但是，令我们惊讶的是，活动得到落地执行，并且效果好于我们原先的顾虑。
  
![](/media/LEGO-Agile-33.png)   
 
以下事情帮我们获得了最初的成功：

* **团队们希望做出效果。**如果团队们不买账，我们就会惨败。尽管对框架的某些部分持怀疑态度，但他们确实投入其中并且尽力使之成功。 
* **已有的敏捷经验。**团队已经运行敏捷多年了，并且组织中相当一部分人都明白敏捷原则。这是敏捷规模化的良好基础。
* **管理层买账。**管理层提供了足够的支持和信任，使得事情得以成功。虽然没什么高级主管直接参与，但他们接受了相关的风险，提供了活动开展的空间，并确保我们有足够的经费来支持每个相关人员的教育和培训。 
* **不教条的方式。**虽然我们以大规模敏捷框架为工作基础，但我们并不迷信框架。由于我们的团队们是工作于不同的产品和服务上，因此对多团队工作于同一款产品的情况进行了优化。因此，每一次新的PI，我们都会定制和调整过程，添加需要的元素，移除不增值的元素。这就给了人们一种主人翁的感觉，他们可以看到过程和框架都为他们服务，而不是反过来。
  

# 现在在干嘛？你们目前面临的挑战是什么？ 
我们还在不断试验。每一种改变要么使事情得到改善，要么使它们变得更糟，要么没有区别。但我们保留有效的和抛弃无用的，所以随着时间的推移事情日趋完善。 
  
![](/media/LEGO-Agile-34.png) 

最大的改进是在开始，现在的变化较小，并且我们似乎也已经找到了一个可持续的工作流程。目前以小的调整和权衡为主。毕竟房间里有这么多人，我们不可能真的确保每个人都极其高兴，但至少我们可以确保没有人极其不高兴。
   
我们最大的挑战是，我们工作于一堆不同类型的事物。虽然我们是一个部门，大多数情况下使用相同的工具和技术，但我们并不是在构建一个浑然一体的产品。理想的情况下，我们希望基于特定的产品或者价值流组成一些较小的团队--我们组织设计上的大一统理论。这样就会有一些较小的PI计划，而不是庞然大物般的会议。但我们还没想出解决方案，我们已经习惯了让每个人都在同一个房间里做计划的便利。 
 
结果，我们很难在PI计划上只聚焦于一两个有着清晰故事线的主题。相反，我们最终会为不同的团队和利益干系人提供一系列不同的目标。这就令主管们不满意，因为他们想要一个有着清晰故事线的团队工作事项。团队也很不满意，因为他们想要清晰地理解上下文和整体优先级。这是个先有鸡还是先有蛋的问题。

尽管试验和调整过程看起来像是一件好事，我们有时仍然会扪心自问：我们真的是在优化正确的变量吗？还是我们只是为了改变而改变？例如，如果PI计划已经不再是最大的限制，我们真的应该继续努力改善它吗？到目前为止，我们真诚地相信我们正在改善正确的事情，但是偶尔反省一下也是好的。

另一个挑战是动力。在开始的时候，对于变化每个人都很兴奋（或焦虑），能量很高，并且感觉也很好。在PI计划中小的变化和惊喜可能是个好东西，能让人们保持思考。我们只需要时刻提醒自己，事情总是会改善的。 

# 结束语
希望本文对你有用！当然它对我们很有用--给了我们一个借口来真正地反思我们所做的和所学的:o) 
 
请记住，我们的方法不是大规模敏捷的“答案”。这只是一个例子，一个正在进行中的旅程的快照。敏捷全都是关于持续改进的--它是一个方向，而非一个终点。 
 
然而有件事情是可以肯定的。如果我们试图从一开始就计划好整个变化，我们将一事无成！相反，如果你想让改变发生，那就从现在开始，设定某个目标，然后开始试验。这一切都是关于人的--如果他们在进行变化，如果他们支持这种想法，那么你就会取得进展。否则没戏。 
 
不要羞于使用现有的流程框架，或借用其他公司或案例研究的想法（比如这个）。没有必要重复发明轮子！只要确保你能调整成适合自己的上下午就可以。虽然不会找到满足你所有需求的完整解决方案，但你可能会找到某些能驱使事情往正确方向发展的东西。
   
  
 
**祝你好运！**

— Henrik & Eik

*致谢：许多聪明、热情、友好的人一直在参与塑造这些变化！我们不会费心去列出每个人。我们的角色主要是支持敏捷之旅和煽风点火。:o)*

![](/media/LEGO-Agile-35.png)
 
Henrik Kniberg
henrik.kniberg@crisp.se 
http://www.crisp.se/henrik.kniberg
https://twitter.com/henrikkniberg
http://blog.crisp.se/author/henrikkniberg

Eik Thyrsted Brandsgård 
eik.thyrsted.brandsgaard@lego.com

Jacky Shen
jackyshen@scrumchina.com

### 反馈？疑问？ 
请使用[中译文博客评论](http://www.jackyshen.com/2017/01/31/planning-as-a-social-event-scaling-agile-at-lego/#comments)或[原文博客评论](http://blog.crisp.se/2016/12/30/henrikkniberg/agile-lego)或[直接联系我们](mailto:jackyshen@scrumchina.com)。大部分都会看，但不幸的是我们不能保证给予响应。人生太短，总不能事事回应:o) 
  