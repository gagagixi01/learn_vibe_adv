# 02. 先学会本地 Git，再开始真实项目

## 预计用时

45-70 分钟。

## 本节位置

本节承接 `01` 的项目选型和工作流理解，但还不进入真实项目仓库。

你会先把 Git 这件事讲清楚：它解决什么问题、Claude Code 平时在替你做什么、你怎么在本地看懂状态、保存进度、查看差异、用 worktree 隔离实验、理解回滚。Remote GitHub 会放到下一节再讲；这一节只练本地 Git。

## 工具使用规则

- 终端负责真实执行：安装或验证 `git`、创建练习目录、运行 Git 命令。
- `Claude Code CLI` 负责协助解释：根据你的操作系统给安装命令、解释 `git status`、`git diff`、`git log` 输出。
- 本节不克隆 `OverClocked Helpdesk`，不创建 `.venv`，不进入 OpenSpec 或 Superpowers 实现流程。

下面出现的终端命令默认以 **POSIX shell** 为例（macOS / Linux / Git Bash）。如果你在 Windows PowerShell 中操作，先让 `Claude Code CLI` 把 `source`、`find`、`which` 这类命令换成当前平台的等价写法，再继续本节。

## 学习目标

- 在自己机器上安装或确认 `git` 可用。
- 理解 Git 的三个本地区域：工作区、暂存区、本地仓库。
- 学会用一个练习仓库运行 `git init`、`git status`、`git add`、`git commit`、`git log --oneline`、`git diff`。
- 理解 `git worktree` 可以让同一个本地仓库拥有多个工作目录，用来隔离不同实验。
- 理解什么是“还没提交的修改”、什么是“已经提交的本地历史”。
- 理解回滚时为什么“没推送”和“已经推送”不是同一件事。

## 本节产出物

- `learn_vibe_adv/git-playground/`
- `learn_vibe_adv/git-playground-worktree/`，练习结束后会被移除
- `learn_vibe_adv/notes/git-basics.md`

## 操作步骤

### 第 0 步：先安装或确认 Git

先启动 `Claude Code CLI`：

```bash
claude
```

在 `Claude Code CLI` 中输入：

```text
请先帮助我安装或确认 git，不要开始克隆任何项目。

我的要求：
1. 先判断我现在是否已经有 git；
2. 如果没有，请根据我的操作系统给出最稳妥的安装方式；
3. 只给终端里需要执行的命令和简短说明；
4. 安装完成后，让我运行 git --version 和一个平台对应的可执行路径检查命令做验证；
5. 如果命令报错，告诉我下一步怎么排查。

我当前系统是：<学生自行填写 macOS / Windows / Linux>
```

回到终端，执行 Claude 给出的安装或验证命令。安装完成后，再执行：

```bash
git --version
which git
```

如果你用的是 Windows，可以把 `which git` 换成 Claude 给出的等价路径检查方式。只有当 `git --version` 能成功运行时，才继续下一步。

### 第 1 步：创建本地 Git 练习仓库

在终端执行：

```bash
cd learn_vibe_adv
mkdir -p git-playground
cd git-playground
git init
git branch -M main
git status
```

这一节不要直接在真实项目上学 Git。先用一个不会影响主项目的 git 练习目录跑通。

### 第 2 步：理解 Git 在本地记录什么

把 Git 先理解成“给整个项目做可回看的存档”，而不是一堆命令。

你正在编辑的文件在 **工作区**。  
你明确标记“这些要进下一次提交”的修改在 **暂存区**。  
你真正保存下来的历史在 **本地仓库**。

可以先用这个最小模型记住：

```text
编辑文件 -> git add -> git commit
工作区   -> 暂存区  -> 本地仓库
```

你后面如果遇到“为什么文件改了但提交里没它”“为什么我刚才的修改不见了”，基本都能回到这三个区域来定位。

### 第 3 步：做第一次修改并看懂 status

在终端执行：

```bash
touch notes.txt
git status
```

你应该看到 Git 把 `notes.txt` 识别成未跟踪文件。它的意思不是“文件坏了”，而是“这个文件存在，但还没进入 Git 历史”。

然后在终端执行：

```bash
git add notes.txt
git status
```

这次再看 `git status`，重点观察：`notes.txt` 已经从“未跟踪”变成“准备提交”。

### 第 4 步：做第一次 commit

在终端执行：

```bash
git commit -m "chore: add first playground note"
git log --oneline
```

这一步的重点不是死记语法，而是理解：

- `git add` 是把修改放进暂存区；
- `git commit` 是真的写进本地历史；
- `git log --oneline` 是你回看本地存档列表的入口。

### 第 5 步：看懂 diff

在终端执行：

```bash
printf '\nsecond line\n' >> notes.txt
git diff
git status
```

这时你应该能看到：

- `git diff` 告诉你“和上一次提交相比，当前工作区改了什么”；
- `git status` 告诉你“这些变化现在还在工作区，没有进入下一次提交”。

然后继续执行：

```bash
git add notes.txt
git diff
git diff --cached
git status
```

这里最重要的观察是：

- `git diff` 变少了，甚至可能空了，因为工作区和暂存区一致；
- `git diff --cached` 会显示“已经放到暂存区、准备提交的内容”。

### 第 6 步：再提交一次，并理解 commit 粒度

在终端执行：

```bash
git commit -m "docs: add second playground note line"
git log --oneline
```

每次 commit 最好对应一件能说清楚的小事。不要攒一整天修改再一次性提交，否则以后想回退或定位问题会非常难受。

### 第 7 步：用 worktree 练习隔离实验

`git worktree` 的意思是：同一个本地仓库，可以同时有多个工作目录。每个工作目录通常切到不同 branch，这样你可以在一个目录里做实验，在另一个目录里保持原来的上下文。

这对 AI 辅助开发很有用：一个 Claude Code 会话可以在一个 worktree 里做 bugfix，另一个会话可以在另一个 worktree 里做 UI 调整，文件不会混在同一个目录里。

先在当前 `git-playground/` 里确认状态是干净的：

```bash
git status
git branch
```

然后创建一个实验分支：

```bash
git switch -c experiment/worktree-note
git status
```

回到 `main`，再从实验分支创建一个兄弟 worktree：

```bash
git switch main
git worktree add ../git-playground-worktree experiment/worktree-note
git worktree list
```

现在你有两个目录：

```text
learn_vibe_adv/git-playground/           main
learn_vibe_adv/git-playground-worktree/  experiment/worktree-note
```

进入 worktree，做一次只属于实验分支的小修改：

```bash
cd ../git-playground-worktree
printf '\nworktree experiment line\n' >> notes.txt
git status
git diff
git add notes.txt
git commit -m "docs: add worktree experiment note"
git log --oneline
```

再回到原来的目录，观察两个工作目录没有混在一起：

```bash
cd ../git-playground
git status
git branch
git log --oneline
git worktree list
```

你会看到：原目录仍在 `main`，worktree 目录在 `experiment/worktree-note`。实验分支上的 commit 不会自动出现在 `main` 的文件内容里。它还在同一个本地仓库里，只是被另一个 branch 承载。

练习完成后，先确认 worktree 目录是干净的，再移除它：

```bash
cd ../git-playground-worktree
git status
cd ../git-playground
git worktree remove ../git-playground-worktree
git worktree list
```

这一节只要求你理解 worktree 的用途，不要求你合并分支。记住这句话就够了：

```text
clone 是从另一个仓库复制一份仓库；worktree 是给同一个本地仓库增加一个工作目录。
```

### 第 8 步：理解本地回滚，不急着背命令

这一节先理解场景，不要求你把所有回滚命令背下来。

常见情况只有三类：

1. 改坏了，但**还没 commit**：
   - 这时你丢掉的是工作区草稿。
2. 已经 commit，但**还没 push 到远程**：
   - 这时可以重写本地历史，影响只在你自己电脑上。
3. 已经 push 到远程：
   - 这时不要随便改写公共历史，通常更适合用 `revert` 生成一个“反向提交”。

也就是说，`reset` 和 `revert` 的区别，先不要背成命令题，而要先理解成：

```text
reset 更像“把本地历史指针移回去”
revert 更像“新增一个提交，把之前那次提交抵消掉”
```

下一节开始讲 Remote GitHub 时，这个差异会更容易看懂。

### 第 9 步：让 Claude Code 帮你解释你刚做的事

在 `Claude Code CLI` 中输入：

```text
请只根据我刚才在 git-playground 里做的操作，帮我解释：
1. 工作区、暂存区、本地仓库分别是什么；
2. git status、git diff、git diff --cached、git log --oneline 分别在看什么；
3. git worktree 是什么，为什么它适合隔离实验；
4. worktree 和 clone 的区别；
5. 什么叫“还没推送的本地提交”；
6. 为什么 pushed commit 和 unpushed commit 的回滚思路不同。

不要修改文件，不要创建远程仓库。
```

### 第 10 步：写本节笔记

在 `Claude Code CLI` 中输入：

```text
请创建 ../notes/git-basics.md。

要求：
1. 只创建或修改 ../notes/git-basics.md；
2. 不修改 git-playground 里的文件；
3. 用中文写清：
   - Git 解决什么问题；
   - 工作区 / 暂存区 / 本地仓库；
   - git status / add / commit / log / diff 的作用；
   - git worktree 的作用、练习过的 worktree 命令、它和 clone 的区别；
   - 为什么 worktree 可以帮助 AI 辅助开发时隔离不同实验；
   - reset 和 revert 的场景区别；
4. 如果有不确定的地方，请标注“待验证”，不要编造。
```

## 验收标准

在终端运行或检查：

```bash
cd learn_vibe_adv
git --version
cat notes/git-basics.md
cd git-playground
git log --oneline
git status
git worktree list
```

通过标准：

- `git` 已安装，并且终端能正常运行 `git --version`。
- 你已经在 `git-playground/` 里跑通过 `git init`、`git status`、`git add`、`git commit`、`git log --oneline`、`git diff`。
- 你已经跑通过 `git worktree add`、`git worktree list`、`git worktree remove`。
- 你能用自己的话解释工作区、暂存区、本地仓库。
- 你能解释 worktree 是同一个本地仓库的第二个工作目录，不是从 GitHub 重新 clone 一份。
- 你知道 worktree 为什么适合把 bugfix、实验、AI 代理任务分开。
- 你知道“没提交的修改”“本地提交”“已经推送的提交”不是同一回事。
- `notes/git-basics.md` 不是只抄命令，而是写清了 Git 的本地心智模型和 worktree 的隔离用途。

需要停下处理：

- 如果 `git --version` 仍然失败，不要进入下一节。
- 如果你还分不清 `git diff` 和 `git status` 在看什么，先让 Claude 再解释一轮。
- 如果 `git worktree remove` 失败，先回到 worktree 目录运行 `git status`，确认没有未提交修改，再让 Claude 根据完整输出解释。
- 如果你已经开始研究 GitHub 远程仓库、SSH 或 push/pull，先停一下；那是 `03` 的内容。

## 常见卡点

- 以为 Git 只是“把代码上传到 GitHub”的工具，忽略了它首先是本地版本历史。
- 把 `git add` 误解成“已经保存进历史”。
- 看到 `git status` 输出就紧张，其实它只是状态说明。
- 只会 commit，不会看 `git diff`，导致不知道自己到底改了什么。
- 先背命令，不先理解“工作区 -> 暂存区 -> 本地仓库”的流转。
- 把 worktree 误解成 clone。worktree 还是同一个本地仓库，只是多了一个工作目录。
