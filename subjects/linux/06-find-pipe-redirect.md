# 查找、管道与重定向

> 在哪儿找文件（find、which）、怎么把命令的「输出」接到下一个命令或写进文件——管道 | 与重定向 >、>>、2>&1 的含义与用法。

- **标签**：`#Linux` `#find` `#管道` `#重定向` `#which`
- **日期**：2025-03-11
- **来源**：Ad-Studium 学习笔记

---

## 1. 核心概念 / 定义

| 术语 | 含义 |
|------|------|
| **标准输入（stdin）** | 命令默认从「键盘/终端」读输入；可被重定向为从文件或管道读。 |
| **标准输出（stdout）** | 命令正常结果输出到终端；可重定向到文件或接到管道。 |
| **标准错误（stderr）** | 错误信息单独一路输出；文件描述符为 2，默认也显示在终端。 |
| **管道（pipe）** | 把前一个命令的 **stdout** 作为后一个命令的 **stdin**；用 `|` 连接。 |
| **重定向** | 把本应输出到终端的内容改写到文件，或把文件内容当作输入。 |

---

## 2. 要点 / 步骤

1. **按名字/类型/时间找文件**：`find 目录 条件`。  
2. **查「某命令在哪」**：`which 命令名`。  
3. **管道**：`命令A | 命令B`，A 的输出当 B 的输入。  
4. **重定向**：`> file` 覆盖写、`>> file` 追加；`2>&1` 把错误合并到标准输出。

---

## 3. 细节与推导

### 3.1 查找文件：find 与 which

**find**

```bash
find /home -name "*.txt"
find . -type f -mtime -7
find /var -name "*.log" -size +100M
```

- **在干什么**：在指定**目录树下**按条件**递归**找文件/目录。  
  - `find /home -name "*.txt"`：在 /home 下找**名字**匹配 `*.txt` 的（注意引号防 shell 先展开）。  
  - `find . -type f -mtime -7`：当前目录下，**普通文件**（-type f），**修改时间**在 7 天内（-mtime -7）。  
  - `find /var -name "*.log" -size +100M`：名字 *.log 且**大于 100MB**。  
- **背后结构**：遍历目录树，对每个节点做条件判断；条件可组合（-a 与、-o 或）。  
- **常用**：-type d 目录、-type f 文件；-mmin 分钟、-mtime 天；-size +10M、-size -1G。

**which**

```bash
which python3
which ls
```

- **在干什么**：在 **PATH** 里找「这个名字」的**可执行文件**，输出其**完整路径**。  
- **背后结构**：按 PATH 里各目录顺序查，找到第一个就输出；用来确认你敲的命令实际跑的是哪个程序。

---

### 3.2 管道（|）

```bash
cat file.txt | grep "error"
ps aux | grep nginx
ls -l | less
```

- **在干什么**：把**左边命令的 stdout** 接到**右边命令的 stdin**；右边命令「像读文件一样」读左边的输出。  
- **背后结构**：内核建一条管道（内存里的队列），左边写、右边读；**stderr 不经过管道**，除非用 2>&1 把 stderr 并到 stdout。  
- **常见组合**：`cat/ls/ps 等 | grep 关键词`、`... | less` 分页看、`... | wc -l` 数行。

---

### 3.3 重定向

**输出重定向**

| 写法 | 含义 |
|------|------|
| **> file** | 把 **stdout** 写到 file；若 file 存在则**覆盖**。 |
| **>> file** | 把 **stdout** **追加**到 file 末尾。 |
| **2> file** | 把 **stderr** 单独写到 file。 |
| **2>&1** | 把 **stderr** 重定向到「当前 stdout 所指的地方」；常写成 `cmd > file 2>&1`，即标准输出和错误都进 file。 |
| **&> file** 或 **> file 2>&1** |  stdout 和 stderr 都写入 file。 |

- **在干什么**：  
  - `ls > list.txt`：ls 的结果写入 list.txt，终端不显示。  
  - `echo "line" >> log.txt`：在 log.txt 末尾加一行。  
  - `some_cmd > out.txt 2>&1`：正常输出和错误都进 out.txt；2>&1 要写在 > out.txt 后面，表示「2 指向 1 当前指向的文件」。  
- **背后结构**：文件描述符 0=stdin，1=stdout，2=stderr；重定向就是改变它们指向的文件。

**输入重定向**

```bash
cat < file.txt
grep "key" < input.txt
```

- **在干什么**：命令的 **stdin** 从文件读而不是从键盘读；`< file` 表示「用 file 当输入」。  
- **常见**：很多命令可直接接文件名（如 `grep "key" input.txt`），所以 `<` 用得少，但脚本里或需要明确「从哪读」时会用。

---

### 3.4 组合示例

```bash
find /var/log -name "*.log" -mtime -1 2>/dev/null | xargs grep -l "ERROR"
```

- **在干什么**：在 /var/log 下找最近 1 天修改的 .log 文件，把 find 的 stderr 丢掉（2>/dev/null），把找到的文件名通过管道传给 xargs，xargs 再对每个文件执行 `grep -l "ERROR"`（-l 只打文件名）。  
- 这里综合了 find、重定向 2>、管道、xargs；先掌握单条，再组合。

---

## 4. 总结 / 与其它知识的联系

- **find** 按路径+条件找文件；**which** 在 PATH 里找命令路径。  
- **|** 把前一个的 stdout 当后一个的 stdin；**> / >>** 把输出写到文件；**2>&1** 把错误并到标准输出一起重定向。  
- 下一篇讲**网络与远程**：ping、curl、wget、ssh、scp 的用途与基本用法。

---

## 5. 参考与延伸

- 本系列： [04-view-edit-and-text.md](04-view-edit-and-text.md)（grep）| [07-network-and-remote.md](07-network-and-remote.md)
