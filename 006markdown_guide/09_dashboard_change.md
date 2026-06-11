# 09. 准备并执行 Change 3：dashboard 可见优化

## 预计用时

60-90 分钟。

## 本节位置

前面应该已经完成 Change 1 `quality-baseline` 和 Change 2 `stabilize-slack-notification-config` 的执行；如果有阻塞，也必须已经清楚记录，并且不影响本节的启动、测试、页面观察和 artifact discovery。
本节开始准备第一个明确的可见变化：Change 3 `optimize-dashboard-readability`。这里
的 `openspec/changes/...` 和 `docs/superpowers/...` 都是你在前几节已经逐步生成出来的路径，不是 startup repo 自带目录。

本节把 Change 3 一次走完：先准备，再实现，再验证。目标是把 dashboard 优化从一句“让页面更清楚”变成可执行的 OpenSpec change、Superpowers design doc、Superpowers plan，以及最终能被网页 before / after 证据证明的 `visible change`。

注意：本章不是“边做边想 UI”的自由发挥，而是更严格的 visible change 练习。只有 OpenSpec artifacts 完整、Superpowers plan 真实路径已确认、网页 evidence 检查清单已锁定后，才进入实现。

## 工具使用规则

- 终端用于检查 `git` 状态、查看 `openspec/` 和 `docs/superpowers/` 路径、运行测试、启动服务、记录 before / after。
- `Claude Code CLI` 用于执行 `/opsx:propose`、补齐 OpenSpec artifacts、触发 Superpowers brainstorming、生成 writing plan，并按真实 plan 执行 Change 3。
- 本节先做准备，再做实现；不要在还没补齐 artifacts 和真实 plan 路径前直接改代码。

## 学习目标

- 为一个可见 UI / dashboard 优化创建独立 OpenSpec change。
- 学会用规格限制 UI 改动范围，避免变成“大改版”。
- 把可见变化拆成可验证的统计数据、状态显示和手动 before / after evidence。
- 在实现前先锁定需要观察的页面、预期可见变化和对应验收标准。
- 按真实 plan 完成 Change 3 的完整实现和验证。
- 用网页 evidence 和自动化测试一起证明 Change 3 确实 worked。

## 本节产出物

- `learn_vibe_adv/overclocked-helpdesk-lab/openspec/changes/optimize-dashboard-readability/`：本节创建
- Change 3 的 OpenSpec `proposal.md`、`design.md`、`specs/.../spec.md`、`tasks.md`
- `docs/superpowers/specs/` 下的 Change 3 design doc，真实路径以 `find` 输出为准，本节创建
- `docs/superpowers/plans/` 下的 Change 3 plan，真实路径以 `find` 输出为准，本节创建
- 一份 Change 3 的网页 evidence 检查清单：至少明确 `/admin`、`/mentors-dashboard`、预期可见变化和验收标准映射
- `notes/webpage-change-evidence-optimize-dashboard-readability.md`
- Change 3 自动化测试输出和手动网页验证记录
- `docs/verification/YYYY-MM-DD-optimize-dashboard-readability.md`

## 操作步骤

### 第 1 步：确认 Change 1 和 Change 2 状态

在终端执行：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
git status --short
find openspec/changes -maxdepth 4 -type f | sort | sed -n '1,200p'
find docs/superpowers -maxdepth 4 -type f | sort
```

进入 Change 3 前，你至少要能回答：

- Change 1 是否已经完成执行，或者阻塞是否被清楚记录？
- Change 2 是否已经完成执行，或者阻塞是否被清楚记录？
- 当前工作区是否有你解释不清的代码改动？
- 现在要准备的是 `optimize-dashboard-readability`，不是继续扩大前两个 change。

本课程里的 `quality-baseline`、`stabilize-slack-notification-config` 和 `optimize-dashboard-readability` 是默认名称。如果你前面实际生成的 change 名称、OpenSpec 路径或 Superpowers 文件路径不同，以本地 `find ... | sort` 输出为准，并把真实路径贴进后续提示词，不要把课程示例路径当成固定事实。

这一步也是进入 Change 3 的硬 gate。前两轮如果只是“阻塞已记录”，但阻塞会影响 `.venv`、应用启动、seed data、数据库状态、`/admin`、`/mentors-dashboard`、测试运行或 artifact discovery，就不要进入第 2 步。先解决阻塞，或把为什么不能继续 Change 3 记录清楚。

### 第 2 步：创建 Change 3 OpenSpec proposal

在 `Claude Code CLI` 中输入：

```text
/opsx:propose "优化 OverClocked Helpdesk 的管理端和导师端仪表盘可读性。

Change 名称建议：optimize-dashboard-readability。

背景：
前两轮已经建立质量基线，并修复 Slack 缺失配置的后台稳定性风险。现在要练习一个真正能在页面上看到变化的 change。

目标：
1. 管理端能看到 open query count、resolved query count、mentor workload；
2. 导师端能更清楚地区分待处理、已分配、已解决的问题；
3. 保持现有 Jinja2 模板和 CSS 方式；
4. 不引入 React、图表库、WebSocket、完整 analytics；
5. 不顺手修改 QR、Slack、认证或部署。

验收：
- 管理端页面能显示至少 3 个轻量统计；
- 导师端列表能看出每个问题状态；
- 有测试或手动验证清单证明统计来自真实查询数据；
- before / after evidence 必须覆盖 `/admin` 和 `/mentors-dashboard`，并能说明这是 visible change。"
```

### 第 3 步：检查 OpenSpec artifacts

如果第 1 步发现 Change 3 的真实名称不是 `optimize-dashboard-readability`，下面命令和提示词里的 change 名称都要替换成你本地真实名称。

在终端执行：

```bash
openspec status --change optimize-dashboard-readability --json
openspec status --change optimize-dashboard-readability
find openspec/changes/optimize-dashboard-readability -maxdepth 4 -type f | sort
```

确认这些文件存在，或按 OpenSpec status 提示补齐：

- `openspec/changes/optimize-dashboard-readability/proposal.md`
- `openspec/changes/optimize-dashboard-readability/design.md`
- `openspec/changes/optimize-dashboard-readability/specs/.../spec.md`
- `openspec/changes/optimize-dashboard-readability/tasks.md`

如果缺失 artifact，按缺失类型处理，不要直接进入 Superpowers 或实现：

| 缺失情况 | 恢复方式 |
|---|---|
| `openspec/changes/optimize-dashboard-readability/` 整个目录不存在 | 回到第 2 步，重新运行 `/opsx:propose`，并确认 change 名称。 |
| `proposal.md`、`design.md` 或 `specs/.../spec.md` 缺失 | 在 `Claude Code CLI` 中要求只修复当前 OpenSpec artifacts；先运行 `openspec status --change optimize-dashboard-readability --json`，按 OpenSpec 提示补缺失文件，不开始 Superpowers，不修改业务代码。 |
| `tasks.md` 缺失，或 status 显示 `tasks` 未完成 | 使用下面的 `tasks.md` 补齐提示词。 |
| 修复后 status 仍然 invalid | 停下，把完整 `openspec status` 输出贴给 Claude Code 诊断；不要生成 plan，不要开始实现。 |

如果只缺 `tasks.md`，在 `Claude Code CLI` 中输入：

```text
请只补齐 optimize-dashboard-readability 的 OpenSpec tasks.md。

输入：
- OpenSpec change: openspec/changes/optimize-dashboard-readability/

要求：
1. 先读取 proposal.md、design.md 和 specs/ 下所有 spec.md；
2. 只创建或修改 openspec/changes/optimize-dashboard-readability/tasks.md；
3. tasks 必须覆盖 admin 轻量统计、mentor 状态可见性、自动化测试、网页 evidence 和验证命令；
4. 不生成 Superpowers design doc；
5. 不生成 implementation plan；
6. 不修改 overclocked_helpdesk/、templates/、tests/ 或 docs/superpowers/。
```

### 第 4 步：用 Superpowers 深化 dashboard 设计

在 `Claude Code CLI` 中输入：

```text
请基于 optimize-dashboard-readability 的 OpenSpec artifacts 使用 Superpowers brainstorming。

要求：
1. OpenSpec 是需求事实源；
2. 只深化 dashboard 信息层级、统计字段、数据来源、模板插入位置和测试策略；
3. 输出 design doc 到 docs/superpowers/specs/，并在完成后告诉我实际文件路径；
4. 如发现验收场景缺失，只回写 OpenSpec spec；
5. 不做视觉大改版，不引入前端框架，不改业务代码。
```

回到终端执行：

```bash
find docs/superpowers/specs -type f | sort
```

记录 dashboard design doc 的真实路径。

### 第 5 步：生成 Change 3 实施计划

先在终端记录当前 `base-ref`：

```bash
git rev-parse HEAD
```

然后在 `Claude Code CLI` 中输入：

```text
请使用 Superpowers writing-plans，为 optimize-dashboard-readability 创建实施计划。

输入：
- OpenSpec change: openspec/changes/optimize-dashboard-readability/
- Design Doc: <paste the actual dashboard design doc path from Step 4>
- base-ref: <paste the real git rev-parse HEAD output>

要求：
1. plan 放到 docs/superpowers/plans/，并在完成后告诉我实际文件路径；
2. 先为统计数据来源和 HTML 输出写失败测试；
3. 再修改路由传入模板上下文；
4. 最后小范围修改 Jinja 模板；
5. 每一步都有验证命令或手动检查；凡是 python -m pytest 或 uvicorn，都默认先激活 .venv；
6. 不引入图表库，不引入前端框架；
7. 不修改 QR、Slack、认证或部署。
```

回到终端执行：

```bash
find docs/superpowers/plans -type f | sort
```

记录 dashboard plan 的真实路径。后面的执行阶段必须复用这条真实路径，不要猜文件名。

### 第 6 步：先锁定网页 evidence 检查清单

因为 Change 3 是 `visible change`，所以在进入实现前，你要先把“看什么页面、期待看到什么变化、每个变化对应哪个验收标准”写清楚。这样后面的执行阶段不是凭感觉说“页面好像变了”，而是按清单验证它真的 worked。

在 `Claude Code CLI` 中输入：

```text
请根据 optimize-dashboard-readability 的 OpenSpec artifacts、design doc 和 plan，整理一份“optimize-dashboard-readability 网页 evidence 检查清单”。

要求：
1. 不修改业务代码；
2. 只输出检查清单，不开始实现；
3. 至少列出 `/admin` 和 `/mentors-dashboard` 两个页面；
4. 每个页面写清 Before 应看什么、After 应看什么；
5. 每个可见变化都要对应 OpenSpec 验收标准；
6. 明确哪些结果靠自动化测试证明，哪些结果必须靠网页观察证明。
```

把这份清单落到最终 evidence 文件里，不再创建单独的 prep 草稿文件，避免后面记录分叉。先在终端复制模板：

```bash
cd learn_vibe_adv
cp notes/webpage-change-evidence.md notes/webpage-change-evidence-optimize-dashboard-readability.md
```

然后在同一份文件里填写 Change 信息，并在“截图或备注”下先增加一段“预实现检查清单”，写入刚才整理出来的页面、预期变化和验收标准映射。如果分类栏暂时需要占位，可以先标成：

```text
artifact-only preparation
```

这一步不是提前记录 Before / After，而是先把后面必须观察的 visible signals 固定下来。等 Change 3 执行完成后，必须在这同一份文件里补 Before / After，并把最终分类更新为 `visible change`。

### 第 7 步：确认真实 plan 路径并记录 dashboard Before

在终端执行：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
git status --short
find docs/superpowers/plans -type f | sort
find docs/superpowers/specs -type f | sort
```

找到并记录 `optimize-dashboard-readability` 的真实 design doc 路径和 plan 路径。

然后在终端执行：

```bash
cd learn_vibe_adv
test -f notes/webpage-change-evidence-optimize-dashboard-readability.md
cd overclocked-helpdesk-lab
source .venv/bin/activate
python scripts/seed_data.py
uvicorn overclocked_helpdesk.main:app --reload
```

在浏览器记录 Before 状态，并截图保存：

- `http://127.0.0.1:8000/admin`
- `http://127.0.0.1:8000/mentors-dashboard`

把 `本轮预期变化` 写成：

```text
optimize-dashboard-readability 是 visible change。管理端应出现轻量统计，导师端应更清楚地区分 query 状态；这些变化必须对应 optimize-dashboard-readability 的 OpenSpec 验收标准。
```

### 第 8 步：按 plan 执行 Change 3

在 `Claude Code CLI` 中输入：

```text
请阅读 optimize-dashboard-readability 的 OpenSpec artifacts 和我刚记录的 Superpowers plan 真实路径。

输入：
- OpenSpec change: openspec/changes/optimize-dashboard-readability/
- Superpowers plan: <paste the actual dashboard plan path>

先不要实现。请先回答：
1. 本 change 的 visible outcome 是什么；
2. 哪些文件预计会被修改；
3. 哪些任务必须先写失败测试；
4. 哪些任务适合独立子代理，哪些必须串行；
5. 哪些范围明确不做。
```

然后继续在 `Claude Code CLI` 中输入：

```text
请按真实 plan 执行 optimize-dashboard-readability，直到整个 change 完成或出现明确阻塞。

要求：
1. 只执行 optimize-dashboard-readability；
2. 每个实现任务都先写失败测试；
3. 先改测试，再改路由上下文，最后改 Jinja 模板；
4. 每个任务完成后都做三层 Review checkpoint：
   - scope/spec review：是否符合 optimize-dashboard-readability 的 OpenSpec 验收标准和真实 plan；
   - diff/code review：`git diff` 是否只改 dashboard 相关测试、路由上下文和模板，统计是否来自真实查询数据；
   - completion review：测试、网页 evidence 和验证报告还缺什么；
5. review 不通过就修复并重新验证；
6. 每一步都贴出运行命令、结果和 review 结论；
7. 不执行计划外功能。
```

如果 Claude 建议使用 subagent，只能把互不踩文件的任务交给子代理。共享 dashboard 路由、模板或测试的任务必须串行。

### 第 9 步：终端独立验证

回到终端执行：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
source .venv/bin/activate
python -m pytest tests/ -q
git status --short
```

如果失败，不要直接进入 After evidence。先回到失败输出和 plan，判断是环境问题、测试问题、当前 change 实现不符合 spec，还是破坏旧行为。

### 第 10 步：记录 dashboard After

如果服务已经停止，重新启动：

```bash
source .venv/bin/activate
uvicorn overclocked_helpdesk.main:app --reload
```

重新打开，对比 change 执行前的截图：

- `http://127.0.0.1:8000/admin`
- `http://127.0.0.1:8000/mentors-dashboard`

更新：

```text
../notes/webpage-change-evidence-optimize-dashboard-readability.md
```

After 观察至少写清：

- 管理端出现了哪些统计；
- 导师端状态显示如何变清楚；
- 每个观察对应哪个 OpenSpec 验收标准；
- 分类写 `visible change`，如果第 6 步留下了 `artifact-only preparation` 占位，要在这里替换掉。

### 第 11 步：写 Change 3 验证报告

在 `Claude Code CLI` 中输入：

```text
请创建或更新 docs/verification/YYYY-MM-DD-optimize-dashboard-readability.md。

要求：
1. 记录 dashboard 自动化测试命令和结果；
2. 记录 Review checkpoint 结论：scope/spec review、diff/code review、completion review 是否通过；
3. 记录 `/admin` 和 `/mentors-dashboard` 的 visible Before / After 验证结果；
4. 标注每条 OpenSpec 验收标准是否通过；
5. 引用 ../notes/webpage-change-evidence-optimize-dashboard-readability.md；
6. 如果失败或阻塞，写清原因、影响范围和下一步；
7. 不修改业务代码，不开始 configure-qr-base-url。
```

### 第 12 步：总结 Change 3 执行结果

在 `Claude Code CLI` 中输入：

```text
请总结 optimize-dashboard-readability 的执行结果。

要求：
1. 列出完成的 plan 任务；
2. 列出实际修改的文件；
3. 列出测试命令和结果；
4. 引用网页 evidence 文件；
5. 说明为什么这是 visible change；
6. 不开始 configure-qr-base-url 或任何后续 scope。
```

## 验收标准

通过标准：

- Change 3 OpenSpec change 已创建。
- `proposal.md`、`design.md`、`specs/.../spec.md`、`tasks.md` 已在本节创建完成，或缺失项已按 OpenSpec 指令补齐。
- Superpowers design doc 和 plan 已在本节生成，并通过 `find ... | sort` 记录真实路径。
- `/admin` 和 `/mentors-dashboard` 已被明确列为后面执行阶段必须记录的网页 evidence 页面。
- 同一份 `notes/webpage-change-evidence-optimize-dashboard-readability.md` 先记录预实现检查清单，后补 Before / After，没有维护单独的 prep 草稿文件。
- 你已经写清至少两类可见变化，以及它们对应的 OpenSpec 验收标准。
- 你已经按真实 plan 完成 Change 3 的实现，或把阻塞点清楚记录。
- `notes/webpage-change-evidence-optimize-dashboard-readability.md` 有 Before 和 After。
- `docs/verification/YYYY-MM-DD-optimize-dashboard-readability.md` 已记录测试结果、visible evidence、OpenSpec 验收标准和阻塞状态。
- Evidence 分类为 `visible change`。
- 页面变化能关联到 OpenSpec 验收标准。

需要停下处理：

- 如果 Claude 直接开始改模板，停止，回到本节 artifact checkpoint。
- 如果 proposal 扩成完整 analytics、权限系统或前端框架，回到 OpenSpec 收缩范围。
- 如果你还说不清要看哪两个页面、期待看到什么 visible signal，不要进入实现阶段。
- 如果还没记录真实 plan 路径，不要进入实现阶段。
- 如果还没记录 Before，就不要开始实现。
- 如果页面变化无法对应 OpenSpec 验收标准，先补 evidence 或回到 spec。

## 常见卡点

- 以为准备了 UI change，页面就应该已经变化。
- 没有先写清网页 evidence 清单，就急着开始实现。
- 把 Change 3 evidence 拆成 prep 文件和最终文件，导致验收记录分叉。
- 把统计数字写成模板里的固定文案，而不是来自真实查询数据。
- 还没有 plan 就让 Claude 直接“优化 dashboard”。
- 只跑测试，不看页面。
- 页面变化看到了，但没有写对应哪个验收标准。
- 把 dashboard polish 扩大成完整 UI redesign。
