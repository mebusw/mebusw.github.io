title: 敏捷开发中分解拆分用户故事的几种模式招式
date: 2015-01-19 19:50:59
tags:
	- user story
	- agile
categories:
    - agile
    - engineering

---

User Story is a lightweight requiement in Agile mode, splitting big specification to fine granularity is helpful to increase flexibility, deliver highest value earlier and feedback earlier to expose risks. Here's some patterns for splitting user story, also it described the "5C" characteristics and INVEST acronym for good story.

用户故事(User Story)是敏捷开发中流行的需求表达手段，各种敏捷流派中都提倡将大型需求进行化整为零，减少颗粒度，提高灵活性，实现尽早交付价值和揭示风险。本文根据 @申导 多年敏捷实践经验，同时借鉴业内流行做法，整理出一套分解用户故事的模式，分享给大家。并且最后也给出了优秀的用户故事需要满足的5C特征及INVEST原则。

<!-- more -->


# 各种分解的招式 Approaches to breakdown user stories

### 1 Functional Requirements 功能性需求

1.1.	Take a thin slice through the **workflow**; or do beginning and end of the workflow first and then the middle of the flow. 先从工作流中剥离出薄薄的一层进行开发；或者先完成工作流的头尾部分，然后再完成中间步骤。

As an Amazon user, I can buy a book online so that I can buy book everywhere.
* As a Amazon user, I can search for a book so that I can buy it
* As a Amazon user, I can pick book into shopping cart so that I can pay for it later
* As a Amazon user, I can review my shopping cart so that I can change my mind
* As a Amazon user, I can check out shopping cart so that I can ask for delivery
* As a Amazon user, I can pay with PayPal so that I can easily transfer my money to Amazon

1.2.	Split multiple **operations** in the story into separate stories. (e.g. story about managing or configuring or CRUD) 按照多个操作分离成不同的用户故事（例如，关于”管理”、”配置”或”CRUD”的故事）

As a iPhone user, I can manage my contacts so that I can keep in touch with my friends.
* As a iPhone user, I can browse my contacts so that I can see who are in my contacts
* As a iPhone user, I can delete a contact so that I can shorten the list
* As a iPhone user, I can create a contact so that I can remember my friend’s phone number
* As a iPhone user, I can edit my contacts so that I can remember my friend’s new phone number
* As a iPhone user, I can share my contacts so that others can get this information easily

1.3.	Do a subset of the **business rules** first and enhance with additional rules later. (e.g. a domain term in the story like flexible dates that suggest several variations?) 先完成业务规则的一部分，然后再进一步增添其他规则（例如，包含的领域术语”弹性日期”的故事暗示着多种变化）

As a Aeroplane Chess player, I can move my spawn in order to play
* As a Aeroplane Chess player, I can only start moving my spawn when roll a 6
* As a Aeroplane Chess player, I can move my spawn according to the roll
* As a Aeroplane Chess player, I can move my spawn thru shortcut when it stop at the teleporter
* As a Aeroplane Chess player, I should move my spawn backward when meet the end point

As a JIRA admin, I can see permission of all users
* As a JIRA admin, I can see permission of individual so that I can join him to a project
* As a JIRA admin, I can see permission of project member so that I can ensure proper authorization for projects
* As a JIRA admin, I can see visitor permission so that I can release temporary access in time

1.4.	Process one kind of **data** first and then the other variations later with same operations. 先处理一类数据，然后再处理基于相同操作的其他类型的数据。

As a online banking user, I want to transfer my money so that I can make transactions everywhere
* As a online bank user, I can transfer money to my own accounts
* As a online bank user, I can transfer money to other bank account
* As a online bank user, I can make international transfer
* As a online bank user, I can pay bills

1.5.	Handle data from one I/O **channel** first and enhance with the others that handle the same kind of data later. 先处理来自某一个渠道的数据，然后再关注处理同类数据的其他渠道。

As a citizen, I can view PM2.5 rating from all the data publishers so that I can get an average assessment.
* As a citizen, I can view PM2.5 rating from U.S. Embassy.
* As a citizen, I can view PM2.5 rating from National Meteorological Centre.

1.6.	Split for different **roles**.  按照不同的角色来分解。

As a WebEx user, I can schedule a meeting so that I can connect to my colleagues
* As a mobile WebEx user, I can schedule a meeting with iPhone so that I can take a walk during the meeting
* As a desktop WebEx user, I can schedule a meeting with desktop browser so that I can get full features of a meeting

As a user, I can login to the online store
* As consumer user, I can login to go shopping.
* As admin user, I can login to manage the big data.

1.7.	Strip low **priority** from high priority. 将低优先级与高优先级需求分离。

As a user, I’m required to log into the system so that I can access the system.
* As a user, I can login into system with correct username and password so that I can access the system
* As a user, I want to be frozen access for logging in with invalid password three times.
* As a frozen user, I want to be sent an email notifying a malicious attempt was made.

### 2 Non-Functional Requirements 非功能性需求

2.1.	Make it work first and **cross-cutting concerns** to reduce the complexity. Such as Performance, Conceptual Integrity, Reusability, Availability, Interoperability, Manageability, Scalability, Security, Supportability, Testability, Usability, Maintainability, Auditability and Reliability.  使软件先工作起来，推迟跨领域的关注点，包括性能、概念完整性、可重用性、可用性、互操作性、可管理性、可伸缩性、安全性、可支持性、可测性、易用性、可维护性、可审计性、可靠性。

[Performance] As a new user, I want to be able to login to the system and join the meeting quickly when I use it first time so that others don’t need to wait for me.
* As a new user, I want to be able to login to the system and join the meeting.
* As a new user, I should complete application downloading within 20 seconds.

[Manageability] As a privileged user, I can see restricted search results so that I can make sure information confidential
* As a privileged user, I can see all search results.
* As a privileged user, I can see restricted search results that authorized to me

[Security] As an online bank user, I want to add payee so that I can pay to it. 
* As an online bank user, I want to get current payee list
* As an online bank user, I want to add a payee
* As an online bank user, I want to conduct RSA token authentication before adding a payee

[Security] As an attacker, I must not be able to inject SQL with malicious statement.

[Availability] As an operator, I want the system switch over automatically when alarm 1234 raised so that I can continue my business. 

[Interoperability] As a user, I want to log in to system with my account.
* As a user, I can sign up to acquire an application account.
* As a user, I can login with my application account.
* As a user, I want to use OpenID as my user identity to login to system, so that I can use my existing accounts of OpenID service provider and don't need register a new proprietary user account.

[Usability] As a user, I want to start a Webex meeting.
* As a user, I want to start a Webex meeting.
* As a user, I want to start a Webex meeting via a one-click-start tool.

### 3 Technical Constraints 技术约束

3.1.	**Evolve architecture** design. 进化架构设计

As a client, I can read data from data source. 
* As a client, I can connect to JSON file to read data. 
* As a client, I can connect to Mysql database to read data. 

3.2.	**Bypass the real** external systems first with dummy / fake / stub / mock. 利用仿制品/打桩等先绕过真实的外部系统

As a client, I want to integrated with PayPal interface so that to pay the bill
* As a client, I can pay via a fake PayPal interface that returns “successful”
* As a client, I can pay via a fake PayPal interface that returns “insufficient balance”
* As a client, I can pay via real PayPal interface

### 4 Spike 尖刺

4.1.	Separate **simple/complex**, do the core first that provides most of the value and/or learning and enhance it with later stories. 区分简单与复杂，先完成提供最多价值或知识的核心部分，以及解决最严重的阻碍，澄清需求和约束，然后再进一步完善。

As a user, I can search for game cards and see result grid in same screen.
* As a user, I can search cards with title and text and see total amount found so that I know searching is done
* As a user, I can see result grid so that I can see detailed result
* As a user, I can search with criteria including sets, types, houses, abilities so that I can make advanced searching


4.2.	Setting up **infrastructure component** can be verified alone. 搭建基础设施组件的工作可以被独立验证。
I want to setup Weblogic environment in order to deploy backend services on that.

4.3.	Can you do some **trace bullet** such as mockup / experiment / simulation / prototype to evaluate the new tool / framework / language/ algorithm within a time-box within a time-box to see what can be achieved? 你是否可以在时间盒内做些端到端的概念验证，比如模型/实验/模拟/原型等，来评估一下新工具、框架、语言、算法，并看看所取得的效果？

I want to do a POC with Robot Framework for automation test in 10 days, in order to see whether it suitable for current project and developers’ skills

I want to compare the pros and cons between Maven and Ant in 5 days, so that we can decide which to use in the coming project.

As a client, I want to connect to “component X” which is ported from legacy system to new system in order to keep the interface unchanged
* Collect all messages from/to “component X” monitored on legacy system to understand the behavior first
* Next step…


 
# Evaluate the split 评估分解的成果

* Do each of the stories satisfies INVEST?  每个用户故事都符合INVEST吗？
* Are the new stories roughly equal in size? 新的故大小是否基本一致？
* Is each story about 1/10 to 1/6 of your velocity? 每个故事大小都在你的速率的1/10到1/6之间吗？
* Are there stories you can deprioritize or delete? 这些故事可以被丢弃吗？
* Is there an obvious story to start with that gets you early value, learning, risk mitigation, etc.? 哪个故事明显可以先开始，从而带给你早期价值、学习、缓解风险吗？

# Reference: Good user stories satisfy INVEST 参考: 好的用户故事满足INVEST

* Independent:  The user story should be self-contained, in a way that there is no inherent dependency on another user story. 独立的：用户故事应该是独立的，没有对于其他用户故事的内在依赖。
* Negotiable: User stories, up until they are part of iteration, can always be changed and rewritten. 可协商的：用户故事在进入迭代之前，都可以进行变更和重写。
* Valuable: A user story must deliver value to the end user. 有价值的：用户故事必须向最终用户交付价值。
* Estimable:  You must always be able to estimate the size of a user story. 可估算的：用户故事的大小必须总是可以估算的。
* Small: User stories should not be so big as to become impossible to plan/task/prioritize with a certain level of certainty. 尺寸小：用户故事不应该过大，以至于无法在一定的确定性下进行计划、分派和排列优先级。
* Testable: The user story or its related description must provide the necessary information to make test development possible. 可测试的：用户故事及其相关的描述必须提供必要信息，允许对开发进行测试。


# Reference: 5C of User Story 参考: 用户故事的5C特征
* Card: stories are traditionally written on note cards, and these cards can be annotated with extra details.  卡片：习惯上将故事写在便条卡片上，这些卡片可以标注额外的细节。
* Conversation: details behind the story come out through conversations with the Product Owner. 交谈：与产品负责人的交谈中产生故事背后的细节。
* Confirmation: acceptance tests confirm the story is finished and working as intended. 确认：使用验收测试来确认故事的完成和符合工作要求。
* Construction: implement the story
构建：将故事实现出来
* Consequence: measure and feedback the expected outcome to user
后果：度量及反馈是否达成预期结果


