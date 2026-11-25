---
title: Git 与 GitHub 核心概念大串讲
source: https://www.bilibili.com/video/BV1ySLc6QEcB/
author: 技术爬爬虾
created: 2026-11-24
tags:
  - Git
  - GitHub
  - 版本控制
  - 教程笔记
---

# Git 与 GitHub 核心概念大串讲

> [!summary]
> **Git 负责本地版本控制，GitHub 负责托管远端仓库和多人协作。**
>
> 只需要理解四件事：**提交、分支、分区、远端**，就能应对绝大多数日常开发。即使让 AI 或图形工具代操作，也要先知道这些操作在做什么。

视频来源：[Git+Github核心概念大串讲，从零到一全攻
略，详细实战教程](https://www.bilibili.com/video/BV1ySLc6QEcB/)

---

# 第一部分：视频提纲与讲解

> [!info] 内容分区
> - **第一部分：视频提纲与讲解**：从“0. 一张图记住 Git”到“14. 用 AI 操作 Git 的正确方式”。
> - **第二部分：我的 Q&A**：位于文末，使用 Q1、Q2……编号，不延续视频章节编号。

---

## 0. 一张图记住 Git

```text
工作区 Working Directory
        │ git add
        ▼
暂存区 Staging Area
        │ git commit
        ▼
本地仓库 Local Repository
        │ git push
        ▼
远程仓库 Remote Repository
        ▲
        │ git pull / fetch + merge
        │
工作区与本地仓库
```

| 区域 | 作用 | 常见操作 |
| --- | --- | --- |
| 工作区 | 正在编辑的项目文件 | 修改、删除、新建文件 |
| 暂存区 | 下一次提交的候选内容 | `git add` |
| 本地仓库 | 保存每次提交形成的版本历史 | `git commit` |
| 远程仓库 | 在 GitHub 等平台备份、分享和协作 | `git push` / `git pull` |

---

## 1. Git 与 GitHub 是什么

### Git

- 开源、免费的**分布式版本控制软件**。
- 记录文件的修改历史，可以回退、比较、合并不同版本。
- 每次提交都会形成一条历史记录，整个仓库可追溯。
- 初始化仓库时会生成隐藏的 `.git` 文件夹，版本信息都在里面。

### GitHub

- 全球最大的代码托管与协作平台。
- 可以托管公开或私有仓库，并支持 Issue、Pull Request、代码审查、Actions 等协作功能。
- **Git 是工具，GitHub 是提供远程仓库与协作服务的网站。**

### 常见概念

- **Commit（提交）**：一次版本快照。
- **Branch（分支）**：一条独立的开发路线。
- **HEAD**：当前所在的提交位置。
- **Remote（远程仓库）**：位于服务器上的仓库，例如 GitHub 仓库。
- **`.gitignore`**：规定哪些文件不应被 Git 跟踪。

---

## 2. 安装与初始化

### 检查 Git

```bash
git --version
```

### 首次配置身份

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
```

### 初始化仓库

```bash
git init
```

执行后会生成 `.git` 文件夹。删除这个文件夹会取消该目录的 Git 管理，因此不要随意删除。

### `.gitignore`

以下内容通常不应提交：

```gitignore
.env
*.key
*.pem
node_modules/
__pycache__/
.venv/
.DS_Store
```

> [!warning] 不要把密钥传到 GitHub
> `.env`、API Key、访问令牌、密码和私钥应加入 `.gitignore`。提交前使用 `git diff --staged` 或 `git status` 再检查一次。

---

## 3. 提交：保存一个可回退的版本

### 最常用的本地流程

```bash
git status
git diff
git add 文件名
git diff --staged
git commit -m "说明这次修改了什么"
```

一次添加全部修改：

```bash
git add .
```

查看历史：

```bash
git log --oneline --graph --decorate
```

### Commit ID

每次提交都有一个唯一哈希值：

- 短 ID 通常是前 7～8 位。
- 完整 ID 通常是 40 位。
- 短 ID 和完整 ID 都能定位同一次提交。

### 提交粒度的建议

- 每完成一个明确的小功能就提交一次。
- 提交信息写“做了什么”，例如 `feat: add login page`。
- 不要把无关修改塞进同一个提交。
- AI 修改代码后，先检查 `git diff`，确认无误再提交。

---

## 4. 撤销：Restore、Reset、Revert

| 操作 | 作用 | 适用场景 | 风险 |
| --- | --- | --- | --- |
| Restore | 丢弃尚未提交的工作区修改 | 改错了，且不需要保留 | 修改会直接消失 |
| Reset | 把分支指针强制退回到某次提交 | 仅限个人分支、尚未共享的历史 | 可能丢失后续提交 |
| Revert | 新建一个“反向提交”抵消指定提交 | 已共享或多分支协作的历史 | 最安全，不篡改原有历史 |

### 丢弃未提交修改

丢弃某个文件的改动：

```bash
git restore 文件名
```

### 撤销某次已经提交的修改

```bash
git revert <commit-id>
```

`revert` 会生成一条新提交，不会删除原历史。

### 强制回退

```bash
git reset --hard <commit-id>
```

> [!danger] 谨慎使用
> `reset --hard` 会丢弃工作区和暂存区中尚未提交的内容。多人协作分支不要把已推送的历史直接 `reset` 后再强制推送。

---

## 5. 分支：在不影响主干的情况下开发

分支本质上只是指向某次提交的**指针**，创建分支不会复制整个项目。

```bash
git branch
git switch -c feature/login
git switch main
git merge feature/login
```

也可以使用较旧但仍常见的写法：

```bash
git checkout -b feature/login
git checkout main
```

### 推荐流程

```bash
git switch main
git pull
git switch -c feature/功能名

# 修改、检查、提交
git add .
git commit -m "feat: 完成某功能"

# 回到主干并合并
git switch main
git merge feature/功能名
```

### HEAD

- 在 `main` 分支上：`HEAD` 指向 `main` 的最新提交。
- 切换到 `feature` 分支：`HEAD` 指向该分支的最新提交。
- 直接检出某个历史提交时，会进入 **Detached HEAD（分离头指针）**。
- 分离头指针状态适合查看旧代码，不适合直接长期开发；若要修改，应先基于该提交创建新分支。

---

## 6. Worktree：同时打开多个分支

普通切换分支只能在一个目录中工作；`git worktree` 可以把另一个分支检出到新的文件夹，让多个任务并行进行。

```bash
git worktree add ../project-feature feature/login
git worktree list
```

适合：

- 主目录继续开发；
- 另一个目录修复紧急 Bug；
- 让多个 AI 任务在不同工作树中并行，完成后分别合并。

删除工作树时要使用 Git 命令：

```bash
git worktree remove ../project-feature
```

---

## 7. 合并冲突

当两个分支修改了**同一文件的同一位置**时，Git 无法自动决定保留哪一份，就会产生冲突。

```text
<<<<<<< HEAD
当前分支的内容
=======
待合并分支的内容
>>>>>>> feature/xxx
```

处理方式：

1. 打开冲突文件，理解两边修改。
2. 选择保留当前内容、对方内容，或人工组合两边内容。
3. 删除冲突标记。
4. 保存文件并暂存。
5. 完成提交。

```bash
git add 冲突文件
git commit
```

> [!tip] 核心原则
> 冲突不是“报错坏了”，而是 Git 在等你做内容决策。不要让工具随意删掉冲突内容。

---

## 8. 远程仓库：Clone、Push、Pull

### 克隆已有仓库

```bash
git clone <仓库地址>
```

### 查看远程地址

```bash
git remote -v
```

### 添加远程仓库

```bash
git remote add origin <仓库地址>
```

### 首次推送本地分支

```bash
git push -u origin main
```

`-u` 会建立本地 `main` 与远程 `origin/main` 的跟踪关系，之后通常直接执行：

```bash
git push
```

### 拉取远程更新

```bash
git pull
```

`git pull` 大致相当于：

```bash
git fetch
git merge
```

`origin/main` 表示远程仓库 `origin` 的 `main` 分支，`main` 表示本地分支。同步后，两者通常指向同一提交。

---

## 9. GitHub 页面速览

仓库地址的一般形式：

```text
github.com/用户名/仓库名
```

| 区域 | 用途 |
| --- | --- |
| Code | 浏览源码，复制 HTTPS/SSH 克隆地址，下载 ZIP |
| README | 项目介绍和使用说明 |
| Issues | 报告 Bug、提出功能建议、搜索已有问题 |
| Pull requests | 提交和审查代码合并请求 |
| Actions | 运行自动化测试、构建和部署 |
| Releases | 下载已发布版本 |
| About | 项目简介、标签、许可证等 |
| Star | 收藏并表达喜欢，反映项目热度 |
| Fork | 把别人的仓库复制到自己账号下 |

### 常用快捷键

| 快捷键 | 功能 |
| --- | --- |
| `/` | 打开 GitHub 搜索 |
| `t` | 搜索仓库中的文件 |
| `l` | 跳转到指定行 |
| `?` | 查看快捷键帮助 |
| `.` | 在网页版 VS Code 中打开仓库 |

选中某行后，可以：

- **Copy permalink**：复制永久链接。
- **Git blame**：查看该行最近一次修改及提交者。

---

## 10. 多人协作的三种方式

### 方式一：仓库所有者直接开发

```text
Clone → 创建分支 → 修改 → Commit → Push → 合并到 main
```

### 方式二：Fork + Pull Request（开源项目常用）

```text
Fork 上游仓库
    ↓
Clone 自己账号下的 Fork
    ↓
创建功能分支并提交
    ↓
Push 到自己的仓库
    ↓
向上游仓库创建 Pull Request
    ↓
代码审查、修改、合并
```

PR 的合并方向应是：

```text
你的仓库分支 → 上游仓库的 main
```

上游仓库更新后，可以配置 `upstream` 并同步：

```bash
git remote add upstream <上游仓库地址>
git fetch upstream
git switch main
git merge upstream/main
git push origin main
```

### 方式三：Collaborator 协作者

仓库管理员在：

```text
Settings → Collaborators → Add people
```

邀请协作者后，对方可以直接推送，但仍建议采用“功能分支 + PR + 代码审查”的流程。

> [!important] 权限区别
> 陌生人通常不能直接向别人的仓库 `push`，因此需要 Fork + PR；协作者拥有直接推送权限，但也不应直接覆盖主干。

---

## 11. 进阶操作

### Cherry-pick：只拿某一次提交

```bash
git switch main
git cherry-pick <commit-id>
```

只把指定提交应用到当前分支，而不是合并一整个分支。

### Stash：临时保存未完成修改

```bash
git stash push -m "未完成的功能"
git stash list
git stash pop
```

适合需要临时切分支处理其他事情，但当前修改还不想提交的情况。

### Rebase：把当前分支接到另一分支最新提交之后

```bash
git switch feature/login
git rebase main
```

- 优点是提交历史更线性、干净。
- 缺点是会改写提交 ID。
- **不要对已经共享给其他人的公共分支随意 rebase。**
- 已经推送的个人分支在 rebase 后通常需要：

```bash
git push --force-with-lease
```

`--force-with-lease` 比直接 `--force` 更安全，但使用前仍要确认分支只有自己在使用。

---

## 12. 日常最稳妥的工作流

```bash
# 1. 更新主干
git switch main
git pull

# 2. 创建功能分支
git switch -c feature/功能名

# 3. 开发并检查
git status
git diff

# 4. 暂存并复查
git add .
git diff --staged

# 5. 提交
git commit -m "feat: 完成某功能"

# 6. 推送
git push -u origin feature/功能名

# 7. 在 GitHub 创建 PR，审查后合并
```

---

## 13. 最容易出错的地方

1. **把 API Key、`.env` 或密码提交到 GitHub。**
2. 在错误分支上直接修改，导致功能改动混入主干。
3. 没看 `git diff` 就让 AI 自动提交。
4. 用 `git reset --hard` 时忘记未提交内容会丢失。
5. 对公共分支执行 `rebase` 后强制推送，覆盖别人的提交。
6. 冲突时直接删除冲突标记，没有确认两边的业务逻辑。
7. 忘记 `git pull`，导致本地与远程分叉。
8. 提交信息只写“修改”“更新”，以后无法判断版本内容。

---

## 14. 用 AI 操作 Git 的正确方式

可以让 AI 执行命令，但要把目标说清楚：

```text
先运行 git status 和 git diff。
只提交与登录功能有关的文件。
提交前检查是否包含密钥、.env、缓存或无关文件。
提交信息使用 feat: add login validation。
不要执行 reset --hard 或 force push。
```

回顾：

- **Commit**：保存一个版本。
- **Branch**：隔离不同开发任务。
- **Merge / PR**：把完成的改动合回主干。
- **Remote**：备份、共享与多人协作。
- **Reset / Revert / Rebase**：改变历史时要格外谨慎。

---

## 视频章节目录

| 时间 | 章节 |
| --- | --- |
| 00:00 | 引言 |
| 00:50 | Git 与 GitHub 是什么 |
| 03:57 | 实战准备 |
| 08:29 | Commit 提交 |
| 14:17 | Branch 分支 |
| 17:50 | WorkTree 工作树 |
| 19:41 | Merge Conflict 合并冲突 |
| 20:50 | Git 分区 |
| 23:25 | 远端仓库实战 |
| 27:32 | GitHub 入门 |
| 32:29 | GitHub 多人协作 |



---

# 第二部分：我的 Q&A

> [!question] 这一部分是我自己的问题
> 下面的内容不是视频原提纲，而是我在学习 Git、GitHub、Obsidian 和 Markdown 时产生的疑问及补充解释。问题使用独立的 `Q1、Q2……` 编号，不延续上面的视频章节编号。

## Q1. `git init` 到底是干什么的？

`git init` 是把普通文件夹初始化成 Git 仓库。它会在当前目录创建隐藏的 `.git` 文件夹，用来保存配置、暂存区、分支引用和提交历史。

```text
普通文件夹
   │ git init
   ▼
普通文件夹 + .git/
   │ git add + git commit
   ▼
有版本历史的 Git 仓库
```

它不会自动加入暂存区，不会创建第一次 commit，不会上传到 GitHub，也不会修改文件内容。

```bash
cd 项目目录
git init
git status
```

通常只在项目根目录初始化一次。不要在用户主目录、整个桌面或整个 Obsidian Vault 上随意执行。

## Q2. Git Bash 怎么知道当前是哪个仓库？

Git Bash 不会读取某个 IDE 的工作区。它只看自己的当前目录，并从当前目录逐级向父目录寻找最近的 `.git`。

```bash
pwd
git status
git -C "项目路径" status
```

- `pwd`：显示 Git Bash 当前目录。
- `-C`：Git 的参数，表示先进入指定目录再执行命令，不是盘符。
- 同时打开多少资源管理器或 IDE，都不会改变 Git Bash 的判断。

## Q3. `status`、`diff`、`add`、`diff --staged`、`commit` 分别做什么？

```bash
git status
git diff
git add 文件名
git diff --staged
git commit -m "说明这次修改了什么"
```

- `git status`：查看分支、未跟踪文件、修改和暂存状态。
- `git diff`：查看“工作区”和“暂存区”之间的未暂存修改。
- `git add`：把文件当前内容放入暂存区，不是上传，也不是移动文件。
- `git diff --staged`：查看暂存区与当前提交之间的差异，即下一次将提交什么。
- `git commit`：根据暂存区创建本地提交，并返回 commit ID。

缺少这些检查会增加误提交风险；不执行 `add`，暂存区没有新内容；不执行 `commit`，修改不会形成版本历史。

## Q4. `add`、`commit`、`push` 有什么区别？

```text
工作区
  │ git add
  ▼
暂存区
  │ git commit
  ▼
本地仓库
  │ git push
  ▼
远程仓库
```

- `add`：选择下一次提交的内容。
- `commit`：在本地创建版本记录。
- `push`：把本地提交上传到远程仓库。

所以 commit 不是上传云端，`-m` 也不是简单标签，而是这次提交的说明。

## Q5. 每次 commit 都会复制整个项目吗？

逻辑上，一次 commit 像项目当时的一份完整快照；物理上，Git 通过对象哈希复用未变化的内容，不会把每个文件都重新复制一遍。

例如 A 从 v1 改成 v2：

- A 的旧内容仍属于旧 commit；
- A 的新内容进入新 commit；
- 未变化的文件继续复用原对象；
- 每次 commit 都会得到新的 ID。

如果已提交的 A 改名为 B，Git 通常能识别 rename；旧提交里的 A 不会消失。如果只是工作区改名但尚未提交，则表现为删除 A、新增 B。

## Q6. `restore` 和 `reset` 分别撤销什么？

`git restore 文件名` 默认撤销工作区中尚未暂存的修改，让文件回到暂存区版本。它不是撤销“上一次 commit”。

`git restore --staged 文件名` 取消暂存，把文件从暂存区移回工作区，不改文件内容。

`git reset` 移动当前分支所指向的 commit，并可能同时影响暂存区和工作区：

- `--soft`：只移动 HEAD，改动保留在暂存区；
- `--mixed`：移动 HEAD 并取消暂存，改动保留在工作区；
- `--hard`：同时重置暂存区和工作区，未保存的修改可能丢失。

执行 `--hard` 前必须先检查 `git status`。

## Q7. 切分支、pull 和 HEAD 是怎么工作的？

- branch 是一条独立开发路线。
- HEAD 是指向当前位置的指针，通常指向当前分支。
- `git switch 分支名` 会移动 HEAD，并把工作区切换成目标分支对应的版本。
- `git pull` 通常等于 `git fetch` 加 `git merge`。
- `fetch` 先把远程新提交取到本地远程跟踪引用，不直接修改当前分支。
- `merge` 再把这些提交合入当前分支。
- `origin/main` 表示本地目前所知道的远程 main，不等同于本地 main。

## Q8. Worktree 是什么，为什么不等于克隆？

Worktree 是共享同一套 `.git` 对象库和历史、但拥有独立工作区与暂存区的额外目录。它适合同时打开多个分支，而不是复制整个远程仓库。

```bash
git worktree add ../project-feature feature/login
git worktree list
```

第一条命令在相邻目录创建额外工作区；第二条命令列出所有相关 worktree。同一个普通分支不应同时检出到两个 worktree。

## Q9. `upstream` 那组命令是什么意思？

```bash
git remote add upstream <上游仓库地址>
git fetch upstream
git switch main
git merge upstream/main
git push origin main
```

- `upstream`：给自己指定的上游远程仓库起的名字；
- `fetch upstream`：取得上游新提交，不直接修改当前分支；
- `switch main`：切换到本地 main；
- `merge upstream/main`：把上游 main 合入本地 main；
- `push origin main`：把本地 main 推到自己的远程仓库。

## Q10. Stash 存在哪里，和暂存区有什么区别？

Stash 保存的是尚未提交的临时修改，位置在 `.git` 中的 stash 引用与 reflog 里；`git add` 使用的是暂存区 Index。两者不是同一个东西。

```bash
git stash push -m "临时说明"
git stash list
git stash pop
git stash apply
git stash drop
```

`pop` 取出并删除；`apply` 取出但保留；`drop` 删除。Stash 不会因切换分支、commit、push 或重启电脑自动消失，但可能被 Git 维护机制清理，不适合作为长期备份。

## Q11. Obsidian 中 Markdown 源文件和阅读视图为什么不同？

- `.md` 是普通文本文件，Obsidian 负责读取和渲染。
- 编辑/源码模式显示 Markdown 原文；阅读模式显示渲染结果。
- 顶部的大标题通常是文件名或笔记标题区域，不参与正文 Markdown 渲染。
- 在源码模式写 `# 标题`，阅读模式会显示为一级标题。
- Windows 资源管理器如果看不到扩展名，需要打开“查看”中的“文件扩展名”。

## Q12. 日常操作固定检查顺序

1. 用 `pwd` 确认当前目录。
2. 用 `git status` 确认仓库与文件状态。
3. 用 `git diff` 检查未暂存修改。
4. 用 `git add` 选择本次要提交的内容。
5. 用 `git diff --staged` 检查真正将提交的内容。
6. 用 `git commit -m` 创建本地版本。
7. 需要共享时再用 `git push`。
8. 涉及 `reset --hard`、`push --force` 时先停下来确认风险。

---

## 本部分小结

Git 的核心链路是：

```text
工作区 → git add → 暂存区 → git commit → 本地仓库 → git push → 远程仓库
```

先看清当前位置和文件状态，再暂存、复查、提交，最后才推送。
