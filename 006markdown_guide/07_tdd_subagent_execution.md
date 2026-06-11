# 07. 用 TDD + subagent 执行 Change 1：质量基线

## 预计用时

90-120 分钟。

## 本节位置

前一节已经生成 Change 1 质量基线 change（推荐名 `quality-baseline`）的可执行计划。本节进入真实执行：先看懂 TDD 执行地图，再判断哪些任务可以委派给 subagent，最后由主会话 review、验证和更新 evidence。

这里的“主会话”指当前 Claude Code 主对话，也就是你一直控制的那条对话。它负责做决策、控制 phase、生成 subagent prompt、检查结果、重跑测试、更新追踪表；它不是新的 terminal tab。

subagent 是执行方式：适合边界清楚、文件不互相踩、验收命令明确的小任务；共享 fixture、共享数据库状态、核心流程判断和最终验证必须留在主会话。

## 工具使用规则

- 终端用于发现真实 plan 路径、查看代码、记录 before / after、运行测试、检查 `git diff`。
- `Claude Code CLI` 用于解释 plan、判断 subagent suitability、执行或起草委派任务、review 结果。
- 本节必须遵守 TDD：没有失败测试，不改生产代码；没有主会话 review，不相信 subagent 已完成。
- 本节不创建新的 OpenSpec change，不开始 Change 2，不做 Slack、dashboard、QR、认证、部署或计划外重构。

## 学习目标

- 能解释 Change 1 质量基线的四段执行地图：测试基础设施、mentor assignment、query creation、统一验证准备。
- 能区分必须串行、可以委派和不适合委派的任务。
- 能写出带 allowed files、forbidden scope、测试命令和 review 要点的 subagent prompt。
- 能让 subagent 也遵守 TDD：失败测试、最小实现、重新运行验证。
- 能在主会话完成 scope/spec review、diff/code review、终端验证和 evidence 更新。

## 本节产出物

- Change 1 TDD 执行地图和 subagent suitability map。
- `learn_vibe_adv/notes/subagent-execution.md`：记录可委派、必须串行、不适合委派的任务。
- Change 1 涉及的测试基础设施、mentor assignment 测试、query creation 测试和验证输出。
- `notes/webpage-change-evidence-quality-baseline.md`：记录页面 before / after，确认没有计划外 UI 回归。
- `docs/verification/YYYY-MM-DD-quality-baseline.md`：记录测试、evidence、OpenSpec 验收标准和阻塞状态。

## 操作步骤

### 第 1 步：确认真实 plan 路径和当前上下文

在终端执行：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
git status --short
find docs/superpowers/plans -type f | sort
find openspec/changes -maxdepth 3 -type f | sort | sed -n '1,160p'
```

确认当前上下文仍然是 `quality-baseline`。如果你已经开始了 Slack bugfix、dashboard 或 QR change，先停下，不要把多个 change 混到本节里。

记录 `quality-baseline` 对应的真实 plan 路径。不要猜文件名。

### 第 2 步：定位 Change 1 相关代码和测试入口

在终端执行：

```bash
find overclocked_helpdesk -maxdepth 3 -type f | sort
sed -n '1,220p' overclocked_helpdesk/services/assigner.py
sed -n '1,220p' overclocked_helpdesk/services/notifier.py
find tests -maxdepth 3 -type f | sort 2>/dev/null || true
```

这一步不是为了立刻改代码，而是为了先把两条业务分支看清：

- `overclocked_helpdesk/services/assigner.py` 对应 mentor assignment 分支
- `overclocked_helpdesk/services/notifier.py` 对应 query creation 分支

如果这时还没有相关测试文件，这是正常的：fresh clone 启动时本来就没有这些测试文件，`quality-baseline` 的目标之一就是把这些测试能力逐步补起来。

### 第 3 步：先记录网页 Before

`quality-baseline` 主要新增测试、fixtures 和行为记录，正常情况下不应该让网页外观发生明显变化。但你仍然要先看页面，后面才能判断“没有变化”是符合预期，还是自己漏看了影响。

在终端执行：

```bash
cd learn_vibe_adv
cp notes/webpage-change-evidence.md notes/webpage-change-evidence-quality-baseline.md
cd overclocked-helpdesk-lab
source .venv/bin/activate
python scripts/seed_data.py
uvicorn overclocked_helpdesk.main:app --reload
```

在浏览器打开并记录 Before 观察：

- `http://127.0.0.1:8000/`
- `http://127.0.0.1:8000/team-status?team=1`
- `http://127.0.0.1:8000/admin`
- `http://127.0.0.1:8000/mentors-dashboard`

把 `本轮预期变化` 写成：

```text
quality-baseline 是质量基线，预期页面无明显变化；证据主要来自新增测试和测试命令通过。
```

### 第 4 步：让 Claude Code 输出 TDD 执行地图

在 `Claude Code CLI` 中输入：

```text
请先阅读我在 06 里记录下来的 quality-baseline plan 真实路径。

不要直接改代码。先帮我做一份 quality-baseline TDD 执行地图：
1. 这一轮一共有哪几类测试任务？
2. 哪些任务属于测试基础设施？
3. 哪些任务属于 mentor assignment？
4. 哪些任务属于 query creation？
5. 当前我应该先从哪一段开始？为什么？
6. 推荐执行顺序是什么？
7. 完成后应该留下哪些测试文件、命令结果和 evidence？
8. 当前计划有没有覆盖 mentor assignment + query creation 两条分支？

请用中文回答，并引用你判断所依据的任务标题或任务编号。
```

如果你忘了 `06` 里记录的路径，先回到终端执行：

```bash
find docs/superpowers/plans -type f | sort
```

然后把真实 plan 路径贴给 Claude。不要直接假设文件名一定叫某个固定名字。

### 第 5 步：判断哪些任务适合 subagent

在 `Claude Code CLI` 中输入：

```text
请阅读当前 quality-baseline 的 OpenSpec artifacts 和真实 Superpowers plan 路径。

输入：
- OpenSpec change: openspec/changes/quality-baseline/
- Superpowers plan: <paste the actual quality-baseline plan path>

先不要实现。请只输出一份 subagent suitability map：
1. 哪些任务可以交给独立 subagent，为什么；
2. 哪些任务必须串行执行，为什么；
3. 哪些任务不适合委派，为什么；
4. 每个可委派任务允许读写哪些文件；
5. 每个可委派任务必须先写哪个失败测试；
6. 每个可委派任务完成后主会话必须运行哪些验证命令；
7. 哪些范围明确禁止：不要做 Slack bugfix、dashboard、QR、认证、WebSocket、部署。

不要修改文件，不要创建新 plan，不要开始 Change 2。
```

判断规则：

- 必须串行：测试基础设施、共享 fixture、共享数据库 session、核心 assignment/query creation 流程判断。
- 可以委派：只触碰独立测试文件或独立 helper，验收命令清楚，失败测试可以明确写出。
- 不适合委派：需求边界还没弄清、会跨多个模块重构、会影响后续 change 范围。

### 第 6 步：把判断和执行策略写进笔记

在 `Claude Code CLI` 中输入：

```text
请根据刚才的 subagent suitability map 创建 ../notes/subagent-execution.md。

要求：
1. 只创建或修改 ../notes/subagent-execution.md；
2. 不修改 overclocked_helpdesk/、templates/、tests/、openspec/ 或 docs/superpowers/；
3. 按“必须串行 / 可以委派 / 不适合委派”三类整理；
4. 每个可以委派的任务都写清 allowed files、forbidden scope、测试命令和 review 要点；
5. 加入一节“执行策略”，说明如何从 suitability map 推导 phased execution order；
6. 明确说明 subagent 不替代 TDD、review、终端验证和网页 evidence。
```

### 第 7 步：让 Claude 生成推荐执行顺序

现在不要直接执行任务。先让 Claude 把真实 plan 和 suitability map 转成可执行的 phase 编排。

在 `Claude Code CLI` 中输入：

```text
请根据真实 quality-baseline plan 和刚才的 subagent suitability map，生成“推荐执行顺序”。

要求：
1. 任务编号、文件名、测试命令必须来自本地真实 plan，不要使用课程示例或凭空编造；
2. 输出使用 phase 结构，而不是普通列表；
3. 每个 phase 都必须写清：owner、task group、expected files、required TDD checks、完成标准、下一步；
4. Phase 结构可以类似：
   - Phase 1 主会话串行：共享 setup、fixtures、DB/session、seed helpers；
   - Phase 2 可并行：一个 subagent 处理独立测试文件，主会话处理不会冲突的准备任务；
   - Phase 3 主会话检查后再委派：先检查前一个 subagent 产物，再决定是否委派下一组独立任务；
   - Phase 4 主会话全量验证：focused tests、full pytest、git diff、evidence；
5. 不要把上面的 phase 示例当成固定任务编号；真实 task 编号、文件名、命令必须来自当前 quality-baseline plan；
6. 明确哪些任务不能并行，尤其是共享 fixture、共享数据库状态、核心 assignment/query creation 流程判断；
7. 不要修改文件，不要执行任务，不要开始 Change 2。
```

你要看懂这份输出，而不是机械复制。重点是学会一种判断方法：先让主会话建立稳定基础设施，再处理互不踩文件的小任务，每个 subagent 回来后都要经过主会话检查，最后由主会话做全量验证。

### 第 8 步：把推荐执行顺序变成执行追踪表

在 `Claude Code CLI` ，把第7步输出的 `推荐执行顺序` 复制到subagent执行计划中：

```text
请把上一步的执行方案以及执行顺序保存到 ../notes/subagent-execution.md
```

先记住一个简单规则：一轮只执行一个 phase。共享 setup、fixture、数据库状态先由主会话执行；只有 phase 明确写了 `subagent`，才采用subagent模式执行。

### 第 9 步：一轮只执行一个 phase

从 `Phase 1` 开始执行，不要跳 phase，也不要同时开多个任务。

在 `Claude Code CLI` 中输入：

```text
请根据 ../notes/subagent-execution.md 中的推荐执行顺序，启动 phase <paste-phase-number>。

要求：
1. 先说明这个 phase 对应真实 plan 中哪些任务编号或标题；
2. 任务编号、文件名、测试命令必须来自真实 plan；
3. 严格遵守 TDD：先写失败测试或最小失败检查，再做最小实现，再重新运行验证；
4. 只修改这个 phase 允许的文件；
5. 不执行后续 phase；
6. 如果这个 phase 需要 subagent，不要直接执行，先告诉我应该进入“生成 subagent prompt”。
```

执行完一个主会话 phase 后，回到终端运行 Claude 指定的命令。命令没有通过，就停在当前 phase，不要进入下一步。

### 第 10 步：如果 phase 需要 subagent，一次只启动一个

只有当前 phase 明确适合 `subagent`，并且前一个 phase 已经通过验证时，才启动 subagent。一次只启动一个 subagent；等它回来、主会话检查完，再决定下一步。

先分清执行位置：

- 主会话：就是当前 `Claude Code CLI` 主对话，负责生成 subagent prompt、等待结果、检查 diff、重跑测试、更新追踪表。
- Subagent：必须在同一个项目 checkout、同一个 repo/worktree 里执行，只处理当前 phase。
- 终端：只负责运行 `git diff`、focused tests 和最终验证；用同一个 terminal tab 或新 terminal tab 都可以。

在 `Claude Code CLI` 中输入：

```text
请根据当前 quality-baseline 的真实 plan 和 ../notes/subagent-execution.md，为当前 phase 生成一个 subagent prompt。

要求：
1. 只覆盖当前 phase，不包含后续 phase；
2. 写清 OpenSpec change 路径、真实 plan 路径、真实任务编号或标题；
3. 明确说明这是 quality-baseline，只补测试，不改核心代码；
4. 从真实 phase plan 和 ../notes/subagent-execution.md 里复制当前 phase 的 allowed files；allowed files 可以有一个或多个文件，但必须是明确文件路径，不要写成 broad folder；
5. quality-baseline 的 allowed writes 通常只应该是测试文件或测试 helper；如果真实 plan 明确允许某个 fixture 文件，也必须把具体路径写清；
6. forbidden scope 必须包含 overclocked_helpdesk/**、templates/**、scripts/**、openspec/**、docs/superpowers/**，以及当前 phase 没列进 allowed files 的其他文件；
7. 要求 subagent 如果无法写入 allowed files，就停止并报告被阻止的文件路径和原因，不要改其他文件；
8. 要求 subagent 返回修改文件、运行命令、结果、失败点和建议的主会话检查命令；
9. 只输出 prompt，不要执行。
```

Claude 只会在这里生成 prompt，不会替你启动 subagent。接下来这样做：

1. 在 `Claude Code CLI` 输入：
```text
启动一个 subagent 执行当前 Phase。

这是 quality-baseline，只补测试，不改核心代码。
请在同一个项目目录中执行。
只允许写当前 Phase 的需要修改的测试文件；如果需要新增其他文件，先停下来问主会话。


如果因为 sandbox 或权限问题不能写这个测试文件：
- 不要改其他文件；
- 不要尝试绕过限制；
- 停下来报告被阻止的文件路径和原因；
- 等主会话调整 prompt 或重新派发。
```

如果sandbox策略拦截subaget执行，可以参考如下方案进行修复：
在Claude Code CLI内输入：
```text
修改~/.claude/settings.json
打开subagent的写权限
```

subagent 返回后，回到刚才启动 subagent 的 `Claude Code CLI` 主对话。这里就是本节说的“主会话”，不是新的 terminal tab，也不是 subagent 窗口。

这里的主会话 review 分三层：

- `scope/spec review`：subagent 做的是否仍然只属于当前 phase、当前 OpenSpec change 和真实 plan。
- `diff/code review`：`git diff` 是否只包含 allowed files，是否没有计划外改动或测试投机。
- `completion review`：focused tests 是否通过，phase 状态是否可以写进追踪表，是否可以进入下一个 phase。

在这个主对话里输入：

```text
subagent 已返回。请先不要进入下一个 phase。
请按三层 Review checkpoint 检查 subagent 结果：
1. scope/spec review：是否符合当前 OpenSpec change、真实 plan 和当前 phase；
2. diff/code review：实际修改是否只在 allowed files 内，是否没有 unrelated changes；
3. completion review：应该运行哪些 focused tests，哪些结果通过后才能标记 phase 完成。

请先告诉我需要看的 diff 和需要运行的 focused tests。
```

然后在终端运行下面的命令；也可以让这个 `Claude Code CLI` 主对话用 `Bash` 帮你运行：

```bash
git diff --stat
git diff
```

然后运行主对话建议的 focused tests。只有主对话看过 diff、focused tests 通过，才把这个 phase 标为完成。

### 第 11 步：每完成一个 phase 就更新状态

每跑完一个 phase，都回到：

```text
../notes/subagent-execution.md
```

把这个 phase 的追踪表补完整：

- `状态`：未开始、进行中、通过、失败或阻塞。
- `实际修改文件`：以 `git diff --stat` 和真实文件为准。
- `运行命令`：写真实执行过的命令，不写“应该运行”。
- `结果`：写 pass / fail / blocked，并贴关键失败原因。
- `下一步`：继续下一个 phase、修复当前 phase，或停止记录阻塞。

如果你不知道怎么手动更新这份追踪表，可以让 Claude Code 主对话先总结，再只更新这个 notes 文件。在 `Claude Code CLI` 中输入：

```text
请根据刚才这个 phase 的执行结果，帮我总结并更新 ../notes/subagent-execution.md 中当前 phase 的状态。

你可以参考：
- subagent 返回的总结；
- git diff --stat；
- git diff；
- 已经运行过的 focused tests 和结果。

要求：
1. 只修改 ../notes/subagent-execution.md；
2. 不修改 overclocked_helpdesk/、templates/、scripts/、tests/、openspec/ 或 docs/superpowers/；
3. 当前 phase 必须写清：状态、实际修改文件、运行命令、结果、下一步；
4. 如果信息不够，先告诉我缺少什么，不要猜。
```

Claude 更新后，重新打开 `../notes/subagent-execution.md` 看一眼，确认它没有把后续 phase 提前标成完成。

重复第 9-11 步，直到所有 phase 都完成。验证失败时，不要进入 Change 2；先回到执行追踪表、`git diff` 和失败输出，判断是 subagent 越界、测试假设错误、共享基础设施不稳，还是当前 quality-baseline 尚未完成。

### 第 12 步：更新 evidence 和执行记录

重新打开第 3 步的四个页面，把执行后的观察写进：

```text
notes/webpage-change-evidence-quality-baseline.md
```

本节的分类通常应该是：

```text
no visible change
```

如果页面真的变了，先问自己：

- 这个页面变化是否写进了 Change 1 OpenSpec change？
- 这个变化是否由当前任务的测试保护？
- 如果没有，它可能是计划外改动，需要停下来检查 `git diff`。

最后在 `../notes/subagent-execution.md` 或 execution summary 中写清：

- 测试基础设施、mentor assignment、query creation、统一验证准备分别完成了什么。
- 哪些任务串行执行，哪些任务委派给 subagent，哪些没有委派。
- 实际运行过的命令。
- 当前状态是完成、失败，还是阻塞。
- 不开始 Change 2。

### 第 13 步：写 Change 1 验证报告

在 `Claude Code CLI` 中输入：

```text
请创建或更新 docs/verification/YYYY-MM-DD-quality-baseline.md。

要求：
1. 记录 quality-baseline 实际运行过的自动化测试命令和结果；
2. 引用 ../notes/subagent-execution.md 和 ../notes/webpage-change-evidence-quality-baseline.md；
3. 标注 mentor assignment、query creation、测试基础设施和 no-visible evidence 对应的 OpenSpec 验收标准是否通过；
4. 如果有失败或阻塞，写清原因、影响范围和下一步；
5. 不开始 Change 2，不修改业务代码。
```

## 验收标准

通过标准：

- 你能说清 `quality-baseline` 至少包含测试基础设施、mentor assignment、query creation 这三类执行分支。
- 你已经记录了 subagent suitability map，并能解释为什么某些任务必须串行。
- 如果实际执行 subagent，主会话已经完成三层 review、看过 diff，并重新运行终端验证。
- 每个编码任务都遵守 TDD：先失败测试，再最小实现，再运行验证。
- 当前工作已经完整推进 infra、mentor assignment 和 query creation，或把阻塞点清楚记录。
- `notes/webpage-change-evidence-quality-baseline.md` 已记录 before / after，并把本轮标为 `no visible change` 或写清计划外变化原因。
- `docs/verification/YYYY-MM-DD-quality-baseline.md` 已记录测试结果、evidence 路径、OpenSpec 验收标准和阻塞状态。
- 没有创建新的 OpenSpec change，没有把 Change 1 和 Change 2 混在一起。

需要停下处理：

- Claude 或 subagent 跳过 TDD，直接改生产代码。
- subagent 修改了 allowed files 之外的文件。
- subagent 修改了 Slack、dashboard、QR、认证、部署或 OpenSpec scope。
- 共享 fixture 或数据库状态还没稳定，就开始并行委派。
- 你说不清 query creation 或统一验证准备是否已经完成。
- 你试图在 Change 1 没收口时直接进入下一轮准备。

## 常见卡点

- 看到 lesson 标题就跳过计划解释，直接写 assignment 测试。
- 没看任务编号，只凭感觉决定“先做哪个测试”。
- 把 subagent 当成“更强 Claude”，让它自由发挥。
- 没有给 subagent 写 allowed files 和 forbidden scope。
- 只看 subagent 总结，不看 diff、不跑测试。
- 还没看失败就写代码。
- 为了让测试过，修改测试预期而不是修实现。
- 没有先看页面，就声称本轮“页面没变化”。
