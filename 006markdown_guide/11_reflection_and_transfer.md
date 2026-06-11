# 11. 最终收口并复盘迁移这套工作流

## 预计用时

45-60 分钟。

## 本节位置

这是进阶路线的收尾课。上一节已经完成 `configure-qr-base-url` 的实现、no-visible evidence 和验证报告；本节先把四个已执行 change 做归档前验证、OpenSpec validate、Superpowers finishing 和 archive，然后再把经验整理成自己的工作流手册。

本课程最终不是为了记住四个具体改动，而是为了学会：在真实代码库里如何先建安全网、修小 bug、做可见优化、做不可见硬化，并且让每一步都有证据。

## 工具使用规则

- 终端用于查看 artifacts、运行 `openspec validate`、执行 `openspec archive`、确认 archived 路径。
- `Claude Code CLI` 用于辅助判断是否可以归档、运行 Superpowers finishing、整理结构和辅助复盘。
- `verify` 是课程里的收尾判断；`openspec validate` 才是 OpenSpec CLI 命令。
- 不要在本节加入新功能，不要为了 archive 临时扩大 scope。

## 学习目标

- 区分测试通过、归档前验证、OpenSpec validate 和 archive。
- 按顺序收口四个已执行 change，让规格历史可追踪。
- 复盘真实项目优化中发生了什么。
- 讲清 OpenSpec、Superpowers、TDD、Review 和 Archive 的关系。
- 写出可复用的 AI 编程工作流。
- 能用 evidence 表解释哪些 change 是 visible change、no visible change，哪些课程阶段只是 artifact-only preparation。
- 能说明不可见变化如何被测试和验证证明。

## 收口概念边界

本节会同时出现 `Review`、`verify`、`openspec validate` 和 `archive`，它们不是同一件事：

| 概念 | 问的问题 | 主要证据 |
|---|---|---|
| `Review` | 这次改动是否符合 OpenSpec、plan、diff 和代码质量要求？有没有越界？ | Claude review 总结、`git diff`、allowed files、测试覆盖说明 |
| `verify` | 测试、网页 evidence、verification docs 是否证明 change 完成？ | pytest 输出、webpage evidence、verification report |
| `openspec validate` | OpenSpec artifacts 的格式和结构是否通过 CLI 校验？ | `openspec validate <name> --strict` 输出 |
| `archive` | 已完成并验证的 change 是否可以收进长期规格历史？ | archive 命令输出、archived 路径、更新后的 `openspec/specs/` |

简单记法：`Review` 先判断“改得对不对、有没有越界”，`verify` 再证明“完成没有”，`openspec validate` 检查规格文件本身，`archive` 才是把完成的 change 收进历史。

## 本节产出物

- 四个 change 的归档前验证结果，或清楚记录的阻塞原因
- 四个 change 的 OpenSpec validate 结果，或清楚记录的阻塞原因
- 四个 change 的 archive 结果和 archived 路径，或清楚记录的阻塞原因
- `notes/final-playbook.md`

## 操作步骤

### 第 1 步：收集最终收口证据

在终端执行：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
find openspec -maxdepth 5 -type f | sort | sed -n '1,200p'
find docs/superpowers -maxdepth 4 -type f | sort
find docs/verification -maxdepth 2 -type f | sort
cd ..
find notes -maxdepth 1 -name 'webpage-change-evidence-*.md' -type f | sort
```

确认至少能看到四类证据：

- OpenSpec change artifacts；
- Superpowers design docs 和 plans；
- `docs/verification/` 下的验证报告；
- `notes/` 下的 webpage evidence。

如果某个 change 的证据缺失，不要进入 archive；先回到对应章节补齐或记录阻塞。

### 第 2 步：验证四个 scope 是否可以归档

在 `Claude Code CLI` 中输入：

```text
请按顺序检查这些已执行 scope 是否可以归档。

注意：
- verify 是收尾判断，不是 openspec CLI 命令；
- OpenSpec CLI 命令应使用 openspec validate

要求：
1. 如果 quality-baseline 仍未归档，先检查它；
2. 如果 stabilize-slack-notification-config 仍未归档，再检查它；
3. 如果 optimize-dashboard-readability 仍未归档，再检查它；
4. 最后检查 configure-qr-base-url；
5. 每个 scope 都要确认测试、evidence、verification report 和 OpenSpec 验收标准；
6. 每个 scope 都要运行对应的 openspec validate <name> --strict；
7. 如果失败，只修复与当前 scope 直接相关的问题；
8. 不加入新需求。
```

如果你本地某个目录名和课程默认名不同，先用真实目录名。可以用下面命令确认：

```bash
find openspec/changes -maxdepth 2 -type d | sort
```

回到终端执行：

```bash
openspec validate quality-baseline --strict
openspec validate stabilize-slack-notification-config --strict
openspec validate optimize-dashboard-readability --strict
openspec validate configure-qr-base-url --strict
```

也可以在逐个检查后再运行一次总检查：

```bash
openspec validate --all --strict
```

### 第 3 步：运行 Superpowers finishing

在 `Claude Code CLI` 中输入：

```text
请使用 Superpowers finishing-a-development-branch 检查当前收尾状态。

要求：
1. 对照四个 scope 的 OpenSpec artifacts、Superpowers plans、测试结果、verification docs 和 webpage evidence；
2. 检查是否有计划外改动；
3. 输出每个 scope 是否可以归档；
4. 如果不能归档，说明具体阻塞和应该回到哪个文件处理；
5. 不创建 PR，不推送远程。
```

只有在 Step 2 的 `openspec validate` 和本步 finishing 都没有阻塞后，才进入 archive。

### 第 4 步：按顺序 archive 四个 change

在 `Claude Code CLI` 中输入：

```text
请按顺序归档已验证通过的 OpenSpec change。

要求：
1. 如果 quality-baseline 仍未归档，先归档它；
2. 如果 stabilize-slack-notification-config 仍未归档，再归档它；
3. 如果 optimize-dashboard-readability 仍未归档，再归档它；
4. 最后归档 configure-qr-base-url；
5. 每次都使用本地 OpenSpec archive 命令；
6. 贴出归档结果；
7. 确认 archived change 路径；
8. 不删除验证报告；
9. 不使用 --no-validate；openspec archive 默认会做 validation。
```

如果你选择手动执行 archive，使用真实目录名：

```bash
openspec archive quality-baseline
openspec archive stabilize-slack-notification-config
openspec archive optimize-dashboard-readability
openspec archive configure-qr-base-url
```

### 第 5 步：确认 archived 路径

归档后在终端执行：

```bash
find openspec -maxdepth 5 -type f | sort | sed -n '1,240p'
find docs/verification -maxdepth 2 -type f | sort
git status --short
```

你要能说明：

- 哪些 change 已经进入 archive；
- 长期 `openspec/specs/` 是否被更新；
- 验证报告和 webpage evidence 是否仍然保留；
- 是否还有未处理的阻塞。

### 第 6 步：让 Claude Code 提问，而不是代写

在 `Claude Code CLI` 中输入：

```text
请不要替我写最终复盘。

请根据当前 OpenSpec、Superpowers plan、verification docs、webpage evidence、archive 结果和 git diff，问我 8 个复盘问题：
1. 这个项目原本是什么；
2. 我们做了哪四个 change；
3. OpenSpec 在每个 change 里解决什么问题；
4. Superpowers 在每个 change 里解决什么问题；
5. TDD 捕捉到了什么风险；
6. 哪个 change 是 visible change，它的 before / after 证据是什么；
7. 哪些 change 是 no visible change，我怎样证明它们真的有效；
8. 下次迁移到新项目时，我会保留和调整什么。

每个问题后给一个简短参考答案，方便我对照。
```

### 第 7 步：写最终 playbook

在 `Claude Code CLI` 中输入：

```text
请根据我的回答创建 ../notes/final-playbook.md。

要求：
1. 使用我的表达，不要写成正式论文；
2. 包含项目背景、四个 change、OpenSpec 作用、Superpowers 作用、TDD 收获、Review 收获、网页 evidence 收获、不可见变化如何被证明、validate/archive 收口、可迁移流程；
3. `Review 收获` 必须写具体例子，例如：review 抓住了 scope creep、bad diff、缺失测试、subagent 越界输出，或确认某个 diff 可以进入 verify；
4. 如果我的回答不清楚，请先问我，不要编造；
5. 最后给一张“下次做真实项目优化”的检查清单。
```

### 第 8 步：自己删掉不能解释的话

在终端执行：

```bash
cd learn_vibe_adv
cat notes/final-playbook.md
```

逐句检查。凡是你不能向别人解释的句子，都删掉或改成自己的话。

## 参考 playbook 结构

```markdown
# 我的 OpenSpec + Superpowers 真实项目优化手册

## 我优化的项目

## 四个 change

1. quality-baseline：为什么是 no visible change
2. stabilize-slack-notification-config：为什么是 no visible change
3. optimize-dashboard-readability：为什么是 visible change
4. configure-qr-base-url：为什么是 no visible change

## OpenSpec 帮我避免了什么

## Superpowers 帮我避免了什么

## TDD 让我先发现了什么

## Review 让我改掉了什么

写具体例子：scope creep、bad diff、缺失测试、subagent 越界输出，或 review 后确认可以进入 verify 的证据。

## 网页 before / after 让我看清了什么

## 不可见变化我是怎样证明的

## validate / archive 收口我是怎样做的

## 下次迁移到新项目的流程

1. 先读 README 和目录结构
2. 记录 baseline
3. 初始化 OpenSpec
4. 每次只创建一个 change
5. 用 Superpowers 深化设计
6. 用 writing-plans 写 TDD 计划
7. 先测试失败，再最小实现
8. 记录 visible / no visible / artifact-only preparation evidence
9. 归档前验证：检查测试、evidence、verification docs 和 OpenSpec 验收标准
10. 运行 openspec validate
11. finishing 没有阻塞后再 archive
```

## 验收标准

运行或检查：

```bash
cat notes/final-playbook.md
```

文档必须能回答：项目是什么、改了什么、怎么验证、哪些变化能在网页看到、哪些变化只能由测试或 artifacts 证明、这套工作流如何迁移。

通过标准：

- 四个已执行 scope 都完成归档前验证，并且对应 OpenSpec validate 通过；否则阻塞被清楚记录。
- 四个已执行 change 都已 archive，或者阻塞被清楚记录。
- 你能用自己的话讲清完整流程。
- 最终 playbook 不是 Claude 的口号，而是你的操作经验。
- playbook 能解释四个 change 的动机、验收方式和迁移方法。
- playbook 能解释 dashboard 为什么是 `visible change`。
- playbook 能解释质量基线、Slack bugfix、QR 配置为什么是 `no visible change`，以及准备课为什么只是 `artifact-only preparation`。
- playbook 能解释 testing、归档前验证、OpenSpec validate 和 archive 的区别。

需要停下处理：

- 如果归档前验证或 openspec validate 失败，不要 archive，先修复当前 change。
- 如果 archive 前又想加新功能，停止，把新想法放到下一轮 change。
- 如果只写“OpenSpec 很重要，Superpowers 很好用”，回到 artifacts，补具体例子。
- 如果只写“页面没变”，但没有说明测试或配置验证证明了什么，回到 evidence 表补记录。

## 常见卡点

- 复盘只写结果，不写过程证据。
- 把测试、verify、review、validate、archive 混成一件事。
- 不敢写失败和阻塞，导致 playbook 不真实。
- 只写“页面有变化 / 没变化”，没有解释它对应哪个 OpenSpec 验收标准。
