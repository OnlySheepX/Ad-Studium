# 实战示例：从零到上传 GitHub 全流程

> 从「新建一个空文件夹」开始，用 Git 管理版本，再把代码推上 GitHub；按顺序做一遍，相当于把 01–05 串成一条可执行的流水线。

- **标签**：`#Git` `#GitHub` `#实战` `#示例` `#从零开始`
- **日期**：2025-03-11
- **来源**：Ad-Studium 学习笔记

---

## 1. 本示例在干什么

- **起点**：电脑上还没有这个项目，也没有 Git 仓库。
- **终点**：GitHub 上有一个仓库，里面能看到你本地提交的至少一个文件（例如 README.md）。
- **用到的概念**：新建目录 → `git init` → 工作区改文件 → `add` → `commit` → 网页建空仓 → `remote add` → `push`。  
对应理论见： [01](01-git-and-github-concepts.md)、[02](02-create-repo-on-github.md)、[03](03-local-init-or-clone.md)、[04](04-add-and-commit.md)、[05](05-remote-and-push.md)。

---

## 2. 前置条件

- 已安装 **Git**（终端里能执行 `git --version`）。
- 有 **GitHub 账号**，且能登录网页；若用 HTTPS 推送，需准备好 **Personal Access Token**（GitHub 已不支持用密码 push）。

---

## 3. 全流程（一步一步做）

### 第 1 步：在本地新建项目文件夹并进入

**你要做的事**：选一个位置，建一个空文件夹当作「项目根目录」，并进入该目录。

```bash
mkdir my-first-repo
cd my-first-repo
```

- **在干什么**：`mkdir` 创建名为 `my-first-repo` 的目录；`cd` 把当前工作目录切进去，之后所有 Git 命令都在这个目录下执行。  
- **可改**：文件夹名可随意，如 `d:\projects\my-app`；路径里不要有中文和奇怪符号，避免后续问题。

---

### 第 2 步：用 Git 管理这个文件夹（初始化仓库）

**你要做的事**：让当前目录变成一个「Git 仓库」，即出现隐藏的 `.git` 目录。

```bash
git init
```

- **在干什么**：在当前目录下创建 `.git`，用来存提交历史、分支、配置等；这个目录就变成了「工作区 + 本地仓库」，但还没有任何提交。  
- **结果**：终端会提示 `Initialized empty Git repository in .../my-first-repo/.git/`。

---

### 第 3 步：新建一个文件并做第一次提交

**你要做的事**：在项目里加一个文件（例如 README），然后把它纳入版本并打成第一条提交。

**3.1 新建文件**

用任意方式在 `my-first-repo` 下创建一个文件，例如 `README.md`，内容随便写几行，例如：

```markdown
# My First Repo

This is my first project on GitHub.
```

若在终端里操作，可以：

```bash
echo "# My First Repo" > README.md
echo "This is my first project on GitHub." >> README.md
```

- **在干什么**：在**工作区**增加了一个新文件；此时 Git 能发现它，但还没有把它纳入版本（未 add）。

**3.2 纳入暂存区并提交**

```bash
git add README.md
git commit -m "Add README"
```

- **在干什么**：`git add` 把 README.md 的当前内容放进**暂存区**；`git commit` 把暂存区打成一条**提交**，写入本地仓库，并附上说明 "Add README"。  
- **结果**：本地已有「一条提交」；用 `git log` 可以看到这条记录。此时还没有和 GitHub 产生任何关系。

---

### 第 4 步：在 GitHub 网页上创建空仓库

**你要做的事**：在 GitHub 上新建一个仓库，用来接收你即将推送的代码。下面按「网页上怎么点、每个选项什么意思」写清楚。

---

#### 4.1 打开「新建仓库」页面

1. 用浏览器打开 **https://github.com**，登录你的账号。
2. 在页面**右上角**找到 **绿色的 “New” 按钮**（或一个 **+** 号），点一下。
3. 在下拉菜单里选 **“New repository”**（新建仓库）。  
   → 会跳转到创建仓库的页面，URL 一般是 `https://github.com/new`。

---

#### 4.2 填写仓库信息（页面上每个选项在干什么）

| 页面上你看到的 | 你要怎么做 | 含义 / 说明 |
|----------------|------------|-------------|
| **Repository name** | 必填。例如填 `my-first-repo`。 | 仓库名 = 项目名，会出现在地址里：`github.com/你的用户名/my-first-repo`。只能用英文、数字、连字符 `-`，不能有空格。不必和本地文件夹同名，但同名方便记。 |
| **Description** | 可选，可留空。 | 一句话描述项目，会显示在仓库首页，不影响 Git 操作。 |
| **Public / Private** | 选 **Public**（公开）即可。 | Public = 所有人能看到仓库；Private = 仅自己或授权的人能看。选哪个都不影响后面 push 的指令。 |
| **Initialize this repository with:** 下面的三个勾选项 | **本示例里三个都不要勾选**，保持都不选。 | 见下面 4.3。 |
| 绿色按钮 **Create repository** | 检查名字没问题后，点它。 | 服务器会真正创建这个仓库，并跳转到新仓库的首页。 |

---

#### 4.3 为什么本示例不要勾选 README / .gitignore / License？

- **Add a README file**：勾选的话，GitHub 会在仓库里自动生成一个 `README.md` 并做**第一次提交**。这样远程就不是「空仓」了，你本地 push 时可能被拒绝（远程多了一条你本地没有的提交），需要先 `git pull` 再 push。为了一次跑通，这里选**不勾选**，建一个**完全空**的仓库。
- **Add .gitignore**：给仓库加一个忽略文件模板（如选 Node、Python）。可以以后在本地自己建 `.gitignore` 再提交，这里可先不选。
- **Choose a license**：给项目选一个开源协议（如 MIT）。学习用可先不选，以后在网页或本地加都可以。

---

#### 4.4 创建完成后：在哪里复制仓库地址

点完 **Create repository** 后，会进入**新仓库的首页**。此时仓库是空的，页面上通常会有：

- 标题 **“Quick setup”** 或 **“…or push an existing repository from the command line”**；
- 下面有一行或两行**命令**，命令里会带一个 **URL**。

**复制这个 URL**（二选一，选你准备用的方式）：

- **用 HTTPS**：复制类似  
  `https://github.com/你的用户名/my-first-repo.git`  
  的地址（页面上可能有 **HTTPS** 标签或下拉可选）。
- **用 SSH**：复制类似  
  `git@github.com:你的用户名/my-first-repo.git`  
  的地址（需要本机已配好 SSH 密钥）。

把复制好的地址先放在记事本或终端旁边，**第 5 步**里会贴进 `git remote add origin <这里>`。

- **在干什么**：在远程准备好一个「空壳」仓库，并得到唯一 URL；本地还没有和它关联。更多选项说明见 [02-create-repo-on-github.md](02-create-repo-on-github.md)。

---

### 第 5 步：把本地仓库和 GitHub 上的仓库关联起来

**你要做的事**：告诉本地 Git「远程仓库的地址是谁」，并确认分支名。

```bash
git remote add origin https://github.com/你的用户名/my-first-repo.git
```

- **在干什么**：在本地 `.git/config` 里记录：有一个叫 **origin** 的远程，地址是你刚复制的 URL。以后 `git push origin main` 里的 `origin` 就指向这个地址。  
- **若你用 SSH**：把 URL 换成 `git@github.com:你的用户名/my-first-repo.git` 即可。  
- **确认分支名**：很多新仓库默认分支是 `main`。若你本地是 `master`，可以统一成 main：  
  `git branch -M main`  
  然后再推送。

---

### 第 6 步：推送到 GitHub（第一次上传）

**你要做的事**：把本地当前分支上的提交，上传到远程仓库。

```bash
git branch -M main
git push -u origin main
```

- **在干什么**：  
  - `git branch -M main`：把当前分支改名为 `main`（若已经是 main 可跳过或执行也无妨）。  
  - `git push -u origin main`：把本地 **main** 分支上的提交推送到远程 **origin** 的 main 分支；`-u` 表示把本地 main 和远程 main 绑定，以后在这个分支上直接打 `git push` 即可。  
- **若提示要登录**：HTTPS 会要求用户名和密码；密码处要填 **Personal Access Token**（在 GitHub → Settings → Developer settings → Personal access tokens 里生成）。  
- **结果**：终端出现类似 `Enumerating objects... Writing objects: 100%...`，最后出现 `main -> main`；到 GitHub 网页上刷新该仓库，应能看到 README.md 和提交记录。

---

### 第 7 步：验证

**你要做的事**：在浏览器里打开该仓库页面，确认文件与提交都在。

- 应能看到：根目录下有 **README.md**，点进去能看到你写的内容；**Commits** 里有一条 "Add README"。
- 之后若要继续加文件、改文件，按 [06-daily-update-and-push.md](06-daily-update-and-push.md) 的流程：改 → `git add` → `git commit -m "说明"` → `git push` 即可。

---

### 第 8 步：后续怎么「更新并上传」（日常应用向）

**你要做的事**：以后每次你更新笔记/代码，都按这条链路走一遍：**改文件（工作区）→ add（暂存区）→ commit（本地仓库）→ push（远程 GitHub）**。

#### 8.1 最常用的日常更新三连

```bash
git status
git add .
git commit -m "Update notes"
git push
```

- **git status**：先看一眼当前改了哪些文件、哪些已经在暂存区，避免漏提/误提。
- **git add .**：把当前目录下的改动放进暂存区（相当于“这次提交要包含这些改动”）。
- **git commit -m "..."**：把暂存区打成一条提交，写进本地历史；message 要能说明“这次改了什么”。
- **git push**：把本地新增的提交上传到 GitHub。因为你第一次用过 `git push -u origin main`，Git 已记住上游分支，所以这里可以不写 `origin main`。

#### 8.2 只想提交一部分文件（不要用 add .）

```bash
git add subjects/github/08-example-from-zero-to-github.md
git commit -m "Update GitHub example"
git push
```

- **在干什么**：你在“挑选”本次提交的范围，只把指定文件放进暂存区；其他文件改动仍留在工作区，不会被这次 commit 带走。

#### 8.3 如果 push 被拒绝（远程比你新）

常见原因：你在 GitHub 网页上改过文件、或另一台电脑 push 过，导致远程 `origin/main` 比你本地 `main` 多了一些提交。

```bash
git pull --rebase
git push
```

- **git pull --rebase**：先把远程的新提交拉下来，再把你的本地提交“接”在远程最新提交后面（历史更直）。如果你不想用 rebase，也可以用 `git pull`（会 merge）。
- **再 git push**：此时本地已经包含远程更新，推送就不会再因为 “non-fast-forward” 被拒。

如果 pull 过程中出现 **冲突**（conflict），按提示打开冲突文件，手动决定保留哪边内容，保存后：

```bash
git add 冲突文件
git rebase --continue
git push
```

（冲突与同步细节见 [07-pull-and-sync.md](07-pull-and-sync.md)。）

---

## 4. 流程小结（对应关系）

| 步骤 | 你在做的 | 对应的概念/篇 |
|------|----------|----------------|
| 1 | 建文件夹、cd 进去 | 准备「工作区」目录 |
| 2 | git init | 本地空仓库，见 03 |
| 3 | 新建文件 → add → commit | 工作区→暂存区→提交，见 01、04 |
| 4 | 网页建空仓、复制 URL | 远程空壳，见 02 |
| 5 | git remote add origin &lt;URL&gt; | 关联远程，见 05 |
| 6 | git push -u origin main | 第一次推送，见 05 |
| 7 | 网页上检查 | 确认本地与远程一致 |

---

## 5. 总结 / 与其它知识的联系

- 从零到 GitHub 的**最小闭环**：**本地目录 → init → 至少一次 add+commit → 网页建空仓 → remote add → push**。  
- 日常更新不再需要 init、remote add，只需：改文件 → add → commit → push（见 06）。  
- 若你建 GitHub 仓库时勾选了 README，远程会多一条提交，需要先 `git pull origin main --rebase`（或 `git pull`）再 push，见 [07-pull-and-sync.md](07-pull-and-sync.md)。

---

## 6. 参考与延伸

- 本系列： [01-git-and-github-concepts.md](01-git-and-github-concepts.md) ～ [07-pull-and-sync.md](07-pull-and-sync.md)  
- 遇到 "rejected" 或 "failed to push"：先看 [05-remote-and-push.md](05-remote-and-push.md) 的「常见情况与报错」；若远程已有提交，按 [07-pull-and-sync.md](07-pull-and-sync.md) 先 pull 再 push。
