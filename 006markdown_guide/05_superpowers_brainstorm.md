# 05. 用 Superpowers 深化设计，但不替代 OpenSpec

## 预计用时

40-60 分钟。

## 本节位置

上一节已经创建 Change 1 `quality-baseline` OpenSpec change，并且 proposal / spec / design /tasks 已经生成。

本节先确认 `Superpowers` skills 可用，再进入 `Superpowers brainstorming` 阶段：基于 OpenSpec artifacts 深化技术设计、测试策略和风险边界。OpenSpec 仍然是需求事实源，本节不写计划，也不写实现代码。

## 工具使用规则

- 终端用于查看 `OpenSpec` artifacts 和生成后的设计文档。
- `Claude Code CLI` 用于检查 `Superpowers` skills 是否可用，并调用 `Superpowers brainstorming`。
- 本节允许创建 `docs/superpowers/specs/` 下的设计文档。
- 如果发现验收场景缺失，只能回写 OpenSpec spec，不能直接改业务代码。
- 如果上一节的 `tasks.md` 还没补齐，先回到 `04` 处理，不要直接开始 brainstorming。

## 学习目标

- 学会把 OpenSpec artifacts 交给 Superpowers 作为输入，而不是让它凭空开始。
- 学会先确认 Superpowers readiness，再开始 brainstorming。
- 区分“需求规格”和“技术设计”。
- 补充测试策略、边界条件和最小可测接口。

## 本节产出物

- `learn_vibe_adv/overclocked-helpdesk-lab/docs/superpowers/specs/` 下的新 Change 1 design doc（文件名以 Claude 实际生成结果为准）
- 如有必要，对 Change 1 OpenSpec spec 的小范围补充。

## 操作步骤

### 第 1 步：检查 Superpowers 是否可用

在 `Claude Code CLI` 中输入：

```text
请检查当前 Claude Code 是否可以使用 Superpowers skills。

要求：
1. 不修改文件；
2. 只告诉我是否能看到 brainstorming、writing-plans、test-driven-development、subagent-driven-development、finishing-a-development-branch；
3. 如果不可用，请给出安装或启用步骤；
4. 不要开始 brainstorming。
```

如果 `Superpowers` 还没有安装，不要假设每个版本都有同一个命令。优先使用你当前 Claude Code 版本里可见的插件市场、插件菜单或官方等价安装方式；安装完成后重新打开 Claude Code，再回到本节继续。

在 `Claude Code CLI` 中输入：

```text
请安装Superpowers Skill，并激活
```

### 第 2 步：定位 Change 1 文件

在终端执行：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
find openspec/changes -maxdepth 4 -type f | sort
```

记录 Change 1 的真实目录名和路径。

### 第 3 步：在 Claude Code CLI 中触发 Superpowers brainstorming

在 `Claude Code CLI` 中输入，把 `<change-dir>` 替换为真实目录：

```text
请使用 Superpowers brainstorming。

先完整读取以下 OpenSpec change 文件，不要摘要后跳过：
- openspec/changes/<change-dir>/proposal.md
- openspec/changes/<change-dir>/design.md
- openspec/changes/<change-dir>/tasks.md
- openspec/changes/<change-dir>/specs/ 下所有 spec.md

约束：
1. OpenSpec 是需求事实源，不要重写需求；
2. 你的任务是做技术设计深化：测试策略、边界条件、最小可测接口、风险；
3. 如果发现缺少验收场景，只能回写 OpenSpec delta spec；
4. 不要实现代码；
5. 不要修改业务文件。

完成后产出：
- Design Doc -> 保存到 docs/superpowers/specs/ 下，并在完成后告诉我实际文件路径
- 如有缺失验收场景，回写到 OpenSpec spec。
```

### 第 4 步：重点追问测试设计

如果 Claude 的设计仍然过泛，在 `Claude Code CLI` 中继续输入：

```text
请把测试策略收缩到 Change 1 质量基线。

要求：
1. 明确 mentor assignment 至少测试哪两个行为；
2. 明确 query creation 至少测试哪一个成功路径；
3. 标注哪些测试是在记录当前行为，哪些测试会成为后续优化的保护网；
4. 不要加入 UI e2e、WebSocket、认证或部署测试。
```

### 第 5 步：发现并记录真实设计文档路径

在终端执行：

```bash
find docs/superpowers/specs -type f | sort
```

从输出中记录本轮 design doc 的真实路径。后面的 `06` 不要再猜文件名，要直接复用你刚记录的真实路径。

确认这份文档是技术设计和测试策略，而不是第二份需求规格。

## 验收标准

在终端运行或检查：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
find docs/superpowers/specs -type f | sort
git status --short
```

通过标准：

- `Superpowers` 设计文档已创建。
- 你已经确认 Superpowers skills 可用，或把不可用的阻塞和安装/启用步骤记录清楚。
- 你已经记录了 design doc 的真实路径，后续会直接复用这个路径。
- 设计文档解释了测试边界、fixtures、风险和最小验证命令。
- OpenSpec 仍然是 canonical spec。
- 没有开始写实现代码，也没有开始写执行计划。

需要停下处理：

- 如果 `Superpowers` 重新定义了需求，要求它回到“基于 OpenSpec 深化技术设计”。
- 如果 Superpowers skills 不可用，不要跳过第 1 步直接让 Claude 手写设计文档。
- 如果你已经在讨论 task-by-task 实施顺序，先停下来，因为那是 `06` 的工作。

## 常见卡点

- 把 design doc 写成 PRD。
- 没确认 Superpowers 可用，就直接让 Claude “设计一下”。
- 测试策略太泛，只写“添加适当测试”。
- 发现缺失验收场景却只写在 design doc，而没有回写 OpenSpec spec。
- 以为 Claude 一定会生成和课程示例完全一样的文件名。
- 在 brainstorming 阶段就开始实现代码。
