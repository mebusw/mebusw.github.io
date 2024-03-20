title: “设计”工作应该放在迭代中吗？
tags:
  - agile
  - design
categories:
  - agile
  - engineering
---

在教练敏捷团队的过程中，一些团队反映“某些用户故事不好拆分，无法在一个迭代内完成”。对于这些故事，一些团队的做法是先指定资深人员作为“设计师”，他从“需求分析师”或业务方拿到需求后，花上1个迭代的时间进行“设计”，然后由他将编码任务进一步分配给“开发人员”，并且负责验收代码。这些故事往往就会持续1~2个迭代才能完成，甚至还来不及测试。

<!--more-->
那么对于团队提出的疑问，经过观察和了解，我们发现所谓的不好分拆的大型“设计”任务包括：
代码考古（很多组织在迁移升级旧系统时都会做这一步）
系统架构设计，包括与关联系统的接口讨论和定义
消化第三方提供的算法，包括翻译咨询机构提供的伪码，将其转换为与现实系统相符的数据结构
确定公共数据库表的结构，特别是当被依赖的关联系统较多时，讨论时间会是持久战。（而这可能不包含数据库优化。）
基础设施与环境部署方案
其他各种信息收集和讨论
这些”设计"任务根本不应该进入迭代！

以上这些正是对技术需求的分析细化与确认。这是任务出现在迭代中，其中一个原因是隐性或技术需求没有在迭代开始前得到充分的准备，以至于不得不在迭代开始后还在细化分析需求，制定所谓的方案。深入团队了解以后，虽然项目已经开始，上线时间也规定得比较紧张，但是需求的产出和细化却远远跟不上团队逐渐上升的产能。如果这些知识没有得到及时明确，也就难怪开发人员无法开展工作，并且在做的过程中要不停的确认需求，举步维艰。当然，深入发掘的话，可能还会看到业务方背后对于产品愿景的摇摆或者内部协调等问题，按下不表。

团队中的“设计师”难道不是在担任SA/PO的替身吗？！

这些设计和方案只是最初的设想和预估，是知识的学习和信息的收集，其中一部分也是为了给众人统一大方向，形成沟通的基础和规范。但那绝对不是最终实现结果。因为，谁能料到真正运行后会不会出现意想不到的问题呢？

到底什么是设计？

还记得1992年Jack Reeves的那篇著名文章吗？他前瞻性地提出”Source code is the design”，而Linus大神也讲过”Talking is cheap. Show me the code.”。这意味着只有能够运行起来的代码才是真正的设计，设计一定是要经过验证正确才算完成。至于各种”设计”文档与方案，充其量是辅助思考的中间产物，是信息空间的草稿。没有哪个其他行业的设计工作是纸上谈兵的，硬件制造、建筑等产出的文档，一定要通过工程和数学方法进行验证和锤炼后，才敢交予生产。

敏捷开发模式的目标是持续地交付，每个迭代的工作内容能够稳定地流动。(另外一个目标是团队持续地改进。) 要达到平稳的持续交付状态，分拆BI时要遵循INVEST，特别是Independent。每个BI都要在迭代结束时满足“完成”的定义，即DoD，通常来说是要部署到集成环境并测试通过，再加上一些其他团队认为合适的质量保证手段。

因此在迭代开始前，团队与业务方一起确定BI的范围，并且约定好如何进行测试和验收。越是不确定性大的内容，越是自身响应变化能力越差的部分，越是要更加提前。DoR（或称迭代准入条件）的概念作为一种有益的补充越来越多地引起人们的重视。

敏捷不提倡对整个项目周期做完全的计划，但是绝对地提倡冻结即将开始的迭代内容。这样，在迭代结束时BI才可以有依据进行验收完成。通常来说，提前1~2个迭代对将来要做的BI确认验收条件(Acceptance Criteria)是一种通用并有效的方式，也就是常说的ATDD实践。这是对业务方/SA/PO提出了更高的要求，既然要敏捷，大家都要协调地动起来才有好的效果。就像左右脚走路一般，需求分拆和沟通总是先于迭代半步。

结论：以上这些“设计”都还只是“需求分析和细化”，不应进入迭代中，而是提前1~2个迭代就应该准备好验收条件。而能够跑起来的代码才是实实在在的设计。
团队反映的这个问题并非"需求太大，不好拆分”的问题。对于敏捷团队遇到的问题，作为顾问和教练应该透过现象去挖掘背后的原因。