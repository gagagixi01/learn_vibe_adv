# 08. 执行 Change 2：Slack 配置 Bugfix

## 预计用时

50-75 分钟。

## 本节位置

完成 `07` 的 Change 1 质量基线执行，并练习过 TDD + subagent 的委派判断后，你已经知道如何用测试记录当前行为，也知道哪些执行任务可以委派、哪些必须由主线程串行控制。本节是 Change 2：一个很小但真实的 bugfix change `stabilize-slack-notification-config`。

这节它只修一个通知边界：当 `SLACK_WEBHOOK_URL` 没有配置时，`send_slack_alert()` 不应该调用 `requests.post(None, ...)` 并抛异常。它应该跳过 Slack 通知并返回 `False`，同时不影响 Query 创建。

## 工具使用规则

- 终端用于查看当前代码、运行聚焦测试、检查 `git diff`。
- `Claude Code CLI` 用于创建 OpenSpec change、触发 Superpowers brainstorming、生成 plan、按 TDD 修复。
- 本节必须先有 OpenSpec change，再有 Superpowers design 和 plan，最后才进入实现。
- 本节只修 Slack webhook 缺失配置，不顺手重写通知系统、不接入真实 Slack、不加入重试队列。

## 学习目标

- 把 baseline 中发现的风险升级成一个独立 bugfix change。
- 理解 bugfix 也需要 OpenSpec：写清“坏行为是什么、目标行为是什么、不做什么”。
- 用 Superpowers 把 bugfix 拆成失败测试、最小实现、验证。
- 看懂“没有明显页面变化，但后台稳定性变好了”的 change 证据。

## 本节产出物

- `learn_vibe_adv/overclocked-helpdesk-lab/openspec/changes/stabilize-slack-notification-config/`：本节创建
- `docs/superpowers/specs/` 下的 Slack bugfix design doc（以真实生成路径为准，本节创建）
- `docs/superpowers/plans/` 下的 Slack bugfix plan（以真实生成路径为准，本节创建）
- 完整 Slack bugfix 修复与回归验证
- `tests/test_slack_service.py`：本节新增
- 更新后的 `overclocked_helpdesk/services/slack_service.py`
- 通过的 Slack + query creation 回归测试
- `notes/webpage-change-evidence-stabilize-slack-notification-config.md`
- `docs/verification/YYYY-MM-DD-stabilize-slack-notification-config.md`

## 操作步骤

### 第 1 步：确认 bug 不是凭空想象

在终端执行：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
sed -n '1,180p' overclocked_helpdesk/services/slack_service.py
sed -n '1,180p' overclocked_helpdesk/services/notifier.py
rg -n "SLACK_WEBHOOK_URL|send_slack_alert|requests.post" overclocked_helpdesk tests docs
```

你应该能看到：

- `settings.SLACK_WEBHOOK_URL` 默认可能是 `None`。
- `send_slack_alert()` 直接把这个值传给 `requests.post()`。
- Change 1 query creation 测试 mock 掉了 `send_slack_alert()`，所以没有直接修这个风险。

本步只确认问题，不修改代码。

### 第 2 步：创建 OpenSpec bugfix change

在 `Claude Code CLI` 中输入：

```text
/opsx:propose "稳定 OverClocked Helpdesk 的 Slack 通知配置边界。

推荐 OpenSpec change 名称：stabilize-slack-notification-config。

背景：
已有 `quality-baseline` tests 记录 query creation 会调用 send_slack_alert。但当前 startup repo 自带的 `slack_service.py` 在 `SLACK_WEBHOOK_URL` 未配置时仍会 `requests.post(None, ...)`，可能抛异常。

目标：
1. SLACK_WEBHOOK_URL 缺失或为空字符串时，send_slack_alert 返回 False；
2. 缺失 Slack 配置不应该阻止 Query 创建；
3. 有 webhook URL 时仍按原 payload 调用 requests.post；
4. 不接入真实 Slack，不加入重试队列，不重写 notifier 架构。

验收：
- 有单元测试证明缺失 webhook 时不会调用 requests.post；
- 有测试证明 send_slack_alert 返回 False；
- 现有 query creation 测试仍然通过；
- 如果 webhook URL 存在，原有 payload 形状保持不变。"
```

本课程默认后续使用 `stabilize-slack-notification-config`。如果 Claude/OpenSpec 生成了不同目录名，先运行 `find openspec/changes -maxdepth 2 -type d | sort`，后面所有命令和 prompt 都以真实名字为准。

### 第 3 步：检查 OpenSpec artifacts

下面命令使用课程默认名；如果第 2 步生成的名字不同，把 `stabilize-slack-notification-config` 替换为真实名字。

在终端执行：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
openspec status --change stabilize-slack-notification-config --json
find openspec/changes/stabilize-slack-notification-config -maxdepth 4 -type f | sort
```

### 第 4 步：用 Superpowers 深化 bugfix 设计

在 `Claude Code CLI` 中输入：

```text
请基于 stabilize-slack-notification-config 的 OpenSpec artifacts 使用 Superpowers brainstorming。

要求：
1. OpenSpec 是需求事实源；
2. 只深化 Slack 缺失配置的测试策略和完整 bugfix 路径；
3. 把互动中形成的技术决策写进 design doc，包括测试策略、边界条件、mock target、缺失或空 webhook 的预期行为、明确不做什么；
4. 输出 design doc 到 docs/superpowers/specs/，并在完成后告诉我实际文件路径；
5. 不把这个 bugfix 扩大成完整通知系统；
6. 不修改业务代码。
```

### 第 5 步：发现并记录 Slack design doc 真实路径

Claude Code 生成的文件名可能和课程示例不同。先用文件系统确认真实路径：

```bash
find docs/superpowers/specs -type f | sort
```

记录 Slack bugfix design doc 的真实路径。后面的 writing-plans 提示词要直接复用这条路径，不要猜文件名。

### 第 6 步：生成 TDD 实施计划

先在终端执行：

```bash
git rev-parse HEAD
```

然后在 `Claude Code CLI` 中输入：

```text
请使用 Superpowers writing-plans，为 stabilize-slack-notification-config 创建实施计划。

输入：
- OpenSpec change: openspec/changes/stabilize-slack-notification-config/
- Design Doc: <paste the actual Slack design doc path you recorded in Step 5>
- base-ref: <paste the real git rev-parse HEAD output>

要求：
1. 先读取 OpenSpec artifacts 和上面的真实 Design Doc；
2. 以 OpenSpec 作为需求边界，以 Design Doc 作为技术决策来源；
3. plan 必须把 Design Doc 中的技术决策转成 TDD tasks；
4. 如果 Design Doc 与 OpenSpec 冲突，停止并指出冲突，不要自行决定；
5. plan 放到 docs/superpowers/plans/，并在完成后告诉我实际文件路径；
6. 第一项实现任务必须先写失败测试或回归测试；
7. 不把这个 bugfix 扩大成完整通知系统；
8. 除非 Design Doc 明确写入且 OpenSpec 允许，否则不要接入真实 Slack，不要加入重试机制。
```

然后回到终端执行：

```bash
find docs/superpowers/plans -type f | sort
```

记录 Slack bugfix plan 的真实路径。后面的执行不要猜文件名，要直接复用这条真实路径。

### 第 7 步：先记录页面 Before

本节修的是后台稳定性，正常情况下页面外观不应该变化。实现前先记录页面 Before，后面才能证明“没有可见变化”是符合预期，而不是漏看影响。

在终端执行：

```bash
cd learn_vibe_adv
cp notes/webpage-change-evidence.md notes/webpage-change-evidence-stabilize-slack-notification-config.md
```

然后在另一个终端执行：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
source .venv/bin/activate
uvicorn overclocked_helpdesk.main:app --reload
```

保持这个终端窗口运行，再到浏览器记录 Before：

- `http://127.0.0.1:8000/`
- `http://127.0.0.1:8000/team-status?team=1`
- `http://127.0.0.1:8000/mentors-dashboard`
- `http://127.0.0.1:8000/admin`

把 `本轮预期变化` 写成：

```text
本 change 修复 Slack webhook 缺失配置时的后台稳定性，预期页面无明显变化；证据主要来自单元测试和 query creation 回归测试。
```

### 第 8 步：按真实 plan 完成整个 bugfix change

在 `Claude Code CLI` 中输入：

```text
请读取我刚记录的 Slack bugfix plan 真实路径，并按这份 plan 完成整个 stabilize-slack-notification-config bugfix scope，直到它完成或出现明确阻塞。

要求：
1. 先完整阅读这份真实 plan，再开始执行；
2. 严格按 plan 顺序完成所有剩余任务，不要只做第一个红绿循环就停；
3. 每完成一个有意义的阶段，说明刚完成了什么、还剩什么；
4. 只有当整个 bugfix scope 完成，或出现明确阻塞时，才停止；
5. 不修改计划外文件。
```

本 change 的实现与验证通常只应该落在：

- `tests/test_slack_service.py`
- `overclocked_helpdesk/services/slack_service.py`
- `tests/test_query_creation.py`（只运行回归，不应因本 change 被改坏）

完成标准是下面这些可验证结果：

- 缺失 webhook 时，`send_slack_alert` 返回 `False`
- URL 缺失或空字符串时，不会调用 HTTP
- webhook URL 存在时，仍会调用 `requests.post`
- query creation 回归测试仍然通过

### 第 9 步：做 Review checkpoint

在 `Claude Code CLI` 中输入：

```text
请对 stabilize-slack-notification-config 做三层 Review checkpoint。

要求：
1. scope/spec review：确认实现仍然只修 Slack webhook 缺失配置，不接入真实 Slack、不加重试队列、不改 notifier 架构；
2. diff/code review：检查 git diff 是否只包含 plan 允许的测试文件和 slack_service.py，测试是否直接覆盖 send_slack_alert() 的缺失配置分支；
3. completion review：列出还需要运行的测试命令、网页 evidence 和 verification report 项目；
4. 如果 review 发现越界、缺测试或 diff 不可信，先指出问题，不要说本 change 完成。
```

Review 记录至少要能说明：看过哪些 diff、哪些测试证明缺失 webhook 不会调用 HTTP、query creation 回归是否仍然需要独立验证。

### 第 10 步：记录当前完成度

在 `Claude Code CLI` 中输入：

```text
请总结刚才 stabilize-slack-notification-config 的执行进度。

要求：
1. 说明刚完成了哪些具体结果；
2. 说明 plan 中还有哪些未完成项；
3. 只有当整个 bugfix scope 已满足 plan 和测试要求时，才说它已经完成；
4. 如果阻塞，写清阻塞点和下一步。
```

### 第 11 步：用终端独立验证完成状态

不要只依赖 Claude 的总结。回到终端执行：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
source .venv/bin/activate
python -m pytest tests/test_slack_service.py -q
python -m pytest tests/test_query_creation.py -q
python -m pytest tests/test_slack_service.py tests/test_query_creation.py -q
```

如果这些测试没有按预期通过，就不要把本节记成完成。先回到 plan、diff 和失败输出，继续修到通过，或者明确记录阻塞。

### 第 12 步：记录页面 After

测试通过后，回到浏览器重新检查第 7 步的四个页面，把 After 观察写进同一份 evidence 表。本节大概率没有明显页面变化；你仍然要记录，因为这能训练你区分三类 change：

- visible change：用户能在页面上直接看到变化
- no visible change：页面不变，但行为或稳定性变了
- artifact-only preparation：只产生 OpenSpec / Superpowers artifacts，还没实现；这只用于准备阶段，不是本节完成后的最终分类

如果第 7 步的服务已经停止，先在终端重新启动：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
source .venv/bin/activate
uvicorn overclocked_helpdesk.main:app --reload
```

保持这个终端窗口运行，再到浏览器检查以下页面。

在浏览器检查：

- `http://127.0.0.1:8000/`
- `http://127.0.0.1:8000/team-status?team=1`
- `http://127.0.0.1:8000/mentors-dashboard`
- `http://127.0.0.1:8000/admin`

把分类写成 `no visible change`，并在结论里说明：

```text
本 change 修复的是 Slack 配置缺失时的后台稳定性。页面外观不应该变化；证据主要来自单元测试和 query creation 测试仍然通过。
```

### 第 13 步：写 Change 2 验证报告

在 `Claude Code CLI` 中输入：

```text
请创建或更新 docs/verification/YYYY-MM-DD-stabilize-slack-notification-config.md。

要求：
1. 记录 Slack 单元测试和 query creation 回归测试的真实命令与结果；
2. 记录 Review checkpoint 结论：scope/spec review、diff/code review、completion review 是否通过；
3. 记录 no-visible 页面验证步骤和结果；
4. 标注每条 OpenSpec 验收标准是否通过；
5. 引用 ../notes/webpage-change-evidence-stabilize-slack-notification-config.md；
6. 如果失败或阻塞，写清原因、影响范围和下一步；
7. 不修改业务代码，不开始 optimize-dashboard-readability。
```

## 验收标准

在终端运行或检查：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
find openspec/changes/stabilize-slack-notification-config -maxdepth 4 -type f | sort
find docs/superpowers/specs -type f | sort
find docs/superpowers/plans -type f | sort
find docs/verification -type f | sort
source .venv/bin/activate
python -m pytest tests/test_slack_service.py tests/test_query_creation.py -q
cd ..
cat notes/webpage-change-evidence-stabilize-slack-notification-config.md
```

通过标准：

- OpenSpec change 已创建，且没有扩大成完整通知系统。
- Superpowers design doc 和 plan 已在本节生成。
- 你已经记录了真实的 plan 路径，并基于这条路径驱动实现。
- 缺失 webhook 的失败测试先出现，再由完整实现路径变绿。
- `tests/test_slack_service.py` 全量通过。
- `requests.post` 在 webhook 缺失时没有被调用。
- 现有 query creation 测试仍然通过。
- `tests/test_query_creation.py` 仍然通过。
- 你没有把 Claude 的一句“已完成”直接当成事实，而是用真实 plan 和测试结果做了核对。
- 页面证据表说明这是 `no visible change` 的稳定性修复。
- `docs/verification/YYYY-MM-DD-stabilize-slack-notification-config.md` 已记录测试、no-visible evidence、OpenSpec 验收标准和阻塞状态。

需要停下处理：

- 如果 Claude 想接入真实 Slack、读取真实 secret、加入重试队列，先停下，回到 OpenSpec 非目标。
- 如果测试直接 patch 掉整个 `send_slack_alert()`，那就没有测到 bug，要求重写测试。
- 如果为了让测试过而删除 Slack 调用，也要停下，因为有 webhook URL 时的原行为必须保留。
- 如果 Claude 没有逐条说 “Task 1 到 Task 5”，不要立刻判定它跑偏；先看真实 plan 是否已完成、测试是否通过。
- 如果只完成了第一个红绿循环就准备进入下一课，先停下，把剩余 plan 做完或明确记录阻塞。

## 常见卡点

- 以为 bugfix 很小，所以可以跳过 OpenSpec。
- 只测试 `notify_and_create_query()`，却没有直接测 `slack_service.py` 的缺失配置分支。
- 把“页面没变化”误判成“本节没学到东西”。
- 修 bug 时顺手设计完整通知平台。
