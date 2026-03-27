---
title: "Git 常用简写说明书"
published: 2026-03-23
pinned: true
description: "Git 常用简写说明书"
tags: [Git, 简写]
category: Git
licenseName: "CC BY-NC-ND 4.0"
author: 我叫石头鱼
draft: false
comment: true
image: "./cover.jpeg"
permalink: "git-alias"
---

这份文档只讲你平时最常用的这几个：

- `gst`
- `gco`
- `grb`
- `gm`
- `gp`
- `gpf`
- `gl`
- `grh`

目标不是“背命令”，而是让你真的知道：

1. 这个缩写到底等于什么命令
2. 它到底会对仓库做什么
3. 常见参数分别是什么意思
4. 什么时候该用，什么时候别乱用
5. 实战里一般怎么连起来用
6. 哪些地方最容易翻车

---

# 1. 先给你一个总览

| 缩写  | 原命令                        | 核心动作                    | 危险等级 | 常用指数 |
| ----- | ----------------------------- | --------------------------- | -------- | -------- |
| `gst` | `git status`                  | 看状态                      | 低       | ★★★★★    |
| `gco` | `git checkout`                | 切分支 / 恢复文件（旧方式） | 中       | ★★★★☆    |
| `grb` | `git rebase`                  | 变基、整理历史              | 中高     | ★★★☆☆    |
| `gm`  | `git merge`                   | 合并分支                    | 中       | ★★★★☆    |
| `gl`  | `git pull`                    | 拉取并合并远程更新          | 中       | ★★★★★    |
| `gp`  | `git push`                    | 推送到远程                  | 中       | ★★★★★    |
| `gpf` | `git push --force-with-lease` | 更安全的强推                | 高       | ★★☆☆☆    |
| `grh` | `git reset`                   | 重置提交/暂存状态           | 中高     | ★★★☆☆    |

## 这张表怎么理解

### 危险等级

- **低**：几乎不会改坏东西
- **中**：会改变分支状态、远程状态或合并历史，需要看清楚再敲
- **高**：可能覆盖历史、丢改动、影响队友

### 常用指数

这是按**日常团队开发里的主观实用度**打的，不是官方排名。

- ★★★★★：几乎天天会用
- ★★★★☆：非常常用
- ★★★☆☆：比较常用，但不是每次都用
- ★★☆☆☆：场景性很强

---

# 2. 先弄懂 4 个基本概念

在看这些命令前，你先记住下面四个位置：

## 2.1 工作区（Working Tree）

你正在编辑的文件。

## 2.2 暂存区（Staging Area）

你打算放进下一次提交的改动。

## 2.3 本地提交历史（Commit History）

你本地已经提交过的历史记录。

## 2.4 远程仓库（Remote）

GitHub / GitLab 上的仓库内容。

很多 Git 命令，本质上就是在这四个位置之间“搬东西”或者“对齐状态”。

---

# 3. `gst` —— 看当前到底是什么状态

## 原命令

```bash
git status
```

## 作用

这是 Git 里最重要的查看命令之一。

它不会改任何东西，只负责告诉你：

- 你现在在哪个分支
- 当前分支和远程比，是领先还是落后
- 哪些文件已经暂存
- 哪些文件只是修改了、还没暂存
- 哪些文件是新文件
- 有没有冲突

## 常用指数

**★★★★★**

## 危险等级

**低**

## 你会看到什么

典型输出会包括：

- `On branch feat/login`
- `Your branch is ahead of 'origin/feat/login' by 1 commit`
- `Changes to be committed`
- `Changes not staged for commit`
- `Untracked files`

## 常见使用场景

### 场景 1：提交前先确认

你改完代码之后，不要急着提交，先 `gst` 看一眼。

### 场景 2：切分支前确认工作区是否干净

切分支、rebase、merge 前，先看有没有没处理的改动。

### 场景 3：推送前确认有没有漏提交

你以为自己都提交完了，`gst` 一看还有没 add 的东西。

## 常见搭配

```bash
gst
gco main
gst
gl
gst
```

## 实战例子

### 例子 A：开发完准备提交

```bash
gst
```

你看到：

- `modified: src/login.ts`
- `modified: src/api.ts`

说明你改了两个文件，但还没暂存。

### 例子 B：你准备推送

```bash
gst
```

看到：

- `Your branch is ahead of 'origin/feat/login' by 2 commits`

这就说明你本地有 2 个提交还没推上去。

## 提醒

### 1. `gst` 不会修任何问题

它只负责“告诉你发生了什么”。

### 2. 一切危险操作前都先跑一次 `gst`

尤其是：

- `grb`
- `gm`
- `gl`
- `gpf`
- `grh`

### 3. 小白最容易忽略的一点

很多人“以为自己知道当前状态”，结果其实不知道。`gst` 就是防止这种自信翻车的第一道保险。

---

# 4. `gco` —— 切分支，或者恢复文件（旧时代万能刀）

## 原命令

```bash
git checkout
```

## 作用

`checkout` 是 Git 早期特别常用的一个命令，功能很多，主要两大类：

1. **切换分支**
2. **把文件恢复到某个版本**

也正因为它功能太多，所以后来 Git 才拆出了更清晰的：

- `git switch`：专门切分支
- `git restore`：专门恢复文件

## 常用指数

**★★★★☆**

## 危险等级

**中**

## 最常见的两种用法

### 用法 1：切换分支

```bash
gco main
```

意思：切到 `main` 分支。

### 用法 2：恢复某个文件

```bash
gco -- src/login.ts
```

意思：把这个文件恢复成当前提交里的版本，丢掉你在工作区的改动。

这第二种用法很危险。

## 常见参数

### `gco <branch>`

切换到某个已存在分支。

例子：

```bash
gco develop
```

### `gco -b <new-branch>`

创建并切换到新分支。

例子：

```bash
gco -b feat/user-center
```

不过在 oh-my-zsh 里通常更常写成：

```bash
gcb feat/user-center
```

### `gco -- <file>`

恢复单个文件到当前提交版本。

例子：

```bash
gco -- package.json
```

### `gco <commit> -- <file>`

从某个历史提交里拿回一个文件版本。

例子：

```bash
gco a1b2c3d -- src/config.ts
```

## 应用场景

### 场景 1：切到主分支拉最新代码

```bash
gco main
gl
```

### 场景 2：切回自己的功能分支继续开发

```bash
gco feat/login-refactor
```

### 场景 3：某个文件改乱了，想撤回

```bash
gco -- src/login.ts
```

## 实战例子

### 例子 A：你要开始新功能

```bash
gco main
gl
gco -b feat/payment-page
```

意思是：

1. 切到主分支
2. 拉最新代码
3. 基于最新主分支开一个新功能分支

### 例子 B：你误改了一个文件

```bash
gst
gco -- src/mock.ts
gst
```

恢复后再看状态，就会发现这个文件不再显示修改。

## 提醒

### 1. `gco -- 文件名` 会直接丢掉你没提交的修改

如果你还想留着，就别直接用。

### 2. 现在新手更建议学 `git switch` 和 `git restore`

因为语义更清楚，不容易混。

### 3. 切分支失败时，通常不是 Git 坏了

大概率是：

- 你有未提交改动
- 目标分支不存在
- 你当前改动会和目标分支冲突

---

# 5. `grb` —— 变基，把你的提交“重新排队”

## 原命令

```bash
git rebase
```

## 作用

`rebase` 的核心是：

> 把当前分支上的提交，重新接到另一个基底后面。

你可以把它理解成：

“我这条分支原来是从旧的主分支上长出来的，现在我想把它挪到最新的主分支后面。”

## 常用指数

**★★★☆☆**

## 危险等级

**中高**

## 为什么要用它

主要两个原因：

### 1. 同步主分支最新代码

你的功能分支开发了一半，主分支已经前进了。你可以把自己的分支 rebase 到最新主分支上。

### 2. 整理提交历史

把历史弄得更直、更干净，方便 review。

## 最常见用法

### 用法 1：把当前分支 rebase 到 main

```bash
grb main
```

### 用法 2：把当前分支 rebase 到最新远程主分支

```bash
grb origin/main
```

### 用法 3：交互式 rebase

```bash
grbi HEAD~3
```

意思：整理最近 3 个提交。

## 常见参数

### `grb <branch>`

把当前分支变基到某个分支上。

### `grbi`

对应：

```bash
git rebase --interactive
```

作用：交互式编辑提交历史。

### `grba`

对应：

```bash
git rebase --abort
```

作用：rebase 出问题了，放弃本次 rebase。

### `grbc`

对应：

```bash
git rebase --continue
```

作用：解决冲突后继续。

### `grbs`

对应：

```bash
git rebase --skip
```

作用：跳过当前这个冲突提交。

## 应用场景

### 场景 1：提 PR 前同步主分支

```bash
gco main
gl
gco feat/login
grb main
```

### 场景 2：让提交历史更干净

你做了 5 个零碎提交，准备合并前用交互式 rebase 整理。

### 场景 3：团队要求“不要 merge main，统一 rebase main”

很多团队会有这种规范。

## 实战例子

### 例子 A：同步主分支最新提交

你在 `feat/login` 分支上开发两天了，这两天 `main` 又进了很多提交。

你做：

```bash
gco main
gl
gco feat/login
grb main
```

结果：

- 你的功能分支看起来像是基于最新 `main` 开始写的
- 历史通常会比 merge 更直

### 例子 B：rebase 过程中冲突

```bash
grb main
```

报冲突了。

你需要：

1. 打开冲突文件解决冲突
2. `git add` 把冲突解决结果标记好
3. 运行：

```bash
grbc
```

如果你不想继续了：

```bash
grba
```

## 提醒

### 1. rebase 本质上是在“改写提交历史”

这意味着同一段改动会生成新的提交 ID。

### 2. 已经推送给团队、别人也在基于它工作的分支，不要轻易 rebase 后强推

否则别人会很痛苦。

### 3. 小白的基本原则

- **自己本地分支**：可以 rebase
- **团队共享分支**：慎重 rebase

### 4. rebase 不是更高级的 merge

它只是另一种整合历史的方法，没有绝对谁更高级。

---

# 6. `gm` —— 合并分支，把另一条线并进来

## 原命令

```bash
git merge
```

## 作用

`merge` 是把别的分支改动并入当前分支。

你可以理解成：

> “把那条开发线的成果接进我现在站的这条线上。”

## 常用指数

**★★★★☆**

## 危险等级

**中**

## 最常见用法

### 用法 1：把主分支合进当前功能分支

```bash
gm main
```

### 用法 2：把功能分支合进主分支

```bash
gco main
gm feat/login
```

## 常见参数

### `gm <branch>`

合并指定分支到当前分支。

### `gma`

对应：

```bash
git merge --abort
```

合并冲突时，放弃这次 merge。

### `gmc`

对应：

```bash
git merge --continue
```

解决冲突后继续。

### `gms`

对应：

```bash
git merge --squash
```

把对方分支的多个提交压成一个待提交改动。

### `gmff`

对应：

```bash
git merge --ff-only
```

只允许快进合并，不允许产生 merge commit。

## merge 和 rebase 的直观区别

### merge

- 不改已有提交历史
- 会把分支合并痕迹保留下来
- 更适合保留真实开发轨迹

### rebase

- 改写当前分支提交历史
- 历史更直
- 更适合整理后再合并

## 应用场景

### 场景 1：你想保留真实合并关系

那就用 merge。

### 场景 2：团队不要求线性历史

merge 很正常。

### 场景 3：把 `main` 合进功能分支解决兼容问题

```bash
gco feat/order
gm main
```

## 实战例子

### 例子 A：把主分支最新改动并进功能分支

```bash
gco main
gl
gco feat/payment
gm main
```

这样做完后，你的功能分支就包含主分支最新改动。

### 例子 B：merge 冲突处理

```bash
gm main
```

冲突了。

你需要：

1. 编辑冲突文件
2. `git add` 标记冲突已解决
3. 如果 Git 要求继续：

```bash
gmc
```

不想继续：

```bash
gma
```

## 提醒

### 1. merge 不等于安全无脑

它虽然不改历史，但照样会产生冲突。

### 2. 你当前站在哪个分支，非常重要

`gm xxx` 的意思是：**把 xxx 合进当前分支**。

很多人会把方向搞反。

### 3. 小白口诀

先 `gst`，再看自己站在哪个分支，再 merge。

---

# 7. `gl` —— 拉远程更新到本地

## 原命令

```bash
git pull
```

## 作用

`pull` 本质上约等于：

1. `git fetch`
2. 再把拉下来的东西整合进当前分支

默认整合方式通常是 **merge**。

所以你可以把 `gl` 理解成：

> “从远程把最新东西拿下来，并试着和我当前分支合到一起。”

## 常用指数

**★★★★★**

## 危险等级

**中**

## 最常见用法

### 用法 1：更新主分支

```bash
gco main
gl
```

### 用法 2：更新你当前分支对应的远程分支

```bash
gco feat/login
gl
```

## 常见参数

### `gl`

默认 pull。

### `gpr`

对应：

```bash
git pull --rebase
```

作用：拉取后用 rebase 方式整合，而不是 merge。

### `gpra`

对应：

```bash
git pull --rebase --autostash
```

作用：有未提交改动时，先自动 stash，再 pull rebase，最后恢复改动。

### `ggpull`

对应：

```bash
git pull origin 当前分支
```

更明确地拉当前分支的远程对应分支。

## 应用场景

### 场景 1：每天开工先更新主分支

```bash
gco main
gl
```

### 场景 2：切回老分支前先同步

```bash
gco feat/profile
gl
```

### 场景 3：你准备 rebase 前，先把主分支拉到最新

```bash
gco main
gl
gco feat/order
grb main
```

## 实战例子

### 例子 A：早上第一件事

```bash
gco main
gl
```

这是很典型的日常动作。

### 例子 B：pull 出现冲突

说明远程更新和你本地提交/改动发生了冲突，需要你手动解决。

## 提醒

### 1. `gl` 不是“只下载”

它是下载 + 整合。

如果你只想下载不合并，用的是：

```bash
gf
```

### 2. 工作区不干净时直接 `gl`，更容易冲突

先 `gst`。

### 3. 团队如果偏好线性历史，可能更推荐你用 `gpr`

也就是 pull 后 rebase。

---

# 8. `gp` —— 把本地提交推上远程

## 原命令

```bash
git push
```

## 作用

把你本地分支的提交上传到远程仓库。

这是“让别人看见你改动”的关键动作之一。

## 常用指数

**★★★★★**

## 危险等级

**中**

## 最常见用法

### 用法 1：把当前分支推上去

```bash
gp
```

前提是当前分支已经有 upstream（上游分支）配置。

### 用法 2：新分支第一次推送

通常你会用：

```bash
gpsup
```

意思是第一次推送并建立跟踪关系。

## 常见参数

### `gp`

普通推送。

### `gpd`

对应：

```bash
git push --dry-run
```

先演练，不真正推。

### `gpv`

对应：

```bash
git push --verbose
```

更详细输出。

### `ggp`

对应：

```bash
git push origin 当前分支
```

显式推当前分支到 `origin`。

## 应用场景

### 场景 1：本地提交后推上去备份

```bash
gp
```

### 场景 2：推上去让 CI 跑起来

很多团队 push 后自动触发构建、测试、部署检查。

### 场景 3：推上去开 PR

推送是开 PR 前的前置动作。

## 实战例子

### 例子 A：你刚做完一轮提交

```bash
gst
gp
```

### 例子 B：第一次推分支

```bash
gco -b feat/refactor-header
# 开发、提交以后
gpsup
```

## 提醒

### 1. `gp` 只能推“提交”，不能推你还没 commit 的工作区改动

很多小白以为 push 会把所有改动一起传上去，不会。

### 2. push 失败很常见，不一定是你出错

常见原因：

- 远程有新提交，你本地落后了
- 没有权限
- 当前分支没配置 upstream
- 钩子检查没过

### 3. 推之前最好看一眼 `gst`

确认没有一堆你以为已经提交、其实还躺在工作区里的改动。

---

# 9. `gpf` —— 更安全的强制推送

## 原命令

在较新的 Git 里通常等价于：

```bash
git push --force-with-lease --force-if-includes
```

在较旧的 Git 里通常等价于：

```bash
git push --force-with-lease
```

## 作用

当你做了这些操作后，经常需要强推：

- `rebase`
- `commit --amend`
- 交互式 rebase 改历史
- squash 提交后替换原远程历史

因为这些操作改写了本地提交历史，普通 `gp` 往往会被拒绝。

这时你就需要“强制推送”。

## 常用指数

**★★☆☆☆**

## 危险等级

**高**

## 为什么 `gpf` 比 `gpf!` 好一些

### `gpf!`

相当于：

```bash
git push --force
```

它比较蛮横。

### `gpf`

相当于：

```bash
git push --force-with-lease
```

它会先确认远程分支没有被别人偷偷推进新内容。

所以它比纯 `--force` 更安全。

## 常见参数含义

### `--force-with-lease`

如果远程分支状态不是你以为的那个状态，就拒绝强推。

### `--force-if-includes`

在较新 Git 中进一步约束强推条件，降低误覆盖风险。

## 应用场景

### 场景 1：你 rebase 过自己的功能分支

```bash
grb main
gpf
```

### 场景 2：你 amend 了最近一次提交

```bash
git commit --amend
gpf
```

### 场景 3：你用交互式 rebase 把 5 个提交压成 1 个

```bash
grbi HEAD~5
gpf
```

## 实战例子

### 例子 A：你自己的功能分支，自己在维护

```bash
gco feat/login
grb main
gpf
```

这是相对常见且合理的用法。

### 例子 B：共享分支乱强推

如果某个分支是多人一起推的，你 `gpf` 可能直接把别人的历史覆盖掉。

## 提醒

### 1. `gpf` 不是日常 push 替代品

只有在“历史被你改写过”的时候才考虑它。

### 2. 能不用强推，就别用

尤其不要把强推当日常习惯。

### 3. 最适合 `gpf` 的地方

- 你自己的个人功能分支
- 还没被别人基于它开发
- 你明确知道自己刚刚 rebase / amend 过

### 4. 不适合 `gpf` 的地方

- 公共主分支
- 多人共享开发分支
- 你并不确定远程发生过什么

---

# 10. `grh` —— reset，重新摆放 HEAD / 暂存区 / 工作区

## 原命令

```bash
git reset
```

## 作用

`reset` 是 Git 里最容易让新手迷糊、也最值得学会的命令之一。

它主要是在做三件事里的某几件：

1. 移动当前分支指针（HEAD）
2. 调整暂存区
3. 有时连工作区也一起改

## 常用指数

**★★★☆☆**

## 危险等级

**中高**

## 先记一句最重要的话

> `grh` 本身不一定危险，危险的是它后面跟的参数。

## 最常见的几种 reset

### 1. `grh <commit>`

```bash
git reset <commit>
```

作用：把当前分支指针移到某个提交，通常也会重置暂存区，但保留工作区改动。

### 2. `grhs <commit>`

```bash
git reset --soft <commit>
```

作用：回退提交，但保留暂存区和工作区。

适合：

- 想撤销提交
- 但想把改动继续保留下来重新提交

### 3. `grh` / `gru <file>`

常用于取消暂存。

例子：

```bash
git add src/login.ts
gru src/login.ts
```

意思：把这个文件从暂存区拿下来。

### 4. `grhh <commit>`

```bash
git reset --hard <commit>
```

作用：回退提交、重置暂存区、连工作区也一起回退。

这个非常危险。

## 应用场景

### 场景 1：你 add 多了，想取消暂存

```bash
gru src/login.ts
```

### 场景 2：你刚提交，想把提交撤回但保留改动

```bash
grhs HEAD~1
```

### 场景 3：你本地改乱了，想彻底回到某个提交

```bash
grhh HEAD
```

或

```bash
grhh origin/main
```

## 实战例子

### 例子 A：撤销最近一次提交，但保留改动

```bash
grhs HEAD~1
```

结果：

- 最近一次 commit 没了
- 改动还在
- 暂存区状态也还在

### 例子 B：取消暂存某个文件

```bash
git add src/a.ts src/b.ts
gru src/b.ts
```

结果：

- `src/a.ts` 还在暂存区
- `src/b.ts` 被拿回工作区

### 例子 C：硬回退

```bash
grhh HEAD~1
```

结果：

- 最近一次提交没了
- 相关工作区改动也没了

## 提醒

### 1. 小白最常用、最该先会的是：取消暂存

也就是类似：

```bash
gru 文件名
```

### 2. `--soft` 比 `--hard` 安全很多

- `--soft`：保留改动
- `--hard`：可能直接清空改动

### 3. reset 和 revert 不是一回事

- `reset`：改你的本地历史指针
- `revert`：新建一个“反向提交”去抵消旧提交

### 4. 已经推送共享出去的历史，不要随便 reset 后再强推

因为最后往往会演变成你要 `gpf`，然后影响别人。

---

# 11. 这 8 个命令之间最常见的配合方式

## 流程 A：最日常的开发流程

```bash
gst
# 改代码
gst
# add / commit
gp
```

你虽然提交可能用图形化，但 `gst + gp` 还是很常见。

## 流程 B：更新主分支再继续开发

```bash
gco main
gl
gco feat/login
```

## 流程 C：把主分支更新同步到自己的功能分支（merge 版）

```bash
gco main
gl
gco feat/login
gm main
```

## 流程 D：把主分支更新同步到自己的功能分支（rebase 版）

```bash
gco main
gl
gco feat/login
grb main
```

## 流程 E：rebase 之后强推

```bash
gco feat/login
grb main
gpf
```

## 流程 F：提交乱了，回退重整

```bash
grhs HEAD~1
# 重新整理后再提交
```

---

# 12. 你这种“提交主要靠可视化”的习惯，实际最该怎么用

既然你主要是可视化提交，那命令行更多是拿来做这几件事：

## 第一类：看

- `gst`

## 第二类：切

- `gco`

## 第三类：同步

- `gl`
- `gm`
- `grb`

## 第四类：上传

- `gp`
- `gpf`

## 第五类：撤销 / 纠偏

- `grh`

换句话说，你现在这一套其实已经很像“命令行做仓库控制，可视化做提交细节”的工作方式了，这很正常，而且挺实用。

---

# 13. 给你一个最实用的优先级排序

如果按“你这种使用方式”的实战价值排序：

## S 级：必须熟

1. `gst`
2. `gl`
3. `gp`
4. `gco`

## A 级：应该熟

5. `gm`
6. `grb`
7. `grh`

## B 级：会用但要克制

8. `gpf`

---

# 14. 一句话记忆版

- `gst`：我现在啥状态？
- `gco`：我要切去哪？或者把哪个文件撤回来？
- `gl`：把远程最新内容拉下来
- `gm`：把另一条分支合进来
- `grb`：把我的提交重新排到新基底后面
- `gp`：把本地提交推上去
- `gpf`：我改历史了，只能更安全地强推
- `grh`：我要重新摆放提交、暂存区或工作区状态

---

# 15. 最后的安全建议

## 看到这些动作先停一下

- `gpf`
- `grhh`
- `gco -- 文件`
- `grb` 后准备推送时

## 先做这三步

1. `gst`
2. 看自己当前在哪个分支
3. 想清楚这个操作会影响：工作区、暂存区、本地历史，还是远程历史

你只要把这三件事养成习惯，Git 翻车概率会明显下降。

