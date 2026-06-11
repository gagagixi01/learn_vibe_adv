# 04. 创建 Change 1 OpenSpec：质量基线

## 预计用时

60-80 分钟。

## 本节位置

上一节已经完成 baseline：项目已克隆、目录已确认、服务已跑通或阻塞已记录、`learn_vibe_adv/notes/baseline.md` 已生成。到这一刻为止，仓库仍然应该基本保持 upstream startup 状态，还没有 OpenSpec change、Superpowers 设计文档、验证报告或新增测试目录。

本节把原来的 OpenSpec onboarding 和 Change 1 proposal 合在一起：先在真实项目内部初始化 `openspec/`，再把当前系统能力写进 `docs/current-capabilities.md`，最后基于这份能力地图创建 Change 1 `quality-baseline` OpenSpec change。

`docs/current-capabilities.md` 不是旁路笔记，而是本节创建 proposal 的输入。它用来提醒 Claude：这轮质量基线只补测试和记录当前行为，不重写已经存在的 mentor assignment 或 query creation 行为。

## 工具使用规则

- 终端负责检查或安装 `OpenSpec`、执行 `openspec init`、查看生成文件和 change artifacts。
- `Claude Code CLI` 负责只读整理能力地图、调用 OpenSpec proposal、审查 proposal 范围。
- 本节只创建能力地图、规格和任务框架，不使用 Superpowers，不实现代码，不写 implementation plan。
- 本节可以创建 OpenSpec change，但不能开始 brainstorming、TDD、`docs/superpowers/` 产物或业务代码修改。

## 学习目标

- 在已有项目里初始化 `OpenSpec`。
- 写出当前能力地图，避免 `quality-baseline` 重写已有行为。
- 用 OpenSpec 描述一个“质量基线”change。
- 明确 Change 1 只补测试和记录当前 behavior。
- 学会检查 proposal 是否过大，是否越过课程边界。

## 本节产出物

- `learn_vibe_adv/overclocked-helpdesk-lab/openspec/`：本节新初始化
- `learn_vibe_adv/overclocked-helpdesk-lab/docs/current-capabilities.md`：本节新创建
- `learn_vibe_adv/overclocked-helpdesk-lab/openspec/changes/quality-baseline/` 下的新 proposal、design、spec artifacts，以及可继续进入 `05`、`06` 的 tasks artifact（或你本地实际生成的等价 change 目录）

## 操作步骤

### 第 1 步：检查 OpenSpec

在终端执行：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
openspec --version
```

如果没有安装，在终端执行：

```bash
npm install -g @fission-ai/openspec@latest
```

如果安装失败，先检查 Node 版本，再把完整报错交给 `Claude Code CLI`。`OpenSpec` 官方要求使用较新的 Node 版本，课程里以你本机实际可用版本为准。

### 第 2 步：初始化 OpenSpec

在终端执行：

```bash
openspec init
```

### 第 3 步：查看生成内容

在终端执行：

```bash
find openspec -maxdepth 3 -type f | sort
```

你应该能看到 `OpenSpec` 相关说明、模板或 change 结构。不同版本输出可能略有差异，以本地实际结果为准。

### 第 4 步：确认本节仍然只做 OpenSpec

继续前先确认：本节还没有进入 `Superpowers` 阶段。不要检查 skills，不要开始 brainstorming，也不要创建 `docs/superpowers/`。这些都留到 `05`。

### 第 5 步：让 Claude Code 生成当前能力地图

在 `Claude Code CLI` 中输入：

```text
请阅读当前项目和 openspec/ 目录，不要修改业务代码。

请帮我生成 docs/current-capabilities.md，作为 OpenSpec 优化前的能力地图。

工程上下文：
- 我正在做 pre-change capability assessment；
- openspec/ 刚初始化完成，但当前 checkout 还没有创建任何 OpenSpec change；
- planned `quality-baseline` 是后续测试覆盖重点，用来保护现有 mentor assignment 和 query creation 行为，不是现在开始实现。

要求：
1. 只创建或修改 docs/current-capabilities.md；
2. 按角色整理：team、mentor、admin；
3. 记录当前已有能力、待验证能力、明显缺失的测试；
4. 记录 planned `quality-baseline` 应该保护、而不应该重写的已有行为；
5. 如果提到后续工作，必须标注为 planned/pre-change context，不要写成已经开始的 change；
6. 不要创建 OpenSpec change；
7. 不要使用 Superpowers；
8. 不要开始 brainstorming；
9. 不要创建 docs/superpowers/；
10. 不要生成 implementation plan；
11. 不要修改 overclocked_helpdesk/ 或 templates/。
```

### 第 6 步：确认能力地图能约束 proposal

打开 `docs/current-capabilities.md`，确认它至少写清三件事：

- 当前系统已经做了什么。
- planned `quality-baseline` 不应该重写哪些已有行为。
- 后续新增或优化必须进入 OpenSpec change。
- 如果文档提到后续工作，它必须标注为 planned/pre-change context，而不是已经创建或正在执行的 change。

如果这份能力地图只是文件流水账，先让 Claude 重新整理。不要带着模糊的能力地图进入 proposal。

### 第 7 步：确认当前仓库状态

在终端执行：

```bash
git status --short
```

当前变化应该主要来自 `openspec/` 和 `docs/current-capabilities.md`。重点是你要知道哪些文件变化属于 onboarding 和能力地图，哪些文件变化会属于接下来的 `quality-baseline` proposal。

### 第 8 步：在 Claude Code CLI 中创建 proposal

在 `Claude Code CLI` 中输入：

```text
/opsx:propose "为 OverClocked Helpdesk 建立 quality-baseline 质量基线 change。

创建 proposal 前，请先读取 docs/current-capabilities.md，并把它作为现有行为边界。
本步骤只创建 OpenSpec artifacts。不要使用 Superpowers，不要开始 brainstorming，不要创建 docs/superpowers/，不要生成 implementation plan，不要修改业务代码。

Change 名称建议：quality-baseline。

目标：
1. 为 mentor assignment 逻辑补充测试，记录当前分配行为；
2. 为 team query creation 流程补充测试，记录当前最小成功路径；
3. 不改变用户可见功能；
4. 不重写 docs/current-capabilities.md 中已经记录的服务层、路由层、模板或已有业务行为；
5. 不加入登录、WebSocket、Docker、Analytics。

验收：
- proposal / design / spec 必须引用 docs/current-capabilities.md 中需要保护的现有行为；
- 可以运行一个聚焦测试命令验证 mentor assignment；
- 可以运行一个聚焦测试命令验证 query creation；
- 当前行为如果有缺陷，先用测试和文档暴露，不在本 change 中扩大成完整重构。"
```

### 第 9 步：检查生成 artifacts

在终端执行：

```bash
find openspec/changes -maxdepth 4 -type f | sort
openspec status --change <你的 quality-baseline change 名称> --json
```

找到 Change 1 的真实目录名。后续课程如果提到 `quality-baseline`，都要以你本地真实目录名为准。

### 第 10 步：让 Claude Code 审查 proposal 是否过大

在 `Claude Code CLI` 中输入：

```text
请 review 当前 quality-baseline OpenSpec change 是否过大。

要求：
1. 完整阅读 docs/current-capabilities.md、proposal.md、design.md、specs/ 下所有 spec，以及已经就绪的 `tasks.md`；
2. 只检查范围，不实现代码；
3. 确认本 change 是否只做质量基线；
4. 确认 proposal / design / spec / tasks 是否使用 docs/current-capabilities.md 保护已有 behavior；
5. 找出所有 scope creep，例如登录、WebSocket、Docker、完整 analytics、UI 重做，或没有依据地重写现有行为；
6. 如需修改，只回写 OpenSpec change artifacts，不改业务代码；
7. 不要使用 Superpowers，不要开始 brainstorming，不要创建 docs/superpowers/，不要生成 implementation plan。
```

### 第 11 步：锁定 Change 1 边界

Change 1 必须保持：

- 允许：新增测试、测试 fixture、少量文档、暴露当前行为的失败测试。
- 谨慎允许：为可测试性做极小重构，但必须写进 tasks，并且不能推翻 `docs/current-capabilities.md` 记录的现有行为。
- 不允许：新增大功能、重做 UI、重写数据库模型、接入通知平台。

## 验收标准

在终端运行或检查：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
cat docs/current-capabilities.md
find openspec/changes -maxdepth 4 -type f | sort
git status --short
```

通过标准：

- `openspec/` 已在克隆项目根目录初始化。
- `docs/current-capabilities.md` 已创建，并且按角色整理，而不是按文件流水账。
- Change 1 已创建。
- proposal 写清了“为什么补测试”和“暂不改功能”。
- proposal / design / spec / tasks 使用能力地图保护已有 behavior。
- `tasks.md` 已经在本节创建完成，并且任务能落到具体测试文件和命令，而不是只写“添加测试”。
- OpenSpec artifacts 没有扩大成完整系统重构。
- 业务代码仍然没有在本节被修改。

需要停下处理：

- 如果 `Claude` 在没有 proposal 前修改了业务代码，停止继续，回到只读 onboarding。
- 如果你还分不清 baseline、能力地图、change spec，先不要进入下一节。
- 如果 proposal 变成“完善整个 Helpdesk 系统”，让 Claude 先把范围收缩回 mentor assignment 和 query creation。
- 如果 proposal 没有使用 `docs/current-capabilities.md`，先补齐再进入 `05`。
- 如果你已经开始想实现细节，先停下来，因为设计深化是 `05` 的工作。

## 常见卡点

- 把 `OpenSpec` 初始化在课程目录，而不是克隆项目根目录。
- 把“当前能力”和“计划优化”混在一起。
- 只确认 `OpenSpec` 安装成功，却没有写当前能力地图。
- 质量改进 change 写得像功能开发 change。
- 忘记把“不改变用户可见功能”写进 proposal。
- 看到 bug 就想直接修，跳过基线测试。
- 在 proposal 阶段就开始讨论具体实现方案。
