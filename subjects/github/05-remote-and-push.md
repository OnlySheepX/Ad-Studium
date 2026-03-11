# 关联远程仓库与第一次推送（push）

> 本地仓库建好、有了至少一次 commit 之后，要告诉 Git「远程仓库的地址是谁」，再把本地提交**推**上去。这里讲 `remote add` 和第一次 `push` 在做什么、常见报错怎么处理。

- **标签**：`#Git` `#GitHub` `#remote` `#push` `#上传`
- **日期**：2025-03-11
- **来源**：Ad-Studium 学习笔记

---

## 1. 核心概念 / 定义

| 术语 | 含义 |
|------|------|
| **远程（remote）** | 一个「名字 + URL」的对应关系，表示「这个远程仓库在哪儿」。默认常叫 **origin**，指向你在 GitHub 上建的那个仓库。 |
| **git remote add &lt;名字&gt; &lt;URL&gt;** | 给当前本地仓库添加一个远程：以后 push/pull 时说「推到 origin」就是指这个 URL。 |
| **git push -u origin main** | 把当前分支（如 main）的提交**上传**到远程仓库里同名的分支；`-u` 表示「以后默认就推这个远程、这个分支」。 |

---

## 2. 要点 / 步骤

1. 在 GitHub 上已经建好仓库，并复制好仓库 URL（HTTPS 或 SSH）。
2. 本地在项目目录里执行：`git remote add origin <你的仓库URL>`（若用 clone 的则已有 origin，可跳过）。
3. 确认本地有至少一次 commit；确认分支名（一般是 `main` 或 `master`）。
4. 执行：`git push -u origin main`（若你的分支叫 `master` 就改成 `master`）。
5. 若用 HTTPS，可能会提示登录/令牌；按提示在浏览器或命令行完成认证，成功后远程就有你的代码了。

---

## 3. 细节与推导

### 3.1 为什么要「关联远程」？

- 本地 Git 默认不知道你的代码要推到哪台服务器、哪个地址。**remote** 就是给那个地址起个名字（通常叫 `origin`），之后说「push 到 origin」就等于「推到那个 URL」。
- 用 `git clone` 得到的仓库，**已经自动配置好 origin**，不需要再 `remote add`；用 `git init` 新建的仓库，必须自己加一次。

### 3.2 指令与背后结构

**1）添加远程**

```bash
git remote add origin https://github.com/你的用户名/仓库名.git
```

- **在干什么**：在本地仓库的配置里记录：有一个叫 **origin** 的远程，地址是后面的 URL。  
- **背后结构**：这条信息写在 `.git/config` 里；之后 `git push origin main` 里的 `origin` 就查这个表找到 URL。  
- **注意**：如果已经存在 `origin`（比如 clone 来的），再执行会报错「remote origin already exists」；要改地址可用 `git remote set-url origin <新URL>`。

**2）查看当前配置的远程**

```bash
git remote -v
```

- **在干什么**：列出所有远程名字及其「拉取用」和「推送用」的 URL（一般两者相同）。用来确认 origin 是否指对了 GitHub 仓库。

**3）第一次推送（push）**

```bash
git push -u origin main
```

- **在干什么**：  
  1. 把本地当前分支（这里是 `main`）上**尚未推送到远程**的 commit，按顺序上传到远程仓库。  
  2. 远程仓库会多出一个叫 `main` 的分支（若没有会自动创建），并指向你推上去的最新 commit。  
  3. `-u`（即 `--set-upstream`）：把「当前分支」和「远程的 main」绑定，以后在这个分支上直接打 `git push` 就会默认推到 `origin main`。  
- **背后结构**：push 是「把本地 commit 对象上传 + 更新远程分支指针」；远程没有的 commit 会传上去，远程已有的不会重复传。  
- **若你的默认分支叫 master**：把上面命令里的 `main` 改成 `master` 即可：`git push -u origin master`。

**4）如何确认自己当前分支名**

```bash
git branch
```

- **在干什么**：列出本地分支，当前分支前面有 `*`。新建仓库第一次 commit 后，通常就在 `main` 或 `master` 上。

---

### 3.3 常见情况与报错

| 情况 | 原因 | 处理 |
|------|------|------|
| **remote origin already exists** | 已经加过 origin（例如 clone 的仓库）。 | 不需要再 add；要改地址用 `git remote set-url origin <新URL>`。 |
| **failed to push / 认证失败** | HTTPS 需要账号密码或 Personal Access Token（PAT）。 | GitHub 已不支持密码，需在 GitHub → Settings → Developer settings → Personal access tokens 生成 PAT，在提示密码时粘贴 PAT。或改用 SSH（另学）。 |
| **分支名不匹配** | 本地是 `master`，远程或 GitHub 默认是 `main`。 | 要么 `git push -u origin master` 推上去（远程会多一个 master）；要么在本地把分支改名为 main：`git branch -M main` 再 `git push -u origin main`。 |
| **! [rejected] main -> main (non-fast-forward)** | 远程已有你本地没有的提交（例如在网页上改了 README）。 | 先拉再推：`git pull origin main --rebase` 或 `git pull origin main`，再 `git push origin main`。详见 07。 |

---

## 4. 总结 / 与其它知识的联系

- **remote add origin &lt;URL&gt;** = 告诉本地「远程仓库在哪」；**push** = 把本地分支的提交上传到远程对应分支。  
- 第一次推送用 **git push -u origin main**（或 master），既上传又设好默认上游，之后在同一分支上只需 **git push**。  
- 推送只上传「已经 commit 过」的内容；若还没 commit，要先按 04 做 add + commit，再 push。下一篇是「日常更新」：改文件 → add → commit → push 的完整流程。

---

## 5. 参考与延伸

- 本系列： [03-local-init-or-clone.md](03-local-init-or-clone.md) | [04-add-and-commit.md](04-add-and-commit.md) | [06-daily-update-and-push.md](06-daily-update-and-push.md) | [07-pull-and-sync.md](07-pull-and-sync.md)
