# 权限与归属：chmod 与 chown

> 每个文件/目录都有「谁拥有、谁能读/写/执行」；理解 rwx、数字表示、以及 chmod / chown 在改什么，才能避免 Permission denied 和误开权限。

- **标签**：`#Linux` `#权限` `#chmod` `#chown` `#rwx`
- **日期**：2025-03-11
- **来源**：Ad-Studium 学习笔记

---

## 1. 核心概念 / 定义

| 术语 | 含义 |
|------|------|
| **所有者（owner）** | 文件/目录的「主人」，通常创建它的用户；可用 chown 改（一般需 root）。 |
| **所属组（group）** | 文件属于哪个用户组；组内成员可共享一组权限。 |
| **其他人（others）** | 既不是所有者、也不在所属组里的用户。 |
| **rwx** | 读（r）、写（w）、执行（x）；对**文件**表示能否读内容、改内容、作为程序运行；对**目录**表示能否列目录、在目录里增删、能否 cd 进该目录。 |

---

## 2. 要点 / 步骤

1. 用 **ls -l** 看权限与归属；格式如 `-rwxr-xr--` 和 `user group`。
2. **改权限**：`chmod`，可用数字（如 755）或符号（u+x、g-w）。
3. **改所有者/组**：`chown`、`chgrp`，通常需 root 或相应权限。

---

## 3. 细节与推导

### 3.1 ls -l 里权限长什么样

```text
-rwxr-xr--  1 user group  1234 Mar 11 10:00 script.sh
```

- **第 1 位**：类型；`-` 普通文件，`d` 目录，`l` 符号链接。  
- **第 2–4 位**：**所有者**的 rwx。  
- **第 5–7 位**：**所属组**的 rwx。  
- **第 8–10 位**：**其他人**的 rwx。  
- **user / group**：所有者与所属组；后面是大小、修改时间、名字。

**目录的 x**：对目录来说，**x = 能否进入（cd）**；没有 x 就列得出文件名也进不去。**r** = 能否用 ls 列出目录项。

### 3.2 数字表示（八进制）

把 rwx 当成二进制位：r=4，w=2，x=1，三位相加得到一个 0–7 的数。

| 数字 | 二进制 | 含义   |
|------|--------|--------|
| 7    | 111    | rwx    |
| 6    | 110    | rw-    |
| 5    | 101    | r-x    |
| 4    | 100    | r--    |
| 0    | 000    | ---    |

例如 **755**：所有者 7(rwx)，组 5(r-x)，其他人 5(r-x)。  
例如 **644**：所有者 6(rw-)，组与他人 4(r--)，常见于普通文件。

### 3.3 chmod（改权限）

**数字形式**

```bash
chmod 755 script.sh
chmod 644 config.txt
chmod -R 755 mydir
```

- **在干什么**：  
  - `chmod 755 script.sh`：把 script.sh 设为 **rwxr-xr-x**（所有者全权限，其他人只读+执行）。  
  - `chmod 644 config.txt`：**rw-r--r--**，常见配置文件。  
  - `chmod -R 755 mydir`：**递归**把 mydir 下所有文件与子目录都设为 755。  
- **背后结构**：修改的是文件 inode 里的「模式位」；只有所有者或 root 能改。

**符号形式**

```bash
chmod u+x script.sh    # 给所有者加执行
chmod g-w file.txt     # 去掉组的写权限
chmod o-rwx file.txt   # 去掉其他人全部权限
chmod a+r file.txt     # 给所有人加读（a=all）
```

- **u** = owner，**g** = group，**o** = others，**a** = all；**+** 加权限，**-** 减权限，**=** 设为某权限。

### 3.4 chown 与 chgrp（改归属）

```bash
sudo chown newuser file.txt
sudo chown newuser:newgroup file.txt
sudo chgrp devs file.txt
sudo chown -R user:group mydir
```

- **在干什么**：  
  - `chown newuser file.txt`：把 file.txt 的**所有者**改为 newuser。  
  - `chown newuser:newgroup file.txt`：同时改**所有者**和**所属组**。  
  - `chgrp devs file.txt`：只把**所属组**改为 devs。  
  - `-R`：递归改目录下所有项。  
- **背后结构**：文件 inode 里存的是用户 ID（uid）和组 ID（gid）；chown/chgrp 改的就是这两个。通常只有 root 能把文件给别人；普通用户一般只能改自己拥有的文件的组（且自己要在目标组里）。

---

## 4. 总结 / 与其它知识的联系

- 权限分**所有者 / 组 / 其他人**，各自 **r/w/x**；目录的 x 表示能否进入。  
- **chmod** 改权限（数字 755/644 或符号 u+x 等）；**chown/chgrp** 改归属，多需 root。  
- 遇到 Permission denied：先 **ls -l** 看是谁的、缺什么权限，再决定改权限还是改归属。下一篇讲**查看与编辑**文件内容（cat、less、vim 等）。

---

## 5. 参考与延伸

- 本系列： [02-file-and-directory-commands.md](02-file-and-directory-commands.md) | [04-view-edit-and-text.md](04-view-edit-and-text.md)
