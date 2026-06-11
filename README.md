# Python 新人 Vibecoding 进阶路线：用 OpenSpec + Superpowers 优化一个真实 GitHub 项目

这是一套承接 `learn_vibe` 的中文进阶课程。入门课从零做一个最小任务管理系统；进阶课不再从零开始，而是克隆一个已经存在的 GitHub 项目，用 `Claude Code`、`OpenSpec` 和 `Superpowers` 完成真实代码库的理解、基线验证、规格驱动优化、测试驱动实现、Review 和归档。

本课程选用的练习项目是 [gagagixi01/OverClocked-Helpdesk](https://github.com/gagagixi01/OverClocked-Helpdesk)。它是一个面向黑客松和校园活动的 Helpdesk 系统，技术栈包含 `FastAPI`、`SQLite`、`SQLAlchemy`、`Jinja2` 和 `Uvicorn`。项目规模不大，但包含团队提交问题、导师响应、管理员管理等多角色流程，足够练习更复杂的 AI 编程工作流。

本课程的起点是一个刚刚 `git clone` 下来的 upstream 仓库。学生一开始看到的应该是原始项目目录；`openspec/`、`docs/current-capabilities.md`、`docs/superpowers/`、新增测试和 `docs/verification/` 都是后续课程逐步创建出来的产物，不应该被理解成 startup repo 自带内容。

## 学习目标

完成本课程后，你应该能做到：

- 先理解 `OpenSpec` 和 `Superpowers` 的职责，再开始在真实项目里使用它们。
- 在已有代码库里先读懂结构，再让 AI 修改代码。
- 使用 `OpenSpec` 创建、验证和归档变更规格。
- 使用 `Superpowers` 做需求澄清、设计深化、TDD 计划、subagent 执行判断和 Review。
- 理解 `Review` 的三层检查：`scope/spec review`、`diff/code review`、`completion review`。
- 用测试和手动验证记录优化前后的行为变化。
- 区分 visible change、no visible change 和 artifact-only preparation，并用网页证据表记录差异。
- 能区分 `Review`、`verification report`、`openspec validate` 和 `archive` 的职责。
- 把 `OpenSpec propose -> Superpowers brainstorm -> write-plan -> TDD -> Review -> verification report -> validate -> archive` 变成可复用工作流。

## 课程目录

1. `guide/00_overview.md`
2. `guide/01_repo_selection_and_workflow.md`
3. `guide/02_git_basics.md`
4. `guide/03_clone_and_baseline.md`
5. `guide/04_openspec_proposal.md`
6. `guide/05_superpowers_brainstorm.md`
7. `guide/06_superpowers_write_plan.md`
8. `guide/07_tdd_subagent_execution.md`
9. `guide/08_slack_notification_bugfix.md`
10. `guide/09_dashboard_change.md`
11. `guide/10_qr_change.md`
12. `guide/11_reflection_and_transfer.md`

## 练习路线

本课程先用 `00`、`01` 让学生理解 `OpenSpec` 与 `Superpowers` 以及它们在真实项目里的配合方式，再用 `02` 学会本地 Git，用 `03` 理解 Remote GitHub、克隆项目并记录基线，最后从 fresh clone 进入真实项目优化。后续练习围绕四个独立 change 展开：

- Change 1 `quality-baseline`：补充导师分配和问题创建测试，建立质量基线。结果通常是 `no visible change`。
- Change 2 `stabilize-slack-notification-config`：把 baseline 中发现的 Slack 配置风险变成一个独立 bugfix。结果通常是 `no visible change`。
- Change 3 `optimize-dashboard-readability`：改进管理端和导师端仪表盘可读性，增加轻量统计和状态可见性。结果是 `visible change`。
- Change 4 `configure-qr-base-url`：让 QR 码生成使用可配置 base URL，默认本地页面体验保持不变。结果是 `no visible change`。

每一轮都必须经过规格、设计、计划、测试、实现、验证和归档。不要让 Claude Code 直接“优化整个项目”。先读懂 startup repo 的真实状态，再一步步把 OpenSpec、Superpowers artifacts、测试、subagent 执行判断和验证记录加上去。

`07` 会把 Change 1 的 TDD 执行和 `subagent-driven-development` 拆分、委派、review 边界合在一起练习；它不新增第五个 change，也不替代 TDD、验证或 evidence。

从 `07` 开始，主会话必须对 AI 或 subagent 输出做三层 Review checkpoint：

- `scope/spec review`：这次改动是否仍然属于当前 OpenSpec change 和真实 plan；
- `diff/code review`：`git diff` 是否干净、允许文件范围是否正确、是否出现 unrelated changes；
- `completion review`：当前结果是否已经具备进入 verification report、`openspec validate` 和 archive 的证据。

从 `07` 开始，每次 change 都要用 `notes/webpage-change-evidence.md` 的模板记录 before / after。即使页面没有变化，也要写清这是 `no visible change`；如果某一节只准备 OpenSpec / Superpowers artifacts，还没有实现，就写清这是 `artifact-only preparation`，避免把“我没看到变化”误解成“这个 change 没有效果”。

每个已执行 change 都要整理 `docs/verification/YYYY-MM-DD-<change-name>.md`。它不是额外作文，而是 Review 之后的证据汇总：至少记录测试命令与结果、网页 evidence 路径、OpenSpec 验收标准状态，以及 blocker。

课程最后的收口顺序固定为：

`Review -> verification report -> openspec validate -> archive`

可以把这四步理解成四个不同问题：

- `Review`：这次改动对不对，有没有越界；
- `verification report`：有哪些证据证明它已经完成；
- `openspec validate`：OpenSpec artifacts 本身是否通过 CLI 校验；
- `archive`：这个 change 是否已经可以进入长期规格历史。

Claude Code 可能会生成一个与课程示例不同、但仍然有效的 spec / plan 文件名。课程里出现的示例文件名只是提示；真实文件系统才是事实源。后续步骤如果要继续使用某个生成文件，先用 `find ... | sort` 找到真实路径，再把这个真实路径贴回下一步的命令或提示词。

## 前置要求

- 已完成 `coding_with_ai/learn_vibe/guide/00-10`。
- 理解 `FastAPI`、`SQLite`、`Jinja2`、轻量 `SDD`、轻量 `TDD` 和 `Review`。
- 本课程会在 `guide/02_git_basics.md` 中完成 Git 安装或验证，再在 `guide/03_clone_and_baseline.md` 中克隆项目。
- 已安装并能使用 `Claude Code`。
- 本课程会安装或使用 `OpenSpec` 与 `Superpowers`，具体步骤见 `04_openspec_proposal` 和 `05_superpowers_brainstorm`。

## 关键参考

- 本地参考：`Clippings/OpenSpec + Superpowers SDD+TDD 双驱动 AI 编程工作流.md`
- `00` 与 `01` 建议先完整阅读本地 clipping，再进入后续章节
- OpenSpec: https://github.com/Fission-AI/OpenSpec
- Superpowers: https://claude.com/plugins/superpowers
- Claude Code Skills: https://code.claude.com/docs/en/slash-commands
