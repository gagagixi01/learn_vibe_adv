# 10. 准备并执行 Change 4：QR base URL 配置硬编码

## 预计用时

60-90 分钟。

## 本节位置

上一节已经在 `09` 里完成了第一个 `visible change`：dashboard 可见优化。本节切换到第四个 change：`configure-qr-base-url`。

这个 change 用来练习另一种真实优化：用户默认看不出页面变化，但后台配置能力变稳。当前 lab 的 `overclocked_helpdesk/utils/qr.py` 把 QR 目标地址硬编码为 `http://127.0.0.1:8000/team-status?team=<id>`。本节只负责把 Change 4 本身走完：先准备，再实现，再做 QR 专项验证和验证报告。四个 change 的最终 validate / archive 收口放到 `11`。

Change 4 最终应归类为 `no visible change`。学生要同时证明两件事：默认页面外观没有计划外变化，测试和 verification 记录又能证明 QR 目标 URL 已从硬编码变成可配置。

## 工具使用规则

- 终端用于检查当前代码、查看 `openspec/` 和 `docs/superpowers/` 路径、运行测试、启动服务、记录 before / after。
- `Claude Code CLI` 用于创建 OpenSpec change、触发 Superpowers brainstorming、生成 writing plan、按真实 plan 执行 Change 4，并整理 QR change 验证报告。
- 本节先做准备，再做实现；不要在还没补齐 artifacts 和真实 plan 路径前直接改代码。
- 本节不顺手重做 QR UI，不修改 dashboard change，不加入新需求。

## 学习目标

- 把“硬编码本地 URL”识别成独立配置 hardening change。
- 学会规划并执行一个默认页面不变、但后台行为可测试的 no-visible change。
- 在 plan 里明确先写测试，再改配置和 QR 工具函数。
- 用 no-visible Before / After evidence 证明页面没有计划外变化。
- 完成 QR change 的测试、evidence 和验证报告，为下一节最终收口准备证据。

## 本节产出物

- `openspec/changes/configure-qr-base-url/`：本节创建
- Change 4 的 OpenSpec `proposal.md`、`design.md`、`specs/.../spec.md`、`tasks.md`：本节创建
- `docs/superpowers/specs/` 下的 Change 4 design doc，真实路径以 `find` 输出为准，本节创建
- `docs/superpowers/plans/` 下的 Change 4 plan，真实路径以 `find` 输出为准，本节创建
- Change 4 的测试和最小实现结果
- `notes/webpage-change-evidence-configure-qr-base-url.md`
- `docs/verification/YYYY-MM-DD-configure-qr-base-url.md`
- QR change 的完成状态，或清楚记录的阻塞原因

## 操作步骤

### 第 1 步：确认 QR 风险来自当前代码

在终端执行：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
git status --short
sed -n '1,120p' overclocked_helpdesk/utils/qr.py
sed -n '1,120p' overclocked_helpdesk/config.py
rg -n "127.0.0.1|team-status|generate_team_qr|APP_BASE_URL" overclocked_helpdesk tests
```

你应该能看到：

- `generate_team_qr()` 里硬编码了 `http://127.0.0.1:8000`。
- `Settings` 里还没有 `APP_BASE_URL`。
- Admin 页面按钮和 QR API 可以保持现有 response shape，不需要视觉改版。

本步只确认问题，不修改代码。

### 第 2 步：创建 Change 4 OpenSpec proposal

在 `Claude Code CLI` 中输入：

```text
/opsx:propose "让 OverClocked Helpdesk 的 QR 码目标地址使用可配置 base URL。

Change 名称建议：configure-qr-base-url。

背景：
当前 overclocked_helpdesk/utils/qr.py 把 QR 码目标地址硬编码为 http://127.0.0.1:8000/team-status?team=<id>。
这在本地开发可用，但迁移到真实活动域名时会生成错误 QR。

目标：
1. 在 settings 中增加 APP_BASE_URL，默认值保持 http://127.0.0.1:8000；
2. generate_team_qr(team_id) 使用 APP_BASE_URL 生成 team-status URL；
3. base URL 末尾是否带 / 都不应生成双斜杠；
4. 现有 admin QR 生成按钮、静态文件路径和 API response shape 保持不变；
5. 默认配置下页面外观不应变化。

验收：
- 有测试证明默认 APP_BASE_URL 会生成 http://127.0.0.1:8000/team-status?team=<id>；
- 有测试证明自定义 APP_BASE_URL 会进入 QR 内容；
- 有测试证明末尾带 / 的 base URL 不会生成双斜杠；
- 有回归检查证明 admin 页面默认外观无明显变化；
- 不重做 QR UI，不改团队状态页面，不加入部署系统。"
```

### 第 3 步：检查 OpenSpec artifacts

在终端执行：

```bash
openspec status --change configure-qr-base-url --json
openspec status --change configure-qr-base-url
find openspec/changes/configure-qr-base-url -maxdepth 4 -type f | sort
```

### 第 4 步：用 Superpowers 深化 no-visible 设计

在 `Claude Code CLI` 中输入：

```text
请基于 configure-qr-base-url 的 OpenSpec artifacts 使用 Superpowers brainstorming。

要求：
1. OpenSpec 是需求事实源；
2. 只深化配置字段、默认值、URL 拼接规则、测试策略和 no-visible evidence；
3. 推荐测试方式优先捕获 QRCode.add_data 的 URL，不要求解码 PNG；
4. 输出 design doc 到 docs/superpowers/specs/，并在完成后告诉我实际文件路径；
5. 不重做 QR 页面，不改团队状态页面，不改部署系统，不改业务代码。
```

回到终端执行：

```bash
find docs/superpowers/specs -type f | sort
```

记录 QR config design doc 的真实路径。

### 第 5 步：生成 Change 4 实施计划

先在终端记录当前 `base-ref`：

```bash
git rev-parse HEAD
```

然后在 `Claude Code CLI` 中输入：

```text
请使用 Superpowers writing-plans，为 configure-qr-base-url 创建实施计划。

输入：
- OpenSpec change: openspec/changes/configure-qr-base-url/
- Design Doc: <paste the actual QR config design doc path from Step 4>
- base-ref: <paste the real git rev-parse HEAD output>

要求：
1. plan 放到 docs/superpowers/plans/，并在完成后告诉我实际文件路径；
2. 第一项实现任务必须先写失败测试；
3. 测试默认 APP_BASE_URL；
4. 测试自定义 APP_BASE_URL；
5. 测试末尾斜杠不会生成双斜杠；
6. 推荐通过 monkeypatch/capture QRCode.add_data 参数验证 URL，不要求解码 PNG；
7. 最小实现只改 settings 和 QR URL 构造；
8. 保持 admin QR response shape 和页面外观不变。
```

回到终端执行：

```bash
find docs/superpowers/plans -type f | sort
```

记录 QR config plan 的真实路径。后面的执行和收尾都必须复用这条真实路径。

### 第 6 步：确认真实 plan 路径并记录 no-visible Before

在终端执行：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
git status --short
find docs/superpowers/plans -type f | sort
find docs/superpowers/specs -type f | sort
```

找到并记录 `configure-qr-base-url` 的真实 design doc 路径和 plan 路径。

然后在终端执行：

```bash
cd learn_vibe_adv
cp notes/webpage-change-evidence.md notes/webpage-change-evidence-configure-qr-base-url.md
cd overclocked-helpdesk-lab
source .venv/bin/activate
python scripts/seed_data.py
uvicorn overclocked_helpdesk.main:app --reload
```

在浏览器记录 Before：

- `http://127.0.0.1:8000/admin`
- `http://127.0.0.1:8000/team-status?team=1`

把 `本轮预期变化` 写成：

```text
configure-qr-base-url 是 no visible change。默认配置下页面外观不应明显变化；证据主要来自 QR URL 构造测试和 admin 页面回归检查。
```

### 第 7 步：按 plan 执行 Change 4

在 `Claude Code CLI` 中输入：

```text
请阅读 configure-qr-base-url 的 OpenSpec artifacts 和我刚记录的 Superpowers plan 真实路径。

输入：
- OpenSpec change: openspec/changes/configure-qr-base-url/
- Superpowers plan: <paste the actual QR config plan path>

先不要实现。请先回答：
1. 本 change 为什么是 no visible change；
2. 哪些测试必须先失败；
3. 哪些文件预计会被修改；
4. 哪些 response shape 或页面外观必须保持不变；
5. 哪些范围明确不做。
```

然后继续在 `Claude Code CLI` 中输入：

```text
请按真实 plan 执行 configure-qr-base-url，直到整个 change 完成或出现明确阻塞。

要求：
1. 只执行 configure-qr-base-url；
2. 先写失败测试，再做最小实现；
3. 覆盖默认 APP_BASE_URL、自定义 APP_BASE_URL、末尾斜杠三种情况；
4. 推荐通过 monkeypatch/capture QRCode.add_data 参数验证 URL；
5. 保持 admin QR response shape 和默认页面外观不变；
6. 每个任务完成后都做三层 Review checkpoint：
   - scope/spec review：是否符合 configure-qr-base-url 的 OpenSpec 验收标准和真实 plan；
   - diff/code review：`git diff` 是否只改配置、QR URL 构造和相关测试，是否没有 QR UI 或部署系统改动；
   - completion review：测试、no-visible evidence 和验证报告还缺什么；
7. 每一步都贴出运行命令、结果和 review 结论；
8. 不执行计划外功能。
```

### 第 8 步：终端独立验证 Change 4

回到终端执行：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
source .venv/bin/activate
python -m pytest tests/ -q
python -m pytest tests/ -q -k "qr or config"
git status --short
```

如果 `-k "qr or config"` 没有匹配测试，改用 Claude plan 里记录的真实 QR 测试文件路径运行，例如：

```bash
python -m pytest tests/test_qr_config.py -q
```

### 第 9 步：记录 no-visible After

如果服务已经停止，重新启动：

```bash
source .venv/bin/activate
uvicorn overclocked_helpdesk.main:app --reload
```

重新打开：

- `http://127.0.0.1:8000/admin`
- `http://127.0.0.1:8000/team-status?team=1`

更新：

```text
../notes/webpage-change-evidence-configure-qr-base-url.md
```

After 观察至少写清：

- 默认 admin 页面外观是否与 Before 一致；
- team status 页面是否仍可打开；
- QR URL 构造由哪些测试证明；
- 分类写 `no visible change`。

### 第 10 步：写 Change 4 验证报告

在 `Claude Code CLI` 中输入：

```text
请创建或更新 docs/verification/YYYY-MM-DD-configure-qr-base-url.md。

要求：
1. 记录自动化测试命令和结果；
2. 记录 Review checkpoint 结论：scope/spec review、diff/code review、completion review 是否通过；
3. 记录默认页面 no-visible 验证步骤和结果；
4. 标注每条 OpenSpec 验收标准是否通过；
5. 引用 ../notes/webpage-change-evidence-configure-qr-base-url.md；
6. 如果失败，写清原因和下一步；
7. 不修改业务代码。
```

## 验收标准

在终端运行或检查：

```bash
cd learn_vibe_adv/overclocked-helpdesk-lab
find docs/verification -type f | sort
find openspec -maxdepth 5 -type f | sort | sed -n '1,200p'
cd ..
find notes -maxdepth 1 -name 'webpage-change-evidence-*.md' -type f | sort
git status --short
```

通过标准：

- `configure-qr-base-url` OpenSpec change 已在本节创建。
- OpenSpec artifacts 已在本节补齐。
- Superpowers design doc 和 plan 已在本节生成并记录真实路径。
- Change 4 已执行完成，或阻塞原因被清楚记录。
- `notes/webpage-change-evidence-configure-qr-base-url.md` 证明默认页面无明显变化。
- 自动化测试通过，或失败原因被清楚记录。
- `docs/verification/YYYY-MM-DD-configure-qr-base-url.md` 已记录测试、no-visible evidence 和 OpenSpec 验收标准。
- 你能说明为什么“没有页面变化”仍然可以是有效优化。

需要停下处理：

- 如果 Claude 开始改 QR 生成代码，停止，回到本节 artifact checkpoint。
- 如果 proposal 扩成部署系统、二维码管理后台或 UI redesign，回到 OpenSpec 收缩范围。
- 如果还没记录真实 plan 路径，不要进入实现阶段。
- 如果当前 Change 4 还没完成，不要进入 `11` 的最终 validate / archive 收口。
- 如果 evidence 只写“页面看起来可以”，但没有说明测试证明了什么，先补证据再进入 `11` 的最终收口。

## 常见卡点

- 以为 no-visible change 不需要测试。
- 试图通过肉眼看 PNG 证明 URL 正确。
- 把配置 hardening 扩成完整部署配置系统。
- 以为 no-visible change 可以跳过 before / after。
- 只看 admin 页面，不验证 QR 内容。
- 把“测试通过”和“QR change 已经有验证报告”当成同一个概念。
