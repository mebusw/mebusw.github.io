---
title: 【经验实录】跨3仓遗留代码迁移到AI编码工程，1个月踩坑提炼6条铁律，逼出企业项目驾驭工程智能体的进化方法论

date: 2026-06-26 01:51:00
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



> 这不是又一篇"AI 写代码提效 10 倍"的爽文。
> 这是一份**真实工程实录**：4 个遗留 git 仓、3 套异构技术栈、生产上跑着的服务。
> 我们花了一个月，搭了一套叫 Harness Engineering 的脚手架，让 AI 能在遗留系统上**可控地演进**，而不是 vibe coding 玩一玩。
> 写给正在评估"AI 编码能不能用在我们这种企业遗留系统上"的技术负责人、架构师、研发管理者。

---


## 一、先给你看个画面

如果你的企业里存在这样的场景，这篇文章就是为你写的：

你接手了一个项目。打开 git 一看：**3 个仓**——一个 Express.js 后端（带一堆历史遗留的 Liberating Structures 内容库），一个微信小程序（视频课程平台是叠在"线下课报名"老小程序上的），一个 AngularJS 管理员网站。

每个仓各自的 main 分支、release tag、CI pipeline、部署节奏都不一样。改 1 个 API 字段要 3 个仓联动，前端先上线、后端 API 还在部署——用户立即看到 500。

新需求：把这套跑了 3 年的老系统，演进成视频课程平台。

这时候你跟 AI 说："帮我加个视频课程模块。"

AI 会回你一份"理想新系统"的设计稿——跟你手里这套老代码完全对不上。

**这就是 vibe coding 的天花板。**

它适合从 0 写 demo、做 side project、做学习练手。
它**不适合**企业里"老房子改扩建"这种真实工程。

那么，AI 编码在企业遗留系统上**到底能不能用**？

我们用一个月的时间，把这件事跑通了。答案不在模型本身，而在我们搭的脚手架——我们叫它 **Harness Engineering**。

<!-- more -->
---

## 二、Harness Engineering 是什么、不是什么

**不是：**
- 不是 prompt 工程（"你应该这样和 AI 说话"）
- 不是选模型（GPT 还是 Claude 还是 Gemini）
- 不是装个 IDE 插件（Cursor / Copilot / Claude Code）
- 不是从 0 开始搞 vibe coding demo

**是：**

**让 AI 在你的工程里"按规矩办事"的一整套脚手架。**

类比软件工程发展史——传统软件工程有这些环节：

需求分析 → 系统设计 → 编码规范 → 代码审查 → 自动化测试 → CI/CD → 灰度发布 → 监控告警 → 应急修复

我们没发明新东西，**这套流程一个都没丢**。只不过执行主体从"人"变成"人 + Agent"——人不再只写代码，人写**让 AI 写出来的代码能可控地汇入主干**的状态机、目录约定和脚本。

一句话总结我们的元经验：

> 当 AI Agent 接管了"写代码"这件事之后，工程师的工作不是写更多代码，而是构建"让 AI 写出来的代码能可控地汇入主干"的状态机、目录约定和脚本。
> 这套基建写不好，Agent 跑得越快，烂摊子越大。

我们 2026-06-09 一上午提交了 11 个 commit。**没有一个是写业务的**——全部都是在补齐 swarm 框架的状态机、文件命名、bash 兼容性、跨仓路径。

如果你看到这里还有共鸣，下面回答你最初关心的 3 个问题。

---

## 三、问题 1：怎么让 Agent 生成的代码"满足需求"？

### 3.1 需求分析：你得先搞清楚，再让 Agent 搞清楚

企业遗留系统的 AI 编码必须**先冻结"哪些是基线、哪些要演进"**。否则 AI 会把遗留代码当"待清理对象"——你的 LS 内容库、用户中心、线下课报名这些**已存在业务**，会被 AI 误判为"可以重构的新模块"。

我们的做法是**冻结基线**：开新会话的第一件事，跑 `/release-skills`（一个独立 skill，不在 7 个 slash command 之列）给当前 git 状态打一个 release candidate tag（带 release notes），把"哪些是已有事实、哪些是新增需求"钉在 tag 的 release notes 里，类似这样：

> 以 3 个仓的遗留代码和已实现的功能作为基础，继续开发视频课程平台功能。
> - 公开遗留的 LS 内容库 API（保留不动）
> - mp-app 视频平台是在"线下课报名"老小程序上叠的
> - 已实现 API / feature 以 shared/openapi.yaml 为准
> - 3 个仓各自的 CLAUDE.md 是遗留代码的总览

💡 **Actionable Tip：** Agent 不怕你啰嗦，怕你不说。你觉得显然的东西，对它一点都不显然。每次开新会话，**第一件事**就是把"哪些是基线、哪些是演进"写清楚。

### 3.2 系统设计：拆 spec 不是"想新系统"，是"扫老代码"

我们第一版用"理想新系统"思路拆出了 7 个 spec（认证、课程目录、订单、支付……），打完 RC tag 回到目录读 release notes，立刻发现两个偏差：

- 整个缺失"优惠券"（用户当面点出来）
- 整个集合过于"理想化"，完全没体现 LS 遗留域、用户中心、内容管理、线下课报名这些**已存在业务**

立刻回炉，最终落地 **13 份 spec**，按 4 类组织：

- **平台核心**：AUTH / ORDER / PAYMENT
- **演进型（已存在但需明确）**：LIVE_COURSES / VIDEO_COURSES / USER_PROFILE / USER_MANAGEMENT / MEDIA_UPLOADS
- **遗留 LS 内容域**：STRUCTURES_CONTENT / STRINGS_GROUPS / CONTENT_ENGAGEMENT（保留但不再主动改）
- **新增（gap）**：COUPON_PROMOTION / PROTOTYPE_TOOL

💡 **Actionable Tip：** 拆 spec 必须用**真实代码与真实接口**作证据，而不是凭需求文档"自上而下"命名。`backend/index.js + backend/models/*.js` 是事实之源，需求文档只是输入。

### 3.3 把"不要碰"写在契约里，不是写在文档里

遗留代码的"存在感"会不断干扰 AI 的判断——"我看到这段代码，是不是要重构它？"

我们的做法是：把 `EntryWriteRequest / EntryContent / EntryGroupWriteRequest` 这类 schema 显式打上"遗留的 liberating-structures 小程序接口，非当前视频平台核心需求"的 description 备注，并在 `info.description` 写明项目分层。

用 OpenAPI 的 `tags: [Platform Core] / [Legacy LS Content]` 给 AI 立边界，比写在 CLAUDE.md 里更难被忽略。

### 3.4 路由 ≠ 已实现

这条是血泪教训。

我们反推 openapi.yaml 时，`backend/index.js` 里挂了 30+ 个路由，但实际模型里只实现了 17 个。最终用户会把这个"假实现"当真——上线就崩。

💡 **Actionable Tip：** 扫 openapi 时**必须**同时扫 `routes/*.js + models/*.js`，区分"已实现"vs"只在路由里挂名"。请求 schema 加 `additionalProperties: false`，从宽松的 `type: object` 收紧到后端真实读取的字段。

### 3.5 永远做覆盖率审计

多仓遗留系统的 spec 集不是"写完即完"。我们做完 13 份 spec 后，对照原始需求 v5 文档做**需求→spec 覆盖率矩阵**，发现：

- COUPON_PROMOTION 是用户当面点出来才补的
- SCRM_ENGAGEMENT / REFERRAL_TRACKING / COURSE_SCHEDULE 三个 gap 还在

不审计，一定漏。

---

## 四、问题 2：怎么减少上线后的不稳定、减少紧急修复？

### 4.1 拆分细了才好审查

不管多大的功能，都要在 plan 阶段拆成"每次提交做一件事"的粒度。原因很简单：

> 拆分细了，审查的人（包括你自己）才能看得过来。
> 单仓内 rebase + 跨仓 release gate，三步到位。

可以先让 Agent 审查，但人要兜底。Agent 审查能抓住格式、命名、明显的逻辑错误，但**业务逻辑对不对、边界条件有没有漏，这些还是得人来判断**。

我们的 reviewer agent 有个硬约束：**REQUEST_CHANGES 不是失败，是"拦截越界"**。6/23 admin-courses 表格的 task 改到 course-detail / shared forms 已经是 forbidden 路径，reviewer 喊停是职责所在，不是流程摩擦。

### 4.2 自动化测试覆盖

这是减少线上事故最直接的杠杆。

**单元测试**：给 Agent 即时反馈的基础——它改了代码跑一下测试就知道有没有破坏已有功能。

**集成测试**：单个模块测试通过不代表组装在一起也没问题。用模拟数据把主要流程的集成测试覆盖起来。

**CI 集成**：单元 + 集成测试要跟 CI 集成，提交就自动跑，不要依赖人手动触发。

**端到端测试**：直接连测试环境或正式环境，通常没那么稳定，但可以代替大部分人工测试。现在有 AI 加持的 browser use 工具，写端到端测试比以前容易很多。

**最后一点：自己多测试，不要过于信任 AI，尤其是关键路径和涉及钱的流程。**

### 4.3 灰度发布 + Feature Flag

新功能不要着急全量上线。先给内部人用，测试没问题了再逐步扩大范围。加上 feature flag 开关，有问题随时关闭，不用等发版。

### 4.4 CI/CD：回滚速度决定事故影响范围

建立自动化部署、发布和回滚机制。修复了问题能以最快速度自动化验证和发布；出了严重问题**第一时间回滚**到稳定版本。

### 4.5 "Phantom merge" 必须正向校验

这是我们 2026-06-22 踩的一次大坑：OFFLINE_COURSE_SYNC-007 registry 里标 `MERGED`，但实际 `branch tip` **没在 admin master 里**。

原因：`merge-all.sh` 的 merge 命令看似成功（exit 0），但实际 push 失败 / merge commit 没真生成 / 后被 force-push 覆盖。

**修复**：

```bash
git merge --no-ff feat/$BRANCH -m "merge: $TID"
# 关键新增：merge 完成后立刻 verify
if ! git merge-base --is-ancestor HEAD origin/$DEFAULT_BRANCH; then
  git reset --hard HEAD~1   # rollback
  exit 1
fi
```

💡 **Actionable Tip：** **"merge 成功"≠"代码真的进了 main"。** 当脚本链条超过 5 步，必须在最后一步做正向校验（ancestor / build / test），不能相信中间步的 exit code。

可选 `BUILD_CHECK=1`（默认 0）：merge 后跑 `ng build` / `npm test`，失败同样 rollback。

---

## 五、问题 3：怎么让 AI 自行复现、debug、修复线上 bug 并提交 PR？

### 5.1 先说现实路径

说实话，让 AI **全自动**修线上 bug 听起来很美好，但目前更现实的路径是：**AI 辅助定位问题、生成修复方案、人来确认后再提交**。

全自动闭环修复，对大多数团队来说还不成熟。建立好的开发、测试、发布流程比追求全自动化更重要。

但即便走"AI 辅助 + 人确认"这条路，**前置基建也必须做**：

### 5.2 记日志：没日志一切免谈

补充一点：**日志要结构化，要有足够的上下文信息**（请求 ID、用户 ID、关键参数、关键状态）。

这样 Agent 拿到日志才能还原现场。否则给它一堆无意义的 print 输出，它也分析不出什么。

### 5.3 能重现和验证

建立一套和生产环境尽量一致的测试环境，能重现问题、能验证修复。

**没有重现环境，Agent 只能靠猜，修出来的东西你也不敢上线。**

### 5.4 CI/CD：不要修一个 bug 搞出三个新问题

修 bug 本身可能很简单，但**自动化测试跑通了再合并**，这个纪律不能省。

### 5.5 `/hotfix` 命令：主控在 crisis 时的逃生通道

我们加了一个 `/hotfix <TASK-ID> <app-repo> <commit-sha>` 命令，**追溯式**生成 task spec + handoff + review + registry MERGED。

适用条件有 4 个 ✅（全打勾才能走）：

- bug 阻断 P0 交易
- 改动 ≤ 5 个文件
- 不跨仓
- 不动 `.openspec/shared/` 跨仓契约 + 不动 `.openspec/specs/` 业务规格

任一不满足就走正规流程。`/hotfix` 不是"事后补单据"，是"主控 Claude 在 crisis 时的逃生通道"——4 个 ✅ 是为了避免 `/hotfix` 命令被滥用为"随便写点东西"。

首个真实案例：`PAYMENT-008`（mp-app commit `0b8e51f`，2026-06-22 retroactive 补登记）。

### 5.6 Session 中断必须可恢复

P0 bug 在凌晨来的时候，最容易出 session 429 中断。我们 6/19 凌晨 ADMIN_POLISH_V3 就中过一次。

**做法**：写 `SESSION_INTERRUPT_<DATE>.md` 到 `handoff/<FEATURE>/`，下次重启时**第一件事读这个**。内容 5 段：已交付 / 中断（partial）/ 未开始 / Registry 状态 / Resume 指令。partial 段的代码**不能 stash / reset**，直接让新 agent 读 diff。

💡 **Actionable Tip：** 与其追求 AI 自己修 bug，不如追求 AI 帮你少写 bug。前面说的需求分析、系统设计、测试覆盖、代码审查做好了，需要紧急修的 bug 自然就少了。

---

## 六、swarm-orchestrator 工程解剖

上面回答的是"为什么"和"怎么做"。下面给你看"长什么样"——把我们搭的脚手架原原本本摊开。

### 6.1 仓库布局：4 个仓的分工

```
~/work/001 优普丰课程平台多仓AI编码工程/
├── swarm-orchestrator/    ← 4 号仓（Source of Truth，不写业务代码）
├── mp-app/                ← 1 号仓（WeChat 小程序）
├── backend/               ← 2 号仓（Express 后端）
└── admin/                 ← 3 号仓（Angular 管理后台）
```

业务仓里**没有** `.openspec/`。Subagent 写交付物时**不会**污染业务仓的 main 分支——这是通过 worktree 内的 symlink 桥接实现的（详见后文）。

### 6.2 swarm-orchestrator 内部结构

```
swarm-orchestrator/
├── .openspec/
│   ├── specs/             # 13 份 feature spec
│   ├── plans/             # 每个 feature 的实施 plan
│   ├── tasks/<FEATURE>/   # 每个 task 的详细 spec
│   ├── handoff/<FEATURE>/ # Subagent 的交付物
│   ├── reviews/<FEATURE>/ # 跨仓一致性 review
│   ├── registry/          # agents.yaml + tasks.yaml
│   ├── shared/            # 跨仓契约（openapi / types / config）
│   ├── swarm/             # 5 个核心 bash 脚本
│   └── templates/         # spec / plan / task / handoff / review 模板
├── .claude/
│   ├── agents/            # 6 个 subagent prompt
│   └── commands/          # 7 个 slash command
├── CLAUDE.md              # 跨仓宪法（路径硬规则 + 失败模式速查）
└── README.md              # 仓库布局 + 操作步骤
```

### 6.3 6 个 subagent：每个角色都有清晰的边界

`.claude/agents/` 下有 6 份 agent prompt，每一份都是这个角色**专属的工作环境 + 边界 + 产出模板**：

**① `architect.md`** — 跨仓契约 owner。

- **独占权**：`.openspec/shared/openapi.yaml`、`.openspec/shared/types.ts`、`.openspec/shared/config.schema.json`、`.openspec/plans/**`、`.openspec/tasks/**`
- **产出**：读 spec → 写 plan → 拆 task → 更新 `registry/tasks.yaml`（每个下游 task 标 `app_repo`）→ 写 `.openspec/shared/openapi.yaml` → 写 handoff
- **铁律**：禁止改 `../mp-app/`、`../backend/`、`../admin/` 下任何文件；禁止 push main

**② `backend-agent.md`** — Express 后端实现者。

- 工作目录：`../backend/wt-<task>/`
- 第一件事：把跨仓契约从 orchestrator 拉到 `types/`（`openapi.yaml` + `types.ts` + `config.schema.json`）
- 改 `models/` `utils/` `index.js` `test/`，push 到 `feat/<task>` 分支

**③ `miniprogram-agent.md`** — WeChat 小程序实现者。

- 工作目录：`../mp-app/wt-<task>/`
- 跨仓契约拉下来后，`types.ts` 会被改成 `.d.ts + 注释结构`（小程序对 TS 不友好）
- 改 `pages/<feature>/`，push 到 `feat/<task>` 分支

**④ `admin-agent.md`** — Angular 管理后台实现者。

- 工作目录：`../admin/wt-<task>/`
- **多一步**：`npx openapi-typescript` 自动把 openapi.yaml 转成 TS 客户端（生成 `src/app/shared/api-types.ts`）
- 改 `src/app/modules/`、`src/app/store/`、push 到 `feat/<task>` 分支

**⑤ `docs-agent.md`** — API 文档生成者。

- **不写业务代码**，只写 API 文档到 `.openspec/docs/**`
- 边界明确：shared / plans / tasks 都是 architect 独占，docs-agent 不碰

**⑥ `reviewer-agent.md`** — 跨仓一致性 reviewer。

- **只读 + 写 review**，不写代码
- 跨仓一致性 checklist：API 字段一致 / Type 一致 / Auth 一致 / 错误码一致 / 跨仓写边界 / **编译测试真跑过**
- 硬约束：**1:1 review-per-task 约定**——每个被 review 的工作 task 都必须有自己的 review file，缺一个 → 该 task 永远 stuck 在 REVIEWED 不入 main
- 文件名严格用 `<TASK-ID>.md`，不带 `-review` / `-umbrella` / `-stub` 等后缀

### 6.4 7 个 slash command：从 spec 到 merge 的完整闭环

`.claude/commands/` 下有 7 份 slash command prompt，每一份都是一段状态机切片：

**① `spec.md` — 写需求**

读用户口述 → 加载 `spec-template.md` → 跟用户确认 4 件事（涉及哪几个端 / 是否需要 architect 先做跨仓契约 / 是否需要 e2e / Done Definition 是什么）→ 写入 `.openspec/specs/<FEATURE>.md`。

> **下一步**：`/plan <FEATURE>`。禁止直接 `/swarm`——没 plan / tasks.yaml 时 swarm 没东西可调度。

**② `plan.md` — 拆 plan + 拆 task**

读 spec → 加载 `plan-template.md` / `task-template.md` → 起 architect subagent → 校验（plan 文件存在 / 每个 task 列 Allowed/Forbidden / `tasks.yaml` 有 `app_repo` 字段 / app_repo 合法 4 选 1）→ 同步 registry status。

> **关键差异（vs monorepo 版）**：多一个字段 `app_repo`；跨仓契约统一在 `.openspec/shared/`，plan 必须列哪些 task 是 architect 独占的。

**③ `swarm.md` — 调度**

读 registry → 拓扑排序（按 `depends_on`）→ 跑 `create-worktrees.sh`（按 app_repo 分组，在各自业务仓里建 worktree）→ 跑 `sync-shared.sh all`（业务仓拉最新契约）→ 起 Subagent（注入 `HANDOFF_DIR / REVIEW_DIR / SHARED_DIR` 环境变量）→ 聚合结果。

> **如果 FEATURE plan 包含 umbrella reviewer task**（`FEATURE-<N+1>`，owner = `reviewer-agent`），那么 `/swarm` 阶段已经一次性完成 review：reviewer 写 1:1 review files + 同步 `tasks.yaml` status → `REVIEWED`。**不需要**单独跑 `/review`。

**④ `review.md` — 跨仓一致性 Review**

只适用于以下场景：
- 老式 plan 风格（plan 里**没**有 umbrella reviewer task）
- hotfix 后重审（REQUEST_CHANGES → APPROVED 后需要刷新 review file）
- 手动 review 路径

**⑤ `merge.md` — 跨仓 merge**

按 `tasks.yaml` 拓扑顺序 + `app_repo` 分组，把 APPROVED 的任务分别 merge 到 3 个业务仓的 main。

**关键纪律**：
- 不带参数：处理所有 APPROVED 的 task
- 带 FEATURE：只处理该 Feature
- 前置校验：每个 task 必须 `status: REVIEWED`，否则跳过并打 `↷ X: status=IN_PROGRESS，not yet REVIEWED，跳过 merge`
- 跨仓 release 协调：`backend → 等部署 OK → mp / admin`
- 脚本默认**不暂停**（`RELEASE_GATE=0`），要开 gate 跑 `RELEASE_GATE=1 bash merge-all.sh`

**⑥ `status.md` — 一张表看全**

读 tasks.yaml registry status → 列跨仓 worktree → 列 handoff 落地 → 列 review 状态 → 跑契约一致性检查。

> 输出示例：每个 task 按 ✓ / ⏳ / ✗ 三个状态色展示，按 `app_repo` 分组——主控一眼能看出哪些 task 卡在哪。

**⑦ `hotfix.md` — P0 hotfix 追溯式补全**

> ⚠️ **专为"来不及走正规流程"的 P0 hotfix 设计。** 严禁把 normal-flow 任务伪装成 hotfix 走本命令——会绕过 reviewer-agent 的质量门。

适用条件 4 个 ✅（全打勾才能走）：bug 阻断 P0 交易 / 改动 ≤ 5 个文件 / 不跨仓 / 不动 `.openspec/shared/` + `.openspec/specs/`。

追溯式生成：task spec + handoff + review（强制 APPROVED retroactive）+ 追加 registry MERGED。

### 6.5 跨仓 symlink 桥：handoff 不污染业务仓

这是企业遗留系统能放心让 AI 写代码的关键设计：

业务仓里**没有** `.openspec/`。Subagent 写交付物时如果用相对路径（`.openspec/handoff/...`），要么落到业务仓 main 分支（污染），要么报错。

**解决**：`create-worktrees.sh` 给每个**业务仓**的 worktree 自动建一个 `.openspec` symlink：

```bash
ln -s ../../swarm-orchestrator/.openspec  backend/wt-auth-002/.openspec
ln -s ../../swarm-orchestrator/.openspec  mp-app/wt-auth-003/.openspec
ln -s ../../swarm-orchestrator/.openspec  admin/wt-auth-004/.openspec
```

Subagent 在 wt- 里写 `.openspec/handoff/AUTH/AUTH-002.md` → 实际写到 orchestrator 仓。**handoff / reviews 永远在 orchestrator 落地，不污染业务仓 main**。

orchestrator 自己的 worktree（`wt-auth-001-arch`、`wt-auth-005-doc`）不走 symlink，因为 `.openspec/` 本来就在仓里。

### 6.6 跨仓契约"单一真相源"

`.openspec/shared/` 是**唯一的接口事实之源**：

- `openapi.yaml` — API 字段 + 路径 + 类型
- `types.ts` — TS 类型定义
- `config.schema.json` — 跨仓配置 schema

**权限表**：

- architect agent → 唯一能写的人
- 其他任何人 → **只读**
- 业务仓需要时 → 走 `bash $ORCHESTRATOR_ROOT/.openspec/swarm/sync-shared.sh` 拉取

Subagent prompt 里明确写：

> 禁止在 `mp-app/types/` 或 `backend/types/` 直接写跨仓类型，只能跑 sync 脚本。

这是 3 仓版本能 work 的核心机制。没有这层"单一真相源 + sync 拉取"，3 仓独立就退化成"3 份维护负担"。

---

## 七、4 个 commands 的演进（这一个月发生了什么）

脚手架不是一天搭好的。下面是 7 个 slash command 一个月里的演进路径，每一步都有具体 commit 和真实教训。

### 7.1 第 1 周：spec / plan / swarm / review / merge / status 6 件套打地基

最初的 monorepo 模板只有 `/spec /plan /swarm /review /merge` 5 个。6/6 拆 4 仓时加了 2 个字段（`app_repo` + handoff 路径硬规则）。

最关键的设计是**主控永远在 orchestrator**。`README.md`「Where to run claude」表里写：

> 写需求 / 拆 plan / 启动 swarm：`swarm-orchestrator/` → `claude` → `/spec /plan /swarm /review /merge /status`
> 改业务代码（不通过 swarm）：`mp-app/` 或 `backend/` 或 `admin/` → `cd <业务仓> && claude`
> 改 agent prompt：直接编辑 `swarm-orchestrator/.claude/agents/`
> 改 slash command：直接编辑 `swarm-orchestrator/.claude/commands/`

**核心规则**：主控永远在 orchestrator。**不要**在业务仓直接 `claude` 跑 `/swarm` 之类的主控命令。

### 7.2 第 2 周：status 字段 + handoff 命名严格化

最初 `tasks.yaml` 里没有 status 字段，"哪个 task 已合并"靠人脑或 grep handoff 文件。结果 `/swarm` 把已合并的 task 又起了一遍 worktree，`/merge` 在已删除的分支上 rebase 报错。

修法：先把状态机写死：

```yaml
# status 枚举：
#   PROPOSED      —— 已建任务，未起 agent
#   IN_PROGRESS   —— launch-agents.sh 起 agent 时改写
#   REVIEWED      —— /review 写完且 APPROVED 后改写
#   MERGED        —— merge-all.sh 成功合并后改写
```

然后把"谁改哪个状态"分摊给 4 个 slash command 的 prompt：

- `/plan` 创建 task = PROPOSED
- `/swarm` 起 agent = PROPOSED → IN_PROGRESS（脚本里 python 原子改 yaml）
- `/review` 写完 = APPROVED → REVIEWED / REQUEST_CHANGES → IN_PROGRESS（review skill prompt 内嵌 python 多行脚本）
- `/merge` 合并成功 = REVIEWED → MERGED（merge-all.sh 改 yaml）

**元经验**：状态机**不要交给主控 Claude 维护**，要切片分给每个负责执行的 skill。主控不可能记住 4 个状态转换发生在何处，但每个 skill prompt 都能在自己那一格做"原子的状态推进"。

同时把 handoff 文件命名严格化：`<TASK-ID>.md`，**不**带 owner 后缀（`-arch` / `-api` / `-mp` / `-admin` / `-doc` / `-review` / `-umbrella` 一律不用）。owner 信息落文件正文 `## Owner` 段。脚本里删了 owner-suffix fallback——**约定不够，必须代码强制**。

### 7.3 第 3 周：umbrella reviewer + `/swarm` 一体化 `/review`

老 reviewer 写 1 个 umbrella 算完 → merge 阶段 skip 了 3 个 task。修法：

新规则——1 个 umbrella reviewer 任务 = 写 1 个详细 umbrella + N 个 per-task stub。每个 stub 必须含 `**APPROVED**`（或 `REQUEST_CHANGES`）in `## Decision` 段。缺一个 stub → 该 task 永远 stuck 在 REVIEWED 不入 main。

**自检脚本**（每个 reviewer 任务完成前必跑）：

```bash
for f in .openspec/tasks/FEATURE/*.md; do
  tid=$(basename "$f" .md)
  rf=".openspec/reviews/FEATURE/$tid.md"
  if ! awk '/^## Decision/{flag=1; next} /^## /{flag=0} flag && /\*\*APPROVED\*\*/{found=1; exit} END{exit !found}' "$rf"; then
    echo "✗ NO '**APPROVED**' in Decision section: $rf"
  fi
done
```

**收益**：从"3 步等用户"变"2 步等用户"，省一次 slash command 调用 + 一次 subagent 启动。`/swarm` 命令的"下一步"从 `/review` 改为 `/merge`。

### 7.4 第 4 周：`/hotfix` 命令 + P0-P2 hardening

P0 bug 阻断交易时，来不及走 `/plan→/swarm→/review→/merge` 全流程。业务仓已经直接 commit 了，事后需要补 audit trail。

**解法**：新 `/hotfix <TASK-ID> <app-repo> <commit-sha>` 命令，**追溯式**生成 task spec + handoff + review + registry MERGED。首个真实案例：`PAYMENT-008`（mp-app commit `0b8e51f`，2026-06-22 retroactive 补登记）。

同步落地的 P0-P2 hardening 6 项：

- **P0-1**：phantom merge guard — `merge-all.sh` merge 后 verify `git merge-base --is-ancestor HEAD origin/main`，否则 rollback
- **P0-2**：可选 `BUILD_CHECK=1` — merge 后跑 `ng build` / `npm test`，失败 rollback
- **P1-3**：worktree 路径校验 — `create-worktrees.sh` 拒绝 `../admin`（应是 `../admin/wt-<task>`）
- **P1-4**：枚举统一 — handoff `Status` 段 4 态，review `Decision` 段 3 verdict，模板与 schema 用同一组枚举
- **P2-5**：APPROVED 检测精确化 — 9 种历史格式兼容，避免"previous APPROVED inside REJECTED"误判
- **P2-6**：last_verification 字段 — merge 成功后写 `{merged_at, method}` 到 registry

**核心教训**：**"看着 exit 0"是工程里最深的坑**。任何链条长过 3 步的脚本，最后一步必须做正向校验（ancestor / build / test），不能相信中间步。

---

## 八、我们这一周的真实数据（6/18-6/24）

**维度 → 数字 → 说明：**

- **Git commits** → 53 → 6/18 上午到 6/23 下午
- **业务 feature 收尾** → 4 个 → ADMIN_POLISH_V3 / OFFLINE_COURSE_SYNC / ENROLLMENT_PIPELINE_HARDENING / ADMIN_UI_TABLE_FIX 等
- **框架代码 commit** → 3 个 → P0-P2 hardening / `/hotfix` 模板 / umbrella reviewer 一体化
- **写新业务代码** → 0 → 全部在 swarming + hardening

**关键判断**：6/18-6/24 是「从建设期 → 运维期」的转折点。前两周几乎都在建流程（spec / plan / tasks / handoff / review / merge）；从这周开始，**流程基本就位，进入"边跑边补"阶段**。

---

## 九、6 条铁律（带走版）

**1. 先冻结"基线"，再谈演进。** 跑 `/release-skills` 给当前 git 状态打一个 release candidate tag（带 release notes），把"哪些是已有事实、哪些是新增需求"钉在 release notes 里，否则 AI 会把遗留代码当"待清理对象"。

**2. 以真实代码反推契约，不要用需求文档正推。** `backend/index.js + backend/models/*.js` 是事实之源，需求文档只是输入。架构师 agent 用 `shared/openapi.yaml` 重新反推——5 个阶段：扫路由 → 扫模型 → 写 schema → 区分已实现 / 挂名 → 按范围打 tag。

**3. 共享契约文件要"独占维护"。** `shared/openapi.yaml` 由 architect agent 独占维护，业务仓 agent 只读 + 同步拉取。这是为数不多必须"中心化"的设计——分散写一定会出三仓字段对不上的问题。

**4. 状态机不要交给主控维护，要切片分给每个负责执行的 skill。** `tasks.yaml` 里只允许 4 个状态枚举（PROPOSED / IN_PROGRESS / REVIEWED / MERGED），状态注释写在 yaml 顶部；每个 slash command 的 prompt 内嵌 Python 脚本做"原子的状态推进"。派生状态（ready / blocked）必须脚本入口现算，不要人手维护。

**5. 任何"看着 exit 0"的脚本步骤都要加正向校验。** ancestor / build / test / 业务码 grep，缺一不可。脚本 fallback 短期友好、长期反模式——删 fallback 让 strict 路径生效。"Phantom merge guard"不是 nice-to-have，是脚本链条超过 5 步时的必填项。

**6. CLAUDE.md 不是文档，是法律。** 凡是踩过两次以上的坑、凡是 AI 容易猜错的目录约定、凡是涉及跨仓边界的硬规则——都要写进 CLAUDE.md，而且要写得像法条一样可执行（"做什么 → 在哪做 → 用什么命令"）。CLAUDE.md 是稳态规则；SESSION_INTERRUPT_*.md 是动态状态——两者分工明确，不要混。

---

## 十、这套脚手架不是模板，是要贴着企业实际搭的

最后说点不太中听的。

这套 harness 之所以能在我们这个项目跑起来，是因为它**贴着我们的实际**：

- 3 仓不同的技术栈（Express + 小程序 + AngularJS）→ 决定 SoT 必须独立仓
- 跨仓改动 < 5% → 决定走 3 仓独立而非 monorepo
- 团队职责边界要清晰 → 决定 architect 独占契约
- 业务仓 release 节奏独立 → 决定跨仓 release 必须有 gate

**直接抄模板到你企业里，大概率跑不起来**。每个企业的遗留系统长得不一样：

- 你的仓可能是 5 个、10 个，技术栈可能是 Java + Go + Python + Vue 的组合
- 你的 CI 可能是 GitLab 不是 GitHub Actions
- 你的跨仓 release 节奏可能比我们的更松散
- 你的 reviewer agent 可能不需要我这种 1:1 per-task stub 的硬约束

**但 harness engineering 的核心思想是不变的：**

- 把"AI 写代码"这件事工程化对待，套上状态机、目录约定、脚本校验
- 让传统软件工程的所有纪律（需求 → 设计 → 编码 → 审查 → 测试 → 发布 → 监控）一个都没丢，只是执行主体变了
- 把"状态外部化到脚本"，因为 Agent 没有持久记忆，每次会话起来都从零开始
- 把"规则"从 CLAUDE.md 升级到"代码强制"，否则就是空话

---

## 十一、如果你想搭一套自己的 Harness Engineering 脚手架

我们提供 **企业级 AI 编码转型咨询顾问服务**，覆盖：

- **现状评估**：你企业的遗留系统 + 团队结构 + 现有 CI/CD 适配 AI 编码的程度
- **脚手架定制**：基于你的多仓结构、技术栈、release 节奏，定制 harness engineering 脚手架（不是套模板）
- **Pilot 落地**：选 1-2 个 feature 做端到端验证，把流程跑通
- **团队赋能**：把你团队的研发骨干培训成"AI 工程的脚手架搭建者"，而不是"AI 操作的执行者"

我们已经在 3 仓异构技术栈上把 13 份 spec、20+ 个 subagent、3 仓并发 merge 的完整流程跑通，**这周又迭代了一轮 P0-P2 hardening**。

如果你正在评估"AI 编码能不能用在我们这种企业遗留系统上"，欢迎把这篇文章转发给你的同事、架构师、技术总监——它就是我们能力的真实证明。

> 联系方式：jackyshen@uperform.cn
> 项目实践：基于真实生产系统的 3 仓异构技术栈 AI 编码工程

---

**💬 讨论**

如果让你给自己企业的遗留系统搭一套 AI 编码脚手架，你会先做哪一步——先写 spec，还是先把跨仓契约的反推做对？

---

*Author: 申导*
*Source: AI Coding 转型实战*