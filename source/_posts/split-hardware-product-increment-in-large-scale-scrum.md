title: 硬件产品增量分拆于规模化敏捷Scrum研发中
date: 2017-06-16 11:12:56
tags:
    - scrum
    - hardware
    - scaling
---


如何分拆硬件研发中的产品增量?

这个例子来自德国纽伦堡的某个硬件产品团队，他们在5年前开始导入LeSS（大规模敏捷）。

产品领域是电信硬件和软件，其中关键是cross connect board(某种PCB电路板)，包含电源、FPGA–现场可编程门阵列(其中一些最终融入到ASIC–专用集成电路中)、设备驱动等。

一个架构要点是，容错是非常重要的；一块PCB电路板经常具备另一块“B计划即故障切换(failover)板子（想象该cross connect board"方案具有A板和B板），即使在单块板子上也会有容错处理（例如:从外接电源切换到电池）

<!--more-->

作为一个例子，一个特性团队应该包含具备不同技能的人，如电路板设计、热能分析和调整（降温和耗散设计）、电源、FPGA（Verilog编程语言）、Linux设备驱动（C和C++语言)、其他（此外，有个人现在能用FPGA的Verilog语言做TDD，采用了一个有趣和有用的工具叫SVUnit。如果想学习更多硬件研发和增量分拆的话，我推荐大家接受FPGA Verilog TDD辅导）

将巨型增量分解为更小（但仍然巨大）增量的一个明显方式是：
* 只有一块板子
* 不集成A板和B板
* 不支持故障切换
* A板和B板集成并带有故障切换。

对于单块板子，它包含供电，并支持主（电源）备（电池）切换。注意团队可以创建一个增量，只有一个主电源的单块板子（相对于支持切换到备用电池来说）…没有其他东西了。于是第一个具体的增量是这样的：
* 带有主电源（外部供电）的单个板子（不含其他芯片/功能）
* 带有第二电源（电池供电）的单个板子（不含其他芯片/功能）
* 带有主备电源（以及主备切换）的单个板子

接下来，现在是DC直流电（而非AC交流电），而板子需要AC/DC转换器。这也需要2个（为了容错切换）。于是：
* 带有主备电源的单个板子，以及主AC/DC转换器
* 带有主备电源的单个板子，以及第二AC/DC转换器
* 带有主备电源的单个板子，以及主备AC/DC转换器（以及主备切换）

板子要能够通过探针和查询来提供信息。于是：
* 带有主备电源的单个板子，以及主备AC/DC转换器，能够启动并提供”空“的服务水平信息
* 带有主备电源的单个板子，以及主备AC/DC转换器，能够启动并提供简单的服务水平信息
* 带有主备电源的单个板子，以及主备AC/DC转换器，能够启动并提供丰富的服务水平信息

板子能够提供配置/控制。于是：
* 带有主备电源的单个板子，以及主备AC/DC转换器，能够启动并提供丰富的服务水平信息，并且可以处理“复位”操作
* 带有主备电源的单个板子，以及主备AC/* DC转换器，能够启动并提供丰富的服务水平信息，并且可以处理“复位”和“进入省电模式”等操作

或许这足以感受一下如何分拆硬件增量？

毫不惊讶，采用FPGA进行增量开发，那么板子的服务就容易进行纵向拆分（一早就有的软件功能分拆）。如果大家想要最终走上ASIC的路上，那么我们可能先仅仅从FPGA开始，最终再转变到ASIC。

=== 注意硬件组件团队的局部优化认知偏见 === 

顺便提醒我的是：据我所知，对于形式上的硬件研发敏捷或Scrum，常见的入门建议是采用预定义的接口来定义单独的硬件组件，然后据此建立单独的组件团队。这与纯软件的组件团队没有区别，且在硬件研发造成和软件研发一样的系统动态，局部优化，缺乏敏捷性，没有工作在最高价值上，造成浪费。

反之，跨硬件组件的特性团队。硬件组件的边界，有时候的确是难以用一个真正的特性团队联系到一起，这是因为”硬知识“的限制（比如我们辅导过的施乐，(1)光学引擎，与(2)送纸机械，会用到(1)大部分光学知识，(2)机械工程知识，于是难以融为一个团队）。但是通常来说，在LeSS中我们建议去尝试更多的跨硬件组件的特性团队（比如：在一个团队中，包含具备不同技能的人，如机械工程、热能分析、FPGA、电源等）。我们承认有时是太难以至于无法融合，但值得尝试，而不是将硬件组件研发落入局部优化偏见的限制思维中。

本文由[@申导](http://www.jackyshen.com) 翻译自Craig Larman(CST/LeSS创始人)在SA TCC board中回答的一个问题。[优普丰敏捷学院](http://www.uperform.cn/blog/split-hardware-product-increment-in-large-scale-scrum
)原创内容，未经授权，不得转载。

--------------------
(Below is the origin text from email by Craig Larman)

just answered a question on the SA TCC board, that realized might be of interest here too, and others here will have interesting contributions


Q: examples of increments & slicing in HW dev?


here's an example of HW product increments from a group based in nuremberg (DE) that was starting a LeSS adoption maybe 5 years ago. 


domain is telecomm hw & sw. in this product, key element is a "cross connect board" (a kind of PCB), which includes power, FPGAs (some ultimately baked into ASICs), device drivers, etc.


one noteworthy arch point to understand this is that fault tolerance is a strong force; so a PCB often has a "plan B" failover board (so one thinks of the "cross connect board" solution has having an A and B board), and even within 1 PCB there can be fault tolerance (e.g. working from supplied power versus from battery)


as an example, a feature team may include diff people with primary skills in board layout, thermal analysis & refinement (one has to design for heat reduction and dissipation), power, FPGA (verilog), linux device drivers (C and C++), and more
(aside, one can now to TDD with system verilog for FPGAs, with SVUnit, which is fun & useful. i recommend folks here get into FPGA verilog TDD coaching if they want to learn more about HW dev & slicing/increments)


so an obvious huge splitting into (still huge) smaller increments was:
only 1 board; no integration of A and B boards; no failover support
(versus its major complement): integrated A and B boards with failover support


for 1 board, it included power supply failover with primary & secondary (battery) support. note that the team can create in increment which is just a board with only primary power... and nothing else on it. a board with only primary power versus with failover to secondary. this led to the first concrete increments in this example:
a board with primary (externally supplied) power (no other chips/features)
a board with secondary (battery supplied) power (no other chips/features)
a board with primary and secondary power (and failover from primary to secondary)


next, this is DC land (not AC land), and the board needs an AC/DC convertor. and this one has 2 (for fault tolerance). so...
a board with primary and secondary power, and primary AC/DC convertor
a board with primary and secondary power, and secondary AC/DC convertor
a board with primary and secondary power, and primary & secondary AC/DC convertors (and failover)


boards can and should provide info, via sw probes/queries. so...
a board with primary and secondary power, and primary & secondary AC/DC convertors, and can bootstrap to providing "null" service level info
a board with primary and secondary power, and primary & secondary AC/DC convertors, and can bootstrap to providing simple service level info
a board with primary and secondary power, and primary & secondary AC/DC convertors, and can bootstrap to providing rich service level info


boards can and should offer config/control. so...
a board with primary and secondary power, and primary & secondary AC/DC convertors, and can bootstrap to providing rich service level info, and can handle operation "reset"
a board with primary and secondary power, and primary & secondary AC/DC convertors, and can bootstrap to providing rich service level info, and can handle operation "reset" & "enter power save mode"
etc


perhaps that's enough to get a sense of this hw splitting and increments? 


and not surprisingly, the services of the board can be sliced vertically relatively easily using FPGA incremental dev (which is plain old sw feature splitting, really). if the group wants to eventually go down the ASIC path, then if possible we'll start with FPGAs only and nail that and then later transform to ASICs.



=== watch out for the local optimization cognitive bias of hw component teams ===
btw, reminds me: afaik common introductory advice for apparently "agile" or "scrum" HW dev is to "define separate hw components with clearly defined interfaces and then have separate component teams". this is no different than the model of pure sw component teams, and leads to the same system dynamics, sub-optimizations, lack of agility, lack of working on highest value, and wastes in hw dev as in sw dev. versus cross-HW-component feature teams. it's true that sometimes a HW component boundary can't be bridged by a true feature team due to "hard knowledge" limitations (e.g. at xerox where used to coach, the (1) optical engine, versus the (2) paper feed mechanism, which involves for (1) mostly laser optics knowledge, and for (2) mech eng knowledge, and was too hard to bridge in 1 team). but on average, in LeSS we suggest experimenting with more cross-HW-component feature teams (e.g. 1 team includes diff people with primary skill in mech eng, thermal analysis, FPGA, power, etc). and we ack that sometimes that's too hard to bridge, but it's worth experimenting rather than falling into the default thinking trap of local optimization bias in HW components & dev.


recent case: angela johnson is working with a hw/sw product group (doing a LeSS adoption) that has switched to true cross-HW-component feature teams (versus prior "hw component teams with interfaces"), with very positive consequences
