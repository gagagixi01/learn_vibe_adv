# 01. 选择真实项目，并看懂双工具工作流

## 预计用时

40-55 分钟。

## 本节位置

本节先判断为什么选择 `OverClocked Helpdesk`项目，默认前提是：你已经在 `00` 里完成课程总览，并整理过一份 `OpenSpec` / `Superpowers` 工具对照笔记。

## 工具使用规则

终端只用于创建课程笔记目录。`Claude Code CLI` 暂不启动；本节先由学生阅读项目介绍、理解工作流、整理笔记。

## 学习目标

- 知道所选 GitHub 项目的业务背景和技术栈。
- 判断它为什么适合进阶练习。
- 知道它为什么适合 `OpenSpec + Superpowers` 的组合工作流。
- 能解释为什么 Change 1 是质量基线，而不是马上改业务行为。
- 能解释为什么 Slack 配置风险要拆成独立 bugfix change。
- 能说清不同 change 在 TDD 用法上的区别。
- 能把 OpenSpec artifacts 映射到 Superpowers skills。
- 能解释为什么这门课不直接用 `/opsx:apply` 代替计划和 TDD。

## 本节产出物

- `learn_vibe_adv/notes/repo-selection.md`
- `learn_vibe_adv/notes/workflow-map.md`

## 操作步骤

### 第 1 步：在终端创建笔记目录

在终端执行：

```bash
mkdir -p learn_vibe_adv/notes
```

如果你上一节还没有把工具对照整理成文件，这一步之前先补上：

```text
notes/tool-comparison.md
```

### 第 2 步：阅读项目定位

项目地址：

```text
https://github.com/gagagixi01/OverClocked-Helpdesk
```

重点阅读 README 里的四块：

- 项目目标：面向黑客松和校园活动的 Helpdesk。
- 技术栈：Python、FastAPI、SQLAlchemy、SQLite、Jinja2、Uvicorn。
- 功能：团队提交问题、管理员管理导师和问题、导师查看和响应分配问题。
- Future Scope：认证、WebSocket、Docker、Analytics。

### 第 3 步：判断它为什么适合双工具工作流

你这一节不是只回答“这个项目好不好”，而是回答：

- 它是否适合用 OpenSpec 管 change
- 它是否适合用 Superpowers 做计划、TDD 和 review
- 它是否适合拆成多轮小 change，而不是一次性重构

`OverClocked Helpdesk` 适合 `OpenSpec`，因为：

- 它已经有真实业务流程，不适合靠聊天临时定义全部需求。
- 它有多角色和多页面，change 需要明确边界。
- 它有 Future Scope，特别需要写清“这轮不做什么”。

它适合 `Superpowers`，因为：

- 它不是空项目，直接动代码更容易破坏现有行为。
- 它有服务、路由、模板和数据模型，适合做 TDD 和 review。
- 它可以拆成一轮轮小优化，适合 plan -> task -> verify 的节奏。

### 第 4 步：写项目选型笔记

在终端创建或编辑 `notes/repo-selection.md`。如果你在 Claude Code 里写，也必须要求它只写这一个笔记文件，不克隆、不改项目。

参考结构：

```markdown
# 项目选型笔记

## 项目名称

OverClocked Helpdesk

## 为什么适合进阶课

- 技术栈承接 learn_vibe：FastAPI、SQLite、Jinja2。
- 已经有多角色流程，比 Todo 更复杂。
- 项目规模可读，不是大型生产系统。
- 有自然优化点：测试、状态流转、仪表盘、通知边界。

## 为什么适合 OpenSpec

- 可以把每一轮优化收成独立 change。
- 可以明确写清哪些 Future Scope 这轮不做。
- 可以把需求和验收标准从聊天里拿出来，放进 change artifacts。

## 为什么适合 Superpowers

- 需要先做设计澄清，而不是直接乱改代码。
- 适合先写测试，再改 assignment、lifecycle、dashboard。
- 适合用 plan、review 和 verify 控制每轮优化范围。

## 本课程不做什么

- 不把它重写成新项目。
- 不直接上 React。
- 不先做部署和 Docker。
- 不一次性实现所有 Future Scope。

## 我最想优化的第一件事

先补导师分配和问题创建测试，建立质量基线。
```

### 第 5 步：记住本课程的标准流程

这门课采用的标准流程是：

```text
baseline -> OpenSpec propose -> spec/design/tasks
-> Superpowers brainstorming -> writing-plans
-> TDD -> subagent-driven-development -> review -> verify -> archive
```

你可以把它拆成三段来理解：

- 前段：先读懂已有项目，记录 baseline，避免盲改。
- 中段：先用 OpenSpec 锁定 change，再用 Superpowers 深化设计和计划。
- 后段：测试驱动实现、review、验证、归档。

### 第 6 步：理解为什么要分成四个小 change

不要把所有问题混成一个大改动，而是分成四个独立 change。**不同 change 在解决不同类型的问题。**

```text
先读懂 OverClocked Helpdesk
-> Change 1：用测试锁住当前 assignment / query creation 行为
-> Change 2：修复 Slack webhook 缺失配置的后台稳定性风险
-> Change 3：改进 dashboard 可读性，让页面变化可观察
-> Change 4：配置 QR base URL，让后台能力变稳但默认页面不变
```

可以这样理解四个 change：

- **Change 1 = 建质量基线**
  - 目标：先把测试基础设施搭起来，再把当前行为测出来、锁住
  - 分支：测试基础设施、mentor assignment 测试、query creation 测试
  - 正常结果：这一轮可能主要新增测试、fixture、行为记录，而不是马上改 service 代码
  - 重点：先把现状测清楚，再谈后续怎么改

- **Change 2 = 修一个真实小 bug**
  - 目标：把 baseline 里发现的 `SLACK_WEBHOOK_URL=None` 风险变成受控 bugfix
  - 重点：bugfix 也要先有 OpenSpec change，再用 Superpowers 做 TDD

- **Change 3 = 做一个可见变化**
  - 目标：改善管理端和导师端的可读性与统计信息
  - 重点：让学生练习页面 before / after evidence，证明可见变化对应 OpenSpec 验收标准

- **Change 4 = 做一个不可见变化**
  - 目标：把 QR 码中的硬编码本地地址改成可配置 base URL
  - 重点：默认页面不变，但测试能证明后台配置能力更稳

### 第 7 步：理解不同 change 里的 TDD 为什么不完全一样

`00` 已经讲过 `Red -> Green -> Refactor` 的基本节奏，但在这门课里，TDD 在不同轮次承担的角色并不完全相同。

- **Change 1 的 TDD**
  - 重点是先把当前行为测出来、锁住
  - 红灯可能来自缺测试、缺 fixture、缺测试框架，或者当前行为与记录预期不一致
  - 目的不是立刻把业务改“正确”，而是先建立安全边界

- **Change 2 的 TDD**
  - 重点是先复现一个具体 bug：缺失 webhook 时不应该抛异常
  - 红灯来自“当前实现仍然调用 `requests.post(None, ...)`”
  - 目标不是接入真实 Slack，而是用最小 guard 让缺失配置安全返回 `False`

- **Change 3 的 TDD**
  - 重点是推动明确的可见页面变化
  - 红灯来自“目标行为还没实现”
  - 这时才更像很多人直觉里的 TDD：先写失败测试，再改路由或模板让它变绿

- **Change 4 的 TDD**
  - 重点是证明默认页面不变，但 QR 生成内部使用可配置 base URL
  - 红灯来自“当前实现仍然把 `http://127.0.0.1:8000` 写死在 QR 内容里”
  - 目标不是重做 QR 页面，而是把硬编码配置化并用测试保护

### 第 8 步：理解每个阶段分别由谁主导

下面这张角色表很关键：

| 阶段 | 主导工具 | 它负责什么 |
|---|---|---|
| baseline | 人 + 项目阅读 | 读懂当前代码和行为，记录起点 |
| propose | OpenSpec | 定义 change 的 why、what、scope |
| spec/design/tasks | OpenSpec | 保存 change 规格、设计和任务框架 |
| brainstorming | Superpowers | 深化技术设计，补测试策略和边界条件 |
| writing-plans | Superpowers | 把设计转成可执行计划 |
| TDD | Superpowers 方法 | 先失败测试，再最小实现 |
| subagent-driven-development | Superpowers | 按计划逐任务实现，并插入 review |
| review | 人 + Superpowers | 检查输出是否符合 OpenSpec、真实 plan、`git diff` 和代码质量要求 |
| verify | OpenSpec + Superpowers | 对照 spec 验证功能，检查实现完成度 |
| archive | OpenSpec | 把已完成 change 归档到历史里 |

如果你感觉记不住，可以记这个简化版：

```text
OpenSpec 负责 change 的边界和历史，
Superpowers 负责 change 的设计深化、实现纪律和 review。
```

### 第 9 步：把 OpenSpec artifacts 映射到 Superpowers skills

在真实工作里，这两者不是各做各的，它们有明确的输入输出关系。

| OpenSpec 产物 | 给谁用 | 在后续里扮演什么角色 |
|---|---|---|
| `proposal.md` | Superpowers brainstorming | 提供 change 目标、范围和非目标 |
| `specs/` | Superpowers brainstorming | 提供需求和验收场景 |
| `design.md` | Superpowers brainstorming / writing-plans | 提供已有技术约束和思路 |
| `tasks.md` | Superpowers writing-plans / execution | 提供任务边界和实施起点 |
| verify 结果 | OpenSpec archive / Superpowers finishing | 证明这个 change 是否能闭环 |

也就是说，Superpowers 不是凭空开始工作的。它的输入是 **OpenSpec 已经整理好的 change 上下文**。

### 第 10 步：理解“单独使用”与“组合使用”的区别

| 用法             | 优点                                    | 短板                           |
| -------------- | ------------------------------------- | ---------------------------- |
| 只用 OpenSpec    | 规格和 change 历史更清晰，范围更容易收住              | 代码实现阶段可能缺 TDD、缺 review、缺执行纪律 |
| 只用 Superpowers | 设计、计划、TDD 和 review 更强                 | 前期需求输入和 change 历史容易散在聊天里     |
| 组合使用           | durable scope + disciplined execution | 成本比单一工具高，但更适合真实项目优化          |

不用“最快捷”的路线。不是因为快捷路线一定错，课程目标不是“一次生成尽量多代码”，而是“学会构建一套可迁移的真实工作流”。

### 第 11 步：为什么这门课不直接用 `/opsx:apply`

OpenSpec 官方工作流里有 `/opsx:apply`。它适合一些简单或已经非常明确的实现过程，不把它作为主路径，因为课程要教的是：

- 怎样把 spec 交给更强的设计讨论
- 怎样把设计变成清楚的 TDD 计划
- 怎样在实现阶段加入 review 和验证闭环

所以这门课的取舍是：

```text
OpenSpec 用来创建和保存 change，
Superpowers 用来替代“直接 apply”的较弱实现阶段。
```

不是否定 `/opsx:apply`，而是选择一条更能提升判断力的路径。

### 第 12 步：写工作流地图

创建或整理 `notes/workflow-map.md`。参考结构：

```markdown
# OpenSpec + Superpowers 工作流笔记

baseline ->
OpenSpec propose ->
spec/design/tasks ->
Superpowers brainstorming ->
writing-plans ->
TDD ->
subagent-driven-development ->
review ->
verify ->
archive

## Change 1：quality-baseline

1. baseline：先看清 OverClocked Helpdesk 目前怎样做导师分配和问题创建。
2. OpenSpec propose：把“补质量基线，不扩大功能范围”写成一个 change。
3. OpenSpec spec/design/tasks：保存 Change 1 要覆盖的行为和任务边界。
4. Superpowers brainstorming：补测试策略、边界条件、最小可测接口。
5. writing-plans：把设计变成逐步 TDD 计划。
6. TDD：先写 assignment 测试和 query creation 测试，先看失败。
7. subagent-driven-development：按计划逐任务实现，穿插 spec review 和 code review。
8. review：主会话对照 OpenSpec、真实 plan、`git diff`、allowed files 和测试结果检查是否越界。
9. verify：确认测试通过、网页 evidence 和验证报告能证明验收标准满足。
10. archive：把 Change 1 存档，进入下一轮。

## Change 2：stabilize-slack-notification-config

1. baseline：从 `notes/baseline.md` 或 design doc 中确认 Slack webhook 缺失配置风险。
2. OpenSpec propose：把“缺失 webhook 不应导致崩溃”写成独立 bugfix change。
3. Superpowers brainstorming：只深化缺失配置的测试策略和最小修复。
4. writing-plans：写清失败测试、mock `requests.post`、最小 guard 和回归测试。
5. TDD：先看到缺失 webhook 测试失败，再让 `send_slack_alert()` 安全返回 `False`。
6. evidence：页面通常无变化，分类为 `no visible change`，证据来自测试。

## Change 3：optimize-dashboard-readability

1. OpenSpec propose：把 admin stats 和 mentor status visibility 写成独立 dashboard change。
2. Superpowers brainstorming：只深化统计字段、数据来源和模板插入点。
3. writing-plans：先写 dashboard 测试，再改路由上下文和 Jinja 模板。
4. TDD：先看到统计或状态 badge 测试失败，再做最小可见实现。
5. evidence：页面有明确 before / after，分类为 `visible change`。

## Change 4：configure-qr-base-url

1. OpenSpec propose：把 QR 码硬编码 URL 风险写成独立配置 hardening change。
2. Superpowers brainstorming：只深化配置字段、默认值和测试策略。
3. writing-plans：先测试默认 URL 和自定义 base URL，再改配置和 QR 工具函数。
4. TDD：先看到 QR 内容仍使用硬编码地址，再让它读取 `APP_BASE_URL`。
5. evidence：默认页面无明显变化，分类为 `no visible change`，证据来自测试和 QR 生成验证。
```

### 第 13 步：检查选型是否过大

如果你的笔记里出现“重写架构”“全量改 UI”“加完整权限系统”，说明范围过大。Change 1 只做质量基线。

## 验收标准

运行或检查：

```bash
cat notes/repo-selection.md
cat notes/workflow-map.md
```

确认两份文档分别回答：

- `repo-selection.md`：为什么选这个项目，为什么适合双工具工作流。
- `workflow-map.md`：Change 1 怎样从 baseline 走到 archive。

通过标准：

- 你能说出项目为什么适合，而不是只说“看起来不错”。
- 你能把 Change 1 从 baseline 讲到 archive。
- 你能解释为什么 Change 1 可能主要新增测试，而不是马上改 service 代码。
- 你能说清不同 change 在 TDD 用法上的区别。
- 你能说清 Slack bugfix 为什么要独立成 Change 2。
- 你能说明为什么 query creation 仍然属于 Change 1，而不是自动掉进后续 change。
- 你知道从 `07` 开始要用 `notes/webpage-change-evidence.md` 记录 before / after。
- 两份笔记都能说明 OpenSpec 和 Superpowers 在本项目里的具体用途。

需要停下处理：

- 如果还没 clone 就想决定所有实现细节，先把“不做什么”和 workflow-map 补完整。

## 常见卡点

- 只关注技术栈，不理解业务流程。
- 被 Future Scope 吸引，忽略 Change 1 应该先补质量基线。
- 把“已有项目”当成“可以随便重写的素材”。
- 只记住命令顺序，却不知道每一步在解决什么问题。
- 以为 testing、verify、archive 只是收尾形式主义。
- 以为页面没有变化就代表 bugfix 没有价值。

## 参考来源

- 本地参考：`Clippings/OpenSpec + Superpowers SDD+TDD 双驱动 AI 编程工作流.md`
- [OpenSpec GitHub](https://github.com/Fission-AI/OpenSpec)
- [OpenSpec concepts](https://github.com/Fission-AI/OpenSpec/blob/main/docs/concepts.md)
- [Superpowers plugin](https://claude.com/plugins/superpowers)
- [Claude Code skills docs](https://code.claude.com/docs/en/slash-commands)
