# 在 GitHub 网页上创建仓库

> 在 GitHub 网站里「新建一个仓库」要做什么、每个选项代表什么、创建完后你会得到什么。

- **标签**：`#GitHub` `#创建仓库` `#网页操作`
- **日期**：2025-03-11
- **来源**：Ad-Studium 学习笔记

---

## 1. 核心概念 / 定义

- **在 GitHub 上创建仓库**：在 GitHub 的服务器上划出一块空间，给你一个「空仓库」或带初始文件的仓库，并得到一个 **仓库地址（URL）**。之后你的本地 Git 会通过这个地址做「上传」和「拉取」。
- **仓库地址**：通常有两种写法——**HTTPS** 和 **SSH**；第一次用建议先用 HTTPS，后面再学 SSH。

---

## 2. 要点 / 步骤

1. 登录 GitHub → 右上角 **+** → **New repository**。
2. 填 **Repository name**（必填），选 **Public/Private**，决定是否勾选 **README / .gitignore / License**。
3. 点 **Create repository** 后，记下页面上的 **仓库 URL**，后面「关联远程」要用。

---

## 3. 细节与推导

### 3.1 每一步在干什么

| 你在网页上做的事 | 背后的含义 |
|------------------|------------|
| 点 **New repository** | 告诉 GitHub：我要一个新项目空间，用来存代码/笔记。 |
| 填 **Repository name** | 仓库名 = 项目名，会出现在 URL 里，例如 `my-project` → `github.com/你的用户名/my-project`。只能用英文、数字、连字符，不能有空格。 |
| 选 **Public / Private** | Public = 所有人可见（可设成只读）；Private = 只有你或你授权的人能看。选哪个都不影响后面的 Git 指令。 |
| **Add a README file** | 在仓库里自动生成一个 `README.md` 文件，并做**第一次提交**。若勾选，远程仓库就不是「完全空」的，你 clone 下来会直接带这个文件。 |
| **Add .gitignore** | 自动加一个 `.gitignore` 模板（如选 Node、Python），让 Git 忽略指定类型的文件（如 `node_modules/`），不把它们纳入版本控制。 |
| **Choose a license** | 给项目加一个开源协议文件（如 MIT），声明别人怎么可以用你的代码。个人学习项目可先不选，后面可补。 |
| **Create repository** | 服务器真正创建这个仓库，并生成唯一 URL。 |

### 3.2 创建完成后页面上有什么

- **Quick setup** 区域会显示仓库地址，例如：
  - HTTPS：`https://github.com/你的用户名/仓库名.git`
  - SSH：`git@github.com:你的用户名/仓库名.git`
- 下面还会有一串「…or push an existing repository from the command line」的指令，那就是：**本地已有项目时，怎么关联这个新仓库并第一次推送**。下一篇和第五篇会用到。

### 3.3 两种常见起点

- **从零开始、本地还没项目**：创建时可以不勾选 README，得到一个「完全空」的远程仓库，然后本地 `git init` 再关联并 push（见 03、05）。
- **先建好远程、再在本地开发**：创建时勾选 README（或再加 .gitignore），然后本地用 `git clone 仓库URL` 把整个仓库拷下来，再在本地改、add、commit、push（见 03、04、06）。

---

## 4. 总结 / 与其它知识的联系

- 在 GitHub 上「创建仓库」 = 在远程准备好一个**空壳（或带 README 的壳）**，并拿到 **URL**。
- 填名字、选 Public/Private、选不选 README/.gitignore，都是为这个远程仓库定个「起点」；真正写代码、做提交还是在**本地**，再通过 push 同步上去。
- 下一篇讲：本地是「从零 init」还是「从远程 clone」，以及各自对应的指令和背后的结构。

---

## 5. 参考与延伸

- GitHub 帮助： [Creating a repository](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template)
- 本系列： [01-git-and-github-concepts.md](01-git-and-github-concepts.md) | [03-local-init-or-clone.md](03-local-init-or-clone.md)
