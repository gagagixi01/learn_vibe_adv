# 06. 用 Superpowers 写可执行 TDD 计划

## 预计用时

35-50 分钟。

## 本节位置

上一节已经完成 Change 1 的 `Superpowers` 设计深化：OpenSpec change 已经在 `04` 里建起来，设计文档也已在 `05` 里生成。

本节只进入 planning 阶段：把 Change 1 和 design doc 转成可执行的 TDD 实施计划。计划必须能交给另一个 agent 或未来的自己逐条执行，不需要再猜文件、命令或测试顺序。

## 工具使用规则

- 终端用于记录 `git rev-parse HEAD` 和查看计划文件。
- `Claude Code CLI` 用于调用 `superpowers:writing-plans`。
- 本节只写计划，不实现代码。

## 学习目标

- 生成一份可执行的 TDD 实施计划。
- 确认每个任务都有测试先行步骤。
- 记录 `base-ref`，方便后续 review diff。

## 本节产出物

- `learn_vibe_adv/overclocked-helpdesk-lab/docs/superpowers/plans/` 下的新 Change 1 实施计划（文件名以 Claude 实际生成结果为准）

## 操作步骤

### 第 1 步：记录 base-ref

在终端执行：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
git rev-parse HEAD
```

复制输出的 commit SHA。后续计划头部要记录这个 `base-ref`。

### 第 2 步：确认写计划前的三个输入值

这一步先准备好三项上下文：

- `change-dir`：当前 OpenSpec change 目录名
- `design-doc`：你在 `05` 里记录下来的 Superpowers design doc 真实路径
- `base-ref`：刚才记录下来的 commit SHA

下面是课程示例值，实际使用时要替换成你当前仓库里的真实值：

- OpenSpec change：`openspec/changes/quality-baseline/`
- Design Doc：`docs/superpowers/specs/<actual-design-doc-path>.md`
- base-ref：`<paste-your-real-git-rev-parse-head-output>`

### 第 3 步：调用 writing-plans

在 `Claude Code CLI` 中输入：

```text
请使用 superpowers:writing-plans，基于以下材料创建 quality-baseline TDD 实施计划：

- OpenSpec change: openspec/changes/<paste change-dir>/
- Design Doc: <paste the actual design-doc path you recorded in 05>
- base-ref: <paste-your-real-git-rev-parse-head-output>

计划要求：
1. 保存到 docs/superpowers/plans/ 下，并在完成后告诉我实际文件路径；
2. 每个任务必须按 TDD 写：先写失败测试，运行并确认失败，再实现最小代码，再运行通过；
3. quality-baseline 只覆盖 mentor assignment 和 query creation 的质量基线；
4. 不加入登录、WebSocket、Docker、analytics、UI 重做；
5. 每个任务写清文件、命令和预期输出；凡是 python -m pip、python -m pytest、uvicorn 这类命令，都默认先激活 .venv；
6. 如果某一步只记录当前行为，不改变实现，要明确写出。
```

### 第 4 步：审查计划是否可执行

在 `Claude Code CLI` 中输入：

```text
请自审刚写的计划：
1. 有没有 TBD、TODO、适当测试、类似上一任务 这类占位话；
2. 有没有生产代码早于失败测试；
3. 有没有超出 quality-baseline 质量基线边界的任务；
4. 每个 pytest 命令是否具体；
5. 如发现问题，请只修改计划文件。
```

### 第 5 步：确认计划路径

在终端执行，先发现真实 plan 路径，再打开它：

```bash
find docs/superpowers/plans -type f | sort
cat <paste-the-actual-plan-path-you-recorded-from-step-3>
```

## 验收标准

运行或检查计划文件。它至少必须包含：

- change 名称。
- design doc 路径。
- `base-ref`。
- TDD 步骤。
- 具体测试命令。
- 明确不做事项。
- 真实 plan 路径，而不是猜测出来的示例文件名。

通过标准：

- 计划任务可以逐条执行，不需要额外猜测。
- 你已经记录并复用了真实的 design doc 路径和真实的 plan 路径。
- 每个编码任务都先写测试，再做最小实现。
- 计划仍然只服务 Change 1 OpenSpec change。
- 本节结束时仍然没有开始执行任务。

需要停下处理：

- 如果计划只写“添加测试并修复代码”，要求 Claude 重写成红绿重构步骤。
- 如果你已经开始跑第一条任务，那说明你把 `06` 和 `07` 混在一起了。

## 常见卡点

- 计划太像任务清单，没有具体命令。
- 没有先发现真实路径，就直接假设 Claude 会生成课程示例文件名。
- 写了实现步骤，却没有先运行失败测试。
- Change 1 计划混入 UI 和 analytics。
- 忘记把 `base-ref` 和真实文件路径写进计划。
