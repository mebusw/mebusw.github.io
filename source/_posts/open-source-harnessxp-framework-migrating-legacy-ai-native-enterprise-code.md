---
title: Open source HarnessXP framework - For migrating legacy AI native enterprise-level cross-warehouse multi-technology stack code projects to HarnessXP development, you can abandon openspec/superpowers. 

date: 2026-06-28 12:43:00
tags:
	- AI
	- HarnessEngineering
	- VibeCoding
	- openspec
	- superpower
	- claudecode
	- orchestrator
	- agent
categories:
    - ai
      - coding

---

## 开源这个框架的起因

上个月，一个朋友跟我吐槽：他们组 5 个 Claude Code agent 一起干活，后端 agent 把 API 字段改了，前端 agent 完全不知道，联调挂在生产环境。

我问他："你们不是用 git 协作的吗？"

他说："用了。但 `git merge` 退出码是 0，分支 tip 就是没进 main。下次部署就是旧代码。"

这其实是所有跨 2 个以上仓库跑 AI agent 的团队都会撞上的三面墙。今天介绍一个工具 HarnessXP，三面墙一起拆。

 项目作者是XP多年实践者，一开始是为了迁移和统一自己的已有的3个项目代码（小程序、后端服务、管理员网站）。后来越搞越大，最后相当于自己弄出来一套openspec commands。一开始确实是想直接用openspec的，后来发现他跨不了仓，然后就在chatGPT和ClaudeCode的帮忙下，自己搞出来一套跨仓的SDD框架。改业务代码过程中，就碰到各种bug，一边儿改代码儿，一边儿修这些命令，就弄出了这么个项目，剥离了业务代码，开源给社区供大家学习和参考。地址：https://github.com/mebusw/HarnessXP

![Image](https://mmbiz.qpic.cn/mmbiz_png/AxSfDycXFQmuEAibH026vF40B9RgvCibtpLdL8rKViaQQRgWobenKiaWVOibs7R9GibTaKzB2GxEoJeqCW185m20Bg2FrrZZmuuUJUzydTibRNQSrg/640?from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

## 5分钟上手

- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 

```
# 1. 拉框架git clone https://github.com/mebusw/HarnessXP.gitcd HarnessXP# 2. 初始化新项目（在干净父目录）mkdir my-project && cd my-project../HarnessXP/bin/init \  --name my-project \  --repo backend:../backend:service:node \  --repo web:../web:web:react \  --repo mobile:../mobile:app:flutter# 3. 把你的共享契约放进 orchestrator 的 shared/cd orchestrator# （把 openapi.yaml / types.ts / config.schema.json 丢进 .openspec/shared/）# 4. 同步契约到所有业务仓bash .openspec/swarm/sync-shared.sh all# 5. 启动 spec-driven workflowclaude> /spec USER_AUTH> /plan USER_AUTH> /swarm USER_AUTH> /merge USER_AUTH
```

## 蓝图



Harness Engineering ← 方法论

HarnessXP ← 开源框架

Specification Gravity ← 核心理论

AI-Native Harness Engineering for Polyrepo & Polyglot Development ← 愿景



## 跨仓开发的几个典型踩坑

### 坑一：合约漂移 —— 后端改了字段，前端到生产才挂

想象一下这个场景：

后端 agent 收到指令："把 `/api/users` 的 `email` 字段改成 `contact_email`。" 它很快把后端代码改了，单元测试通过，提 PR，merge。

前端 agent 同时在另一个仓库登录页面。它不知道 `email` 改名了，照样发 `email`。联调在 staging 没跑过（前端的 spec 没同步），部署到生产，挂了。

合约漂移的本质：**spec 和代码在不同的仓，改 spec 的 agent 和改代码的 agent 不沟通。**

HarnessXP 的解法很直接 —— 把 spec 和共享合约集中到一个 orchestrator 仓。N 个业务仓持有代码，但所有"跨仓接口"必须在 orchestrator 仓里登记。M 个 agent 开工前先读 orchestrator，改完接口必须同步更新 orchestrator 仓。

💡 **核心思路：** 让"接口是什么"这件事只有一个真理来源。Agent 之间通过共享合约沟通，不通过 git commit 历史沟通。

### 坑二：状态无人负责 —— "特性 X 做到哪了？"

你问 PM，PM 说："我以为前端在做。"

你问前端，前端说："我看 PR 在后端那边挂着。"

你问后端，后端说："我 merge 了呀。"

规格在仓 1，代码在仓 2，评审在仓 3，状态在某个人的脑子里。这是多仓 AI 协作最常见的死法 —— **状态没有归属，问谁都是"以为在别人那里"。**

HarnessXP 用一个 4 态状态机解决：PROPOSED → IN_PROGRESS → REVIEWED → MERGED。每个跃迁由且仅由一个工具负责：

- PROPOSED

  ：spec 在 orchestrator 仓登记完毕

- IN_PROGRESS

  ：某个 agent 拿到这个 spec，开始改

- REVIEWED

  ：CI + 跨仓合约校验通过

- MERGED

  ：`merge-all.sh` 二次校验成功

任何时刻问"特性 X 的状态"，工具直接告诉你。不需要去问 PM、前端、后端。

💡 **核心思路：** 不要让人去维护状态，让工具去维护。状态机的价值不是机器本身，是"每个跃迁有且只有一个负责人"。

### 坑三：幽灵合并 —— git merge 退出码 0，但 tip 没进 main

这是最阴险的一个。

CI 全绿。PR 合并按钮显示 "merged"。`git log main` 也有这条 commit。看起来一切都好。

但下次部署，CI 跑的是旧代码。仔细看 `git log main`，发现这条 commit 在 main 上的位置比某个 feature 分支还老 —— 它根本不在 main 的 head 链上。

常见原因：rebase 出错、cherry-pick 错乱、force push 覆盖。`git merge` 的退出码 0 只能告诉你"本地操作没出错"，**不能告诉你"这次合并真的进了 main 的 head"**。

HarnessXP 的解法是 4 行 bash：

- 
- 
- 
- 
- 
- 

```
git fetch origin mainif git merge-base --is-ancestor "$BRANCH_TIP" origin/main; then  echo "OK: tip is in main"else  echo "FAIL: phantom merge detected"fi
```





`git merge-base --is-ancestor` 做的事就是：检查 `$BRANCH_TIP` 是不是在 `origin/main` 的祖先链上。如果不是 —— 合并看起来成功但 tip 没真进 main —— 就是幽灵合并。

每次 `merge-all.sh` 跑都对每个 PR 做这个二次校验。

💡 **核心思路：** 不要相信退出码，相信祖先链。CI 验证的是"代码能不能编译"，`merge-base` 验证的是"代码有没有真的进 main"。

## 架构：1 orchestrator + N 业务仓 + M agent

HarnessXP 整体架构极薄，由三部分组成：

- 1 个 orchestrator 仓

  ：所有 spec、共享合约、状态机记录都在这里

- N 个业务仓

  ：每个仓独立 git 仓库，代码在这里

- M 个 Claude Code agent

  ：每个 agent 有严格的"允许修改的文件"边界，跨边界读写需要走 orchestrator

agent 之间通过共享合约沟通，不通过 git commit 历史沟通。这避免了"我看你昨天改了什么"这种脆弱的协调方式 —— 后者只在单仓有效，跨仓立刻失效。

## 结语

HarnessXP 是 MIT 协议，bash 3.2 兼容，macOS 开箱即用。仓库：

https://github.com/mebusw/HarnessXP

如果你也在跨多仓跑 AI agent，欢迎试用、提 issue、扔 PR。

如果有企业咨询辅导和定制化培训，请联系 <jackyshen@uperform.cn>