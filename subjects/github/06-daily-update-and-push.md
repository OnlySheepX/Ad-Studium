# 日常更新与上传：改文件 → add → commit → push

> 已经关联过远程、做过第一次 push 之后，平时的流程就是：改文件 → 选进暂存区（add）→ 打成提交（commit）→ 推送到 GitHub（push）。这里把「每一步干什么、顺序为什么这样」串成一条线。

- **标签**：`#Git` `#GitHub` `#日常流程` `#push` `#工作流`
- **日期**：2025-03-11
- **来源**：Ad-Studium 学习笔记

---

## 1. 核心概念 / 定义

- **日常更新上传**：把「你在本地的修改」变成「GitHub 上可见的版本」的完整链路：**工作区修改 → 暂存 → 提交到本地仓库 → 推送到远程**。  
- **上游（upstream）**：若之前用 `git push -u origin main` 设过，当前分支会记住「默认推送到 origin 的 main」；之后直接 `git push` 即可，不用再写 `origin main`。

---

## 2. 要点 / 步骤

1. **改文件**：在项目里正常编辑、新建、删除文件（都在工作区）。
2. **选要提交的**：`git status` 看一眼 → `git add .` 或 `git add 某文件`，把改动放进暂存区。
3. **打成一条记录**：`git commit -m "简短说明"`，写入本地仓库。
4. **推到 GitHub**：`git push`（已设过 upstream）或 `git push origin main`。
5. 若多人协作或多设备：推送前可能要先 `git pull` 拉取远程更新，再 push（见 07）。

---

## 3. 细节与推导

### 3.1 流程在动哪一层（回顾）

```
你改文件（工作区）
    → git add（工作区 → 暂存区）
    → git commit（暂存区 → 本地仓库，生成一条 commit）
    → git push（本地仓库 → 远程仓库 GitHub）
```

- **add** 只影响本地，不碰远程。  
- **commit** 只影响本地历史，不碰远程。  
- **push** 才会让 GitHub 上的仓库变样；所以「只 commit 不 push」时，网页上看不到最新提交。

### 3.2 推荐操作顺序（一步一步来）

**第 1 步：改完代码后先看状态**

```bash
git status
```

- **在干什么**：看哪些文件被修改/新增/删除，哪些已经 add、哪些还没。避免漏提交或误提交。  
- **背后结构**：Git 比较「工作区 / 暂存区 / 最近一次 commit」三者，给出状态说明。

**第 2 步：把要提交的改动放进暂存区**

```bash
git add .
```

- **在干什么**：把当前目录下所有改动（含新文件）都标记为「参与下一次 commit」。若只想提交部分文件，就改成 `git add 文件1 文件2`。  
- **背后结构**：暂存区相当于「购物车」；commit 只打包「购物车里的内容」，不打包你没 add 的修改。

**第 3 步：打成一条提交并写说明**

```bash
git commit -m "更新 GitHub 笔记：添加 06 日常更新流程"
```

- **在干什么**：把暂存区里的内容固化成一条新的 commit，写入本地 `.git`，并附上这句说明。  
- **背后结构**：每个 commit 是项目的一个「快照」；历史就是一连串这样的快照。说明写清楚，以后 `git log` 一眼能看懂。

**第 4 步：推送到 GitHub**

```bash
git push
```

- **在干什么**：把本地当前分支上「远程还没有」的 commit，按顺序上传到 origin 的对应分支（通常是 main）。  
- **背后结构**：若第一次 push 时用过 `-u origin main`，这里不用再写分支名；否则要写：`git push origin main`。  
- **若提示要先 pull**：说明远程已有别人或别的设备推上去的提交，需要先拉再推，见 07。

### 3.3 一条命令能不能搞定？

- **不能跳过 add 和 commit**：push 推的是「已经存在的 commit」，不会自动把工作区或暂存区的改动打包。所以必须至少：add → commit → push。  
- 可以习惯性写成顺序三连：  
  `git add .` → `git commit -m "说明"` → `git push`  
  每次上传前跑一遍，就形成稳定节奏。

### 3.4 提交频率建议

- 每完成一个小功能或一小节笔记就 commit 一次，说明写清楚「做了什么」；这样历史清晰、以后也方便回滚或查问题。  
- 不必每改一行就 push，可以本地多次 commit，再一次性 `git push`（会把这几次 commit 都传上去）。

---

## 4. 总结 / 与其它知识的联系

- 日常上传 = **改文件 → add → commit → push**；add/commit 是本地操作，push 才和 GitHub 同步。  
- 设过 upstream 后，日常只需 `git push`；没设过就写全：`git push origin main`。  
- 多人或多设备时，推送前先 **pull** 再 push，避免冲突；详见 07。

---

## 5. 参考与延伸

- 本系列： [04-add-and-commit.md](04-add-and-commit.md) | [05-remote-and-push.md](05-remote-and-push.md) | [07-pull-and-sync.md](07-pull-and-sync.md)
