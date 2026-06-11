# 00. 从做小项目到优化真实项目

## 预计用时

15-20 分钟。

## 本节位置

这是进阶路线的开场课。本节不克隆仓库、不安装依赖、不启动 Claude Code，只建立学习地图：你将从入门课的“从零做任务系统”，进入“读懂并优化一个已有 GitHub 项目”。

## 工具使用规则

本节通常不需要运行项目命令；如果还没有 `learn_vibe_adv/`，只用终端创建课程目录和 `notes/`。本节不启动 `Claude Code CLI`。全课统一工具边界见 `AGENT.md`。

## 学习目标

- 理解为什么进阶课必须使用已有项目，而不是继续从零生成。
- 能用自己的话解释 `OpenSpec` 和 `Superpowers` 分别是什么。
- 知道 `OpenSpec` 管什么，`Superpowers` 管什么，以及为什么这门课同时使用它们。
- 知道本课程要优化的项目、四个独立 change 和最终产出。
- 建立“先读懂、再规格、再测试、再优化”的心智。

## 本节产出物

一份统一的开场笔记，建议保存为：

`learn_vibe_adv/notes/tool-comparison.md`

如果你已经在准备好的课程仓库里阅读本节，只需要确认 `learn_vibe_adv/notes/` 存在；如果你是从一个空的学习工作区开始，先按第 0 步创建它。

```markdown
# Tool Comparison

## 我的进阶工作流概念卡

真实代码库：
OpenSpec：
Superpowers：
TDD：
Review：
Archive：

## OpenSpec 与 Superpowers 对照笔记

OpenSpec 负责：
Superpowers 负责：

为什么这门课同时使用它们：
```

## 操作步骤

### 第 0 步：先准备课程目录

如果你的学习工作区里还没有 `learn_vibe_adv/`，先在终端执行：

```bash
mkdir -p learn_vibe_adv/notes
```

这一步只创建课程目录和笔记目录，方便你保存 `notes/tool-comparison.md`。不要在本节 clone 真实项目，不要安装依赖，不要初始化 OpenSpec，也不要创建后面章节才会出现的 `overclocked-helpdesk-lab/`、`openspec/` 或 `docs/superpowers/`。

如果 `learn_vibe_adv/` 已经存在，只确认 `notes/` 能保存笔记即可。

### 第 1 步：先理解进阶课为什么换成真实项目

入门课的任务管理系统适合建立全栈主线：页面、路由、数据库和测试。但真实开发很少从空目录开始。更多时候，你面对的是一个已有项目：目录已经存在，命名已经存在，设计取舍已经存在，问题也已经存在。

进阶课选用 [OverClocked Helpdesk](https://github.com/gagagixi01/OverClocked-Helpdesk)。它是一个 Helpdesk 系统，学生或团队提交问题，导师处理问题，管理员管理导师和查看问题。它比 Todo 更复杂，但仍然是 Python 新人能理解的技术栈：`FastAPI + SQLite + SQLAlchemy + Jinja2`。

这门课的 startup 状态不是“一个已经塞满教学 artifact 的练习包”，而是一个刚刚从 GitHub clone 下来的真实仓库。后面提到的 `openspec/`、`docs/current-capabilities.md`、`docs/superpowers/`、新增测试和 verification 文档，都是你在课程里一步步创建出来的，不是起点自带。

### 第 2 步：记住四个独立 change

本课程默认做四个独立 change，不做一口气“重构全项目”。四个 change 按“先基线，再小修复，再可见优化，再不可见硬化”的顺序推进：

| 位置 | Change | 目标 | 结果分类 |
|---|---|---|
| Change 1 | `quality-baseline` | 给导师分配和问题创建补测试，建立质量基线 | `no visible change` |
| Change 2 | `stabilize-slack-notification-config` | 修复 Slack webhook 未配置时的崩溃风险 | `no visible change` |
| Change 3 | `optimize-dashboard-readability` | 改善管理端和导师端仪表盘信息层级，增加轻量统计和状态可见性 | `visible change` |
| Change 4 | `configure-qr-base-url` | 让 QR 码目标地址使用可配置 base URL，默认页面体验不变 | `no visible change` |

从 `07` 开始，每一轮都要记录网页 before / after evidence。不是每个 change 都会带来可见页面变化，所以你要学会区分三类结果：

| 分类 | 含义 | 例子 |
|---|---|---|
| `visible change` | 页面上能直接看到变化 | dashboard 增加统计和状态 badge |
| `no visible change` | 页面不变，但测试、后台稳定性或配置能力变了 | 质量基线、Slack 缺失配置 bugfix、QR base URL 配置 |
| `artifact-only preparation` | 只准备 OpenSpec / Superpowers artifacts，还没有实现 | 例如进入 Change 4 实现前的 planning 阶段 |

`artifact-only preparation` 是课程阶段分类，不是最终 change 类型。一个 change 准备时可以先标成 `artifact-only preparation`，真正执行后要重新归类为 `visible change` 或 `no visible change`。

按课程阶段理解后面的章节，会更不容易混乱：

- `04` 负责 Change 1 质量基线的 OpenSpec 初始化、能力地图和 proposal。
- `05` 到 `06` 负责 Change 1 质量基线的 design / plan。
- `07` 负责用 TDD + subagent 执行 Change 1，并记录 no visible change evidence。
- `08` 负责第二个 change：Slack 配置 bugfix。
- `09` 准备并执行第三个 change：dashboard 可见优化。
- `10` 准备并执行第四个 change：QR base URL 配置硬化，并记录 no visible evidence 和验证报告。
- `11` 完成四个 change 的 validate / archive 收口，再复盘并整理可迁移工作流。

### 第 3 步：先理解 OpenSpec 是什么

OpenSpec 官方 README、concepts 和 getting-started 文档都在强调同一件事：在 AI 开始写代码之前，先让人和 AI 对“到底要改什么”达成一致。它不是让你多写几份 Markdown，而是给 AI 编程加一层轻量的 **Spec-Driven Development（规格驱动开发）**。

没有 OpenSpec 时，新手很容易这样开始：

```text
“Claude，帮我优化这个项目。”
```

这句话太大了。AI 不知道优化边界在哪里，也不知道哪些行为不能破坏。OpenSpec 要你先把大意图变成一个明确 change：为什么做、做什么、不做什么、怎样验证。

#### OpenSpec 的目录模型

OpenSpec 初始化后，项目里会出现一个 `openspec/` 目录。你先记住两个核心位置：

```text
openspec/
├── specs/                 # source of truth：当前系统应该具备的行为
└── changes/               # proposed changes：还没完成或正在推进的 change
    └── <change-name>/
```

`openspec/specs/` 是长期事实源，描述系统当前应该怎样工作。  
`openspec/changes/<change-name>/` 是某一次变更的工作区，描述这次准备新增、修改或移除什么。

所以 OpenSpec 的关键单位不是“聊天对话”，而是 **change 文件夹**。聊天会丢上下文，change 文件夹会留下可读、可检查、可归档的记录。

#### 一个 change 文件夹里有什么

一个典型 change 会长这样：

```text
openspec/changes/quality-baseline/
├── proposal.md
├── design.md
├── tasks.md
├── .openspec.yaml
└── specs/
    └── assignment/
        └── spec.md
```

每个文件的职责不同：

| 文件 | 主要回答 | 新手理解 |
|---|---|---|
| `proposal.md` | 为什么做、做什么、范围到哪里 | change 的开题说明 |
| `specs/.../spec.md` | 外部行为和验收场景怎么变化 | 需求事实源，不写太多实现细节 |
| `design.md` | 技术上准备怎么做 | 实现思路、架构取舍、风险 |
| `tasks.md` | 按什么顺序实现和验证 | 可勾选的任务清单 |
| `.openspec.yaml` | 这个 change 的元信息 | 工具识别 change 状态时使用 |

一个容易混淆但很重要的点是：`specs/` 更应该写“外部可见行为”和“可验证场景”，不应该把实现细节全塞进去。实现细节应该放在 `design.md` 和 `tasks.md`。

#### Artifact flow：从意图到实现

OpenSpec 的 artifact 不是随便摆在一起的。它们大致形成这条链路：

```text
proposal -> specs -> design -> tasks -> implement -> verify -> archive
   why       what      how      steps      code       check      history
```

- `proposal` 先收住意图和范围。
- `specs` 把这次行为变化写成可验证需求。
- `design` 说明技术路线。
- `tasks` 把实现拆成小步。
- `verify` 对照 spec 和任务检查是否真的完成。
- `archive` 把完成的 change 收进历史，并让长期 spec 更新。

这条链路不是僵硬瀑布。OpenSpec 允许你在理解变深后回头修正前面的 artifact。比如实现时发现一个验收场景漏了，可以回到 `specs/` 补清楚。但原则是：**代码实现必须能追溯回 OpenSpec 的需求和验收场景**。

#### Delta spec：OpenSpec 怎样表达“这次改了什么”

OpenSpec 里的 change spec 通常是 delta spec，也就是“相对当前系统，这次准备怎么变”。它常用三类标题：

```markdown
## ADDED Requirements
## MODIFIED Requirements
## REMOVED Requirements
```

每条 requirement 下面应该有可检查场景，常见写法是：

```markdown
### Requirement: Mentor assignment stays stable

The system MUST keep query creation and mentor assignment behavior working while assignment tests are added.

#### Scenario: query can still be assigned
- GIVEN a query exists and a mentor exists
- WHEN an admin assigns the query to that mentor
- THEN the query records the assigned mentor without breaking existing pages
```

这里的重点不是英文格式，而是思维方式：OpenSpec 要你把“我想优化一下”变成“在什么条件下，做什么动作，应该得到什么结果”。

#### Archive：为什么 change 完成后还要归档

OpenSpec 的 archive 不是把文件扔进垃圾桶。完成一个 change 后，delta spec 会合并或同步到长期 `openspec/specs/`，change 文件夹会进入 archive，留下当时的 proposal、design、tasks 和 spec 变化。

这给真实项目带来两个好处：

- 以后能看到系统当前规格是什么，而不是只翻代码猜行为；
- 以后能回看当初为什么这么改，而不是只看到最后一堆 diff。

#### 人和 AI 在 OpenSpec 里各负责什么

你不要把 OpenSpec 理解成“AI 自动决定需求”。更好的分工是：

| 角色 | 负责什么 |
|---|---|
| 学生 / 开发者 | 提供目标、背景、限制条件，审查范围是否正确 |
| Claude Code | 根据上下文起草 proposal、spec、design、tasks |
| OpenSpec CLI / workflow | 组织 artifact、提供状态检查、帮助 verify 和 archive |
| 测试和人工验证 | 证明代码真的满足 spec |

也就是说，AI 可以帮你写 artifact，但不能替你决定边界是否合理。你仍然要检查：这个 change 有没有变太大？有没有漏掉验收场景？有没有把实现细节写进 spec？

#### 用本课程 Change 1 举例

Change 1 `quality-baseline` 不是要加大功能，而是给导师分配和问题创建建立质量基线。用 OpenSpec 看，它大概会这样拆：

| Artifact | 在 Change 1 里应该表达什么 |
|---|---|
| `proposal.md` | 为什么先补测试：真实项目已有行为需要被保护，后续优化不能靠感觉 |
| `specs/.../spec.md` | query 创建、mentor assignment、相关页面不能回归 |
| `design.md` | 测试策略：哪些 service / route / 页面行为要覆盖，哪些先不碰 |
| `tasks.md` | 先写失败测试，再补 fixture，再跑回归，再记录网页 evidence |

这也解释了为什么 Change 1 是 `no visible change`：OpenSpec 保护的是行为和边界，测试增强本身不一定让页面长得不一样。

#### OpenSpec 不替代测试

OpenSpec 说清楚 **什么应该为真**。  
测试证明 **代码现在真的满足它**。  

#### 学生每次使用 OpenSpec 前的检查清单

后面每一轮 change 开始前，先问自己五个问题：

- 我现在到底在做哪个 change？
- 这个 change 要新增、修改或保护哪个行为？
- 哪些 scenario 能证明它完成？
- 什么明确不在这次范围内？
- 完成后我要怎样 verify、怎样 archive？

你可以先把 OpenSpec 理解成：**让需求、边界、验收场景和 change 历史不要丢在聊天记录里的工作流**。

### 第 4 步：再理解 Superpowers 是什么

根据 [Superpowers plugin](https://claude.com/plugins/superpowers) 页面、[Superpowers GitHub README](https://github.com/obra/superpowers) ，Superpowers 不是一个单一命令，也不是“让 Claude 更聪明”的魔法开关。它更像是给 coding agent 装上一套可组合的 **skills framework**：每个 skill 都是一份明确的工作方法，告诉 agent 在某类任务里应该按什么顺序思考、产出什么 artifact、什么时候停下来验证。

你可以先把 Superpowers 理解成：**把资深工程师的流程纪律写成 agent 可以执行的技能包**。

#### Skill 的技术原则

Claude 本来就“知道”什么是测试、计划和 review。但知道不等于每次都会做，也不等于会按正确顺序做。Superpowers 的价值在于把这些做法变成更明确、更可重复、更难跳过的流程。

一个 skill 通常会包含：

- 什么时候应该使用这个 skill；
- 使用时必须遵守的步骤；
- 需要产出的文件或检查结果；
- 哪些行为是反模式；
- 什么时候可以进入下一个 skill。

所以 Superpowers 的核心不是“提示词更长”，而是：

```text
任务类型 -> 选择合适 skill -> 按 skill 流程推进 -> 产出 artifact -> 验证后进入下一步
```

#### Skill 怎么被使用

在实际使用时，skill 可能通过几种方式进入流程：

| 方式       | 含义                                 | 学生该怎么理解                             |
| -------- | ---------------------------------- | ----------------------------------- |
| 直接调用     | 你明确说“使用 Superpowers brainstorming” | 适合课程练习，最清楚                          |
| 自动触发     | agent 判断当前任务符合某个 skill 的描述         | 省力，但仍要检查结果                          |
| skill 串联 | 一个 skill 完成后要求进入下一个 skill          | 例如 brainstorming 之后进入 writing-plans |

为了让新手不迷路，会尽量使用明确提示：告诉 Claude 当前 OpenSpec change 是什么、要使用哪个 Superpowers skill、产物要保存到哪个目录、完成后要报告真实路径。

#### Superpowers 的基本工作流

Superpowers README 把基本流程描述成一条从设计到执行再到收尾的链路。课程里你可以先记住这个简化版：

```text
brainstorming -> writing-plans -> TDD execution -> review -> verification -> finishing
```

它和 OpenSpec 的关系不是重复，而是接力：

```text
OpenSpec 给出 change 边界
Superpowers 把边界转成设计、计划、TDD、review 和验证纪律
```

#### Review 的三层模型

本课程里 `Review` 不是一句“看起来不错”，而是一个证据检查点：**检查 AI / subagent 的输出是否符合 OpenSpec、真实 plan、`git diff` 和代码质量要求**。

先记住三层：

| Review 层 | 主要问题 | 常见证据 |
|---|---|---|
| `scope/spec review` | 这次改动是否仍在 OpenSpec change 和 plan 范围内？ | OpenSpec artifacts、真实 plan、Claude 总结 |
| `diff/code review` | 实际 diff 是否只改允许文件，代码和测试是否合理？ | `git diff --stat`、`git diff`、focused tests |
| `completion/finishing review` | 所有 scope 是否具备进入 verify / archive 的条件？ | 测试结果、网页 evidence、verification docs、finishing 输出 |

`review` 和 `verify` 不一样。`review` 问的是：**改得对不对、有没有越界、diff 是否可信**。`verify` 问的是：**测试、页面 evidence、verification report 或 `openspec validate` 是否证明它完成了**。所以顺序通常是：先 review 输出，再运行 verify；review 不通过，就不要把测试结果当作完成证明。

#### 这门课会用到哪些 skill

| Skill | 什么时候用 | 主要产出 / 纪律 |
|---|---|---|
| `brainstorming` | OpenSpec change 已存在，准备深化设计时 | 澄清需求、比较方案、保存 design doc |
| `writing-plans` | design doc 已通过检查后 | 生成小步 implementation plan |
| `test-driven-development` | 开始实现 feature 或 bugfix 时 | 先写失败测试，看到 RED，再写最小 GREEN |
| `subagent-driven-development` | 有完整 plan，适合逐任务执行时 | 按任务推进，并穿插 scope/spec review 与 diff/code review |
| `executing-plans` | 不使用子代理或需要批量执行计划时 | 按 plan 分批实现并设置人工 checkpoint |
| `requesting-code-review` | 完成一段实现后 | 对照 plan、diff 和代码质量找问题 |
| `verification-before-completion` | 想说“完成了”之前 | 先运行客观验证，避免只凭 Claude 口头判断 |
| `finishing-a-development-branch` | 所有任务完成后 | 做 completion/finishing review，整理分支或收尾选择 |

这里最重要的是 `test-driven-development`。它强调：

```text
RED   -> 先写失败测试，并确认它因为预期原因失败
GREEN -> 写最小实现，让测试通过
REFACTOR -> 在测试保持通过的前提下清理代码
```

这不是“写完代码以后补测试”。如果没有先看到失败测试，学生就不知道这个测试是不是真的能抓住问题。

#### Superpowers 会产生哪些 artifact

在本课程中，Superpowers 的主要文件会放在：

```text
docs/superpowers/specs/    # brainstorming 生成的设计文档
docs/superpowers/plans/    # writing-plans 生成的执行计划
```

但文件名不能写死。Claude Code 可能生成一个合理但不同于示例的文件名，所以课程统一规则是：

```text
示例文件名只是提示；真实文件路径以 find 输出为准。
```

也就是说，后续每次要读取 Superpowers design doc 或 plan，都要先从磁盘发现实际路径，再把这个真实路径交给 Claude。

同样要注意时间顺序：`docs/superpowers/specs/` 和 `docs/superpowers/plans/` 是后面课程步骤生成出来的目录，不是 startup repo 一开始就能看到的目录。

#### 用 Change 1 举例

Change 1 `quality-baseline` 已经由 OpenSpec 定义了边界：补质量基线，保护 query 创建和 mentor assignment，不趁机加新功能。

Superpowers 在这里不应该重新发明需求，而应该这样接力：

| 阶段 | Superpowers 应该做什么 |
|---|---|
| `brainstorming` | 阅读 OpenSpec artifacts，澄清测试策略，确认不扩大功能范围 |
| design doc | 保存测试设计：覆盖哪些路径、哪些页面只做回归观察、哪些暂不碰 |
| `writing-plans` | 把测试设计拆成可以 TDD 执行的小任务 |
| TDD execution | 每个行为先写失败测试，再实现或补 fixture，再跑回归 |
| review | 对照 OpenSpec、plan 和 `git diff` 检查是否越界、是否只改了应该改的文件 |
| verification | 用测试结果和网页 evidence 证明行为没有回归 |

如果 Claude 在 brainstorming 阶段开始写业务代码，说明流程跑偏了。你要停下来，把它拉回“先设计、再计划、再实现”。

#### 这门课推荐的提示方式

后面你会看到更完整的 prompt。现在先记住几种句式：

```text
请基于这个 OpenSpec change 使用 Superpowers brainstorming：
openspec/changes/<change-name>/

请只做设计深化，不实现代码。
请把 design doc 保存到 docs/superpowers/specs/ 下，并告诉我实际文件路径。
```

```text
请基于这个真实 design doc 路径使用 Superpowers writing-plans：
docs/superpowers/specs/<actual-path>.md

请把 plan 保存到 docs/superpowers/plans/ 下，并告诉我实际文件路径。
```

```text
请按这个真实 plan 路径执行 TDD：
docs/superpowers/plans/<actual-path>.md

每个行为先写失败测试，确认 RED，再写最小实现，最后运行验证命令。
```

这些提示不是为了控制 Claude 的每一句输出，而是为了控制工作流的边界、输入和客观验证。

#### 使用 Superpowers 前的检查清单

每次进入 Superpowers 阶段前，先检查：

- 我有没有把真实 OpenSpec change 路径交给 Claude？
- 我有没有让 Claude 只做当前 skill 该做的事？
- 我有没有用 `find` 发现真实 design doc 或 plan 路径？
- Claude 是否先处理范围和设计，再进入实现？
- 实现是否从失败测试开始，而不是先写代码？
- Claude 说完成之前，我有没有看到测试、review 或 evidence？

你可以先把 Superpowers 理解成：**给 Claude Code 加上一整套工程执行纪律，但最终仍由学生用真实文件和真实验证结果来判断是否完成**。

### 第 5 步：把两者放在一起比较

下面这张表是本节最重要的内容。不要背句子，要看懂边界。

| 维度 | OpenSpec | Superpowers |
|---|---|---|
| 定位 | 规格驱动 change 工作流 | Claude Code 的技能化开发方法 |
| 解决的问题 | 需求容易散落、边界容易失控、历史难追踪 | 实现容易发散、测试容易缺失、review 容易被跳过 |
| 核心产物 | `proposal.md`、`specs/`、`design.md`、`tasks.md`、archive | brainstorming 输出、design doc、implementation plan、测试与 review 结果 |
| 何时使用 | 在写代码前定义 change；完成后归档 | 在规格基础上深化设计、拆计划、TDD 实现、review、收尾 |
| 不负责什么 | 不保证实现质量，不天然强制 TDD | 不天然保存 durable spec 历史，不该替代正式需求事实源 |

如果你只记一句话，可以记这句：

```text
OpenSpec 管 change 的“为什么做、做什么、边界在哪里”；
Superpowers 管 change 的“怎样更稳地做出来”。
```

### 第 6 步：理解为什么不要混淆它们

这门课把两者一起讲清楚，是因为很多新手会产生三个误解。

第一个误解：`OpenSpec 是自动写代码工具`。  
不是。OpenSpec 更像是一个 change 管理框架，重点是 proposal、spec、design、tasks 和 archive，不是直接替你把代码质量做好。

第二个误解：`Superpowers 是另一份 spec`。  
不是。Superpowers 也会产出 design doc 和 plan，但那是执行层面的设计和计划，不应该取代 OpenSpec 里的需求边界。

第三个误解：`两者结合就是让 AI 更快乱写`。  
也不是。恰恰相反，二者结合的价值是：**先锁边界，再强制纪律**。先用 OpenSpec 把 change 收住，再用 Superpowers 把实现过程收住。

### 第 7 步：理解为什么本课程同时使用它们

如果只用 OpenSpec，你会更容易把需求说清楚，但实现阶段仍然可能缺测试、缺 review、缺小步验证。也就是说，“spec 比较稳”，但“代码不一定稳”。

如果只用 Superpowers，你会更容易得到结构化的设计、计划、TDD 和 review，但前面的需求和 change 历史可能仍然散落在聊天里。也就是说，“代码过程比较稳”，但“规格历史不一定稳”。

所以本课程选择的组合是：

```text
OpenSpec 负责规格驱动开发（SDD）
+ Superpowers 负责测试驱动和工程纪律（TDD + Review）
= 更适合真实项目优化的双驱动工作流
```


### 第 8 步：建立这门课的工作流总览

你现在已经有足够上下文来理解本课程主流程了：

```text
学会本地 Git -> 读懂 Remote GitHub 并记录基线 -> OpenSpec propose
-> Superpowers brainstorm -> Superpowers write-plan -> Change 1 质量基线
-> subagent 执行判断 -> Change 2 Slack bugfix -> Change 3 dashboard 准备与执行
-> Change 4 QR 配置准备与执行 -> validate/archive 收口 -> 复盘迁移
```

### 第 9 步：写统一开场笔记

你不需要写成长文，但要把下面两部分都落进 `notes/tool-comparison.md`：

- `我的进阶工作流概念卡`
- `OpenSpec 与 Superpowers 对照笔记`

## 验收标准

除第 0 步创建目录外，不用运行项目命令。检查 `learn_vibe_adv/notes/` 已存在，并确认 `notes/tool-comparison.md` 或等价笔记是否能回答：

- 为什么这门课不从零写项目？
- OpenSpec 的 change 文件夹里通常有什么？
- OpenSpec 和 Superpowers 分别负责什么？
- 你为什么要先写失败测试，而不是写完代码再补测试？
- `Red -> Green -> Refactor` 分别在做什么？
- Superpowers 在这门课里最关键的几个技能是什么？
- 本课程默认做哪四个 change？
- 为什么 `stabilize-slack-notification-config` 是独立 bugfix，而不是混进质量基线？
- visible change、no visible change、artifact-only preparation 分别是什么意思？

通过标准：

- `learn_vibe_adv/notes/` 已经存在，能保存 `tool-comparison.md`。
- 你能说出“OpenSpec 是需求事实源，Superpowers 是执行纪律”。
- 你能用自己的话解释 TDD 为什么强调“先失败测试，再最小实现”。
- 你知道后面每次改代码都必须先有 change。
- 你知道 `06` 写 TDD 计划，`07` 用 TDD + subagent 执行质量基线，`08` 用 bugfix 练习真实 TDD 修复，后面会分别练习可见变化和不可见变化。
- 你的笔记包含“我的进阶工作流概念卡”和“OpenSpec 与 Superpowers 对照笔记”。

需要停下处理：

- 如果你想直接让 Claude “把这个项目优化一下”，先回到本节，把四个 change 和工具分工写清楚。
- 如果你准备在本节 clone `OverClocked-Helpdesk`、安装依赖、启动项目、初始化 OpenSpec 或创建 `openspec/`，先停下；这些都属于后面章节。

## 常见卡点

- 把 OpenSpec 当成普通文档，而不是变更生命周期。
- 把 Superpowers 当成“更会写代码的提示词”，忽略它的 TDD 和 Review 纪律。
- 看到 `design.md` 和 plan，就以为 OpenSpec 与 Superpowers 重复了。
- 把 plugin、skills、slash commands、workflow 混成一个概念。
- 一开始就想加登录、WebSocket、Docker、部署和复杂权限。
- 把第 0 步误解成项目初始化；它只是在学习工作区里创建课程笔记目录。

## 参考来源

- 本地参考：`Clippings/OpenSpec + Superpowers SDD+TDD 双驱动 AI 编程工作流.md`
- [OpenSpec GitHub](https://github.com/Fission-AI/OpenSpec)
- [OpenSpec concepts](https://github.com/Fission-AI/OpenSpec/blob/main/docs/concepts.md)
- [Superpowers plugin](https://claude.com/plugins/superpowers)
- [Claude Code skills docs](https://code.claude.com/docs/en/slash-commands)
