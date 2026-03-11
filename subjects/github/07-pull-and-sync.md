# 从远程拉取与同步：pull 与 fetch

> 当 GitHub 上有了你本地没有的提交（例如在网页改了 README、或另一台电脑 push 了），需要把那些更新「拉」到本地。这里讲 `git pull` 和 `git fetch` 分别做什么、背后的结构、以及何时用哪个。

- **标签**：`#Git` `#GitHub` `#pull` `#fetch` `#同步`
- **日期**：2025-03-11
- **来源**：Ad-Studium 学习笔记

---

## 1. 核心概念 / 定义

| 术语 | 含义 |
|------|------|
| **git fetch origin** | 向远程（origin）**询问并下载**最新提交和分支信息，更新本地的「远程分支」视图（如 `origin/main`），但**不自动合并**到当前工作分支。 |
| **git pull origin main** | 通常 = **fetch + merge**：先拉取远程 main 的更新，再把这些更新**合并**进当前分支。若当前就在 main 且已设 upstream，可简写为 `git pull`。 |
| **远程分支（remote-tracking branch）** | 本地用来「记住远程某分支上次长什么样」的引用，例如 `origin/main`；fetch 会更新它，但不会动你正在编辑的工作区。 |

---

## 2. 要点 / 步骤

1. **只想先看远程有没有更新、不想动当前文件**：用 `git fetch origin`，再用 `git log origin/main` 或 `git status` 看差异。  
2. **想直接把远程更新合并进当前分支**：用 `git pull origin main`（或当前分支已设 upstream 时直接 `git pull`）。  
3. **push 前若提示「远程有更新」**：先 `git pull`（或 `git pull --rebase`），解决冲突（若有）后再 `git push`。

---

## 3. 细节与推导

### 3.1 为什么需要「拉取」？

- 仓库有两份：**本地** 和 **远程（GitHub）**。别人或你在网页上/另一台电脑上的修改，会先出现在远程；你要在本地也看到这些修改，就要「拉取」。  
- **push 被拒绝** 时，常见原因就是：远程已经有了新的 commit，Git 要求你先把这些 commit 拉下来、合并或变基后再 push，保证历史一致。

### 3.2 fetch：只拉不并

```bash
git fetch origin
```

- **在干什么**：  
  1. 连接 origin 对应的 URL（如 GitHub）。  
  2. 下载远程上你还没有的 commit、以及各分支的最新指向。  
  3. 更新本地的「远程分支」引用，例如把 `origin/main` 指到远程 main 的最新 commit。  
- **背后结构**：你的**当前分支**（如 `main`）和**工作区**都不变；只是本地「知道」了远程现在长什么样。查看方式：`git log origin/main`、或 `git status` 会提示「Your branch is behind 'origin/main' by X commits」。  
- **何时用**：想先安全地看远程更新、再决定要不要合并时；或想对比「本地 main vs 远程 main」时。

### 3.3 pull：拉取并合并（最常用）

```bash
git pull origin main
```

- **在干什么**：  
  1. 等价于先执行 `git fetch origin`，再执行 `git merge origin/main`（把远程 main 合并进当前分支）。  
  2. 若当前分支就是 main 且设过 `origin main` 为 upstream，可直接写 `git pull`，Git 会去拉 origin/main 并合并进当前 main。  
- **背后结构**：pull 会**改变**当前分支和工作区：把远程的提交合并进来，若有冲突需要你解决后再 commit。  
- **结果**：本地 main 已经包含「刚才远程上多出来的那些 commit」，此时再 push 就不会被拒绝。

**若你当前在 main 且已设 upstream：**

```bash
git pull
```

- **在干什么**：拉取并合并「当前分支跟踪的远程分支」（即 origin/main），最省事的写法。

### 3.4 pull --rebase（可选）

```bash
git pull origin main --rebase
```

- **在干什么**：先 fetch，再把你的本地新 commit **挪到**远程最新 commit 之后（变基），而不是做一次 merge 提交。  
- **背后结构**：历史会是一条直线，没有「merge commit」；适合喜欢简洁历史的人。若不确定，先用普通 `git pull` 即可。

### 3.5 常见场景与顺序

| 场景 | 建议操作 |
|------|----------|
| 开始工作前，想和远程保持一致 | `git pull`（或 `git pull origin main`） |
| push 时提示 rejected，要先整合远程更新 | `git pull`，有冲突就解决后 commit，再 `git push` |
| 只想看远程有没有新东西、暂不合并 | `git fetch origin`，再 `git log origin/main` 或 `git diff main origin/main` |
| 多设备：在 A 电脑 push 了，在 B 电脑要继续改 | 在 B 上先 `git pull` 再编辑，改完 add → commit → push |

---

### 3.6 冲突（conflict）时怎么办

- **现象**：pull 或 merge 时提示 "merge conflict"，打开文件会看到 `<<<<<<<`、`=======`、`>>>>>>>` 标记。  
- **含义**：同一处内容在本地和远程都被改过，Git 不知道保留哪边，要你手动决定。  
- **步骤**：编辑文件，删掉标记行，保留或合并成你想要的内容 → 保存 → `git add 该文件` → `git commit`（完成这次合并）→ 再 `git push`。  
- 详细冲突处理可单独做一篇笔记；这里先建立「先 pull 再 push」的习惯即可。

---

## 4. 总结 / 与其它知识的联系

- **fetch** = 只更新「远程分支」的视图，不碰当前分支和工作区；**pull** = fetch + 合并到当前分支，会改变本地文件。  
- 日常协作建议：**动手前 pull 一次，push 前若被拒再 pull 一次**，这样本地和 GitHub 保持同步。  
- 整个「从创建到更新上传」的闭环就是：01 概念 → 02 建仓 → 03 本地 init/clone → 04 add/commit → 05 第一次 push → 06 日常 add/commit/push → 07 需要时 pull 再 push。

---

## 5. 参考与延伸

- 本系列： [05-remote-and-push.md](05-remote-and-push.md) | [06-daily-update-and-push.md](06-daily-update-and-push.md)
- 官方： [Git - 远程仓库](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E8%BF%9C%E7%A8%8B%E4%BB%93%E5%BA%93%E7%9A%84%E4%BD%BF%E7%94%A8)
