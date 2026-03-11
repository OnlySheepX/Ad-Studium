# 进程与系统管理

> 程序跑起来就是一个或多个进程；怎么查看（ps、top）、怎么结束（kill）、怎么放后台/拉回前台，以及用 systemctl 管服务——每条在干什么、背后对应什么概念。

- **标签**：`#Linux` `#进程` `#ps` `#top` `#kill` `#systemctl`
- **日期**：2025-03-11
- **来源**：Ad-Studium 学习笔记

---

## 1. 核心概念 / 定义

| 术语 | 含义 |
|------|------|
| **进程（process）** | 正在运行的程序实例；有唯一 **PID**（进程 ID），占用内存、CPU，可能占文件句柄等。 |
| **前台 / 后台** | 前台进程占着当前终端，能接收键盘输入；后台进程不占终端，或挂到「后台」继续跑。 |
| **信号（signal）** | 发给进程的「通知」；如 SIGTERM(15) 礼貌结束、SIGKILL(9) 强制杀。 |
| **服务（service / daemon）** | 常驻后台、提供某种功能的进程；通常由 systemd 管理（systemctl）。 |

---

## 2. 要点 / 步骤

1. **看有哪些进程**：`ps`（快照）、`top`（实时刷新）。  
2. **结束进程**：`kill PID` 或 `kill -9 PID`；知道进程名时可用 `pkill`、`killall`。  
3. **前台转后台**：Ctrl+Z 挂起，`bg` 放后台跑；`fg` 拉回前台；或启动时加 `&`。  
4. **管服务**：`systemctl start/stop/restart/status 服务名`。

---

## 3. 细节与推导

### 3.1 查看进程：ps 与 top

**ps（Process Status）**

```bash
ps
ps aux
ps -ef
ps aux | grep nginx
```

- **在干什么**：列出**当前**进程的一个**快照**（执行那一刻的状态）。  
  - `ps`：只列当前终端下的进程，信息少。  
  - `ps aux`：列出**所有用户**的进程（a=all，u=用户导向，x=包含无终端的进程）；列 USER、PID、CPU、内存、命令等。  
  - `ps -ef`：另一种风格，e=全部，f=完整格式。  
  - `ps aux | grep nginx`：在进程列表里**过滤**出含 nginx 的行，便于找 nginx 的 PID。  
- **背后结构**：ps 读的是 `/proc` 等内核提供的进程信息；不持续刷新，适合「看一眼」或脚本里用。

**top**

```bash
top
```

- **在干什么**：**实时**刷新进程列表和系统负载（CPU、内存）；默认按 CPU 排序，可按键改排序（如 M 按内存）。  
- **背后结构**：不断读 /proc 并重绘屏幕；q 退出，k 可输入 PID 发信号。  
- **常用**：看谁占 CPU/内存高，记下 PID 再决定是否 kill。

---

### 3.2 结束进程：kill

```bash
kill 12345
kill -9 12345
kill -TERM 12345
pkill nginx
killall nginx
```

- **在干什么**：  
  - `kill 12345`：给 PID 12345 发**默认信号 SIGTERM(15)**，请求进程「自己退出」；进程可捕获并做清理。  
  - `kill -9 12345`：发 **SIGKILL(9)**，内核**强制**结束进程，无法被捕获；慎用，可能留下脏状态。  
  - `kill -TERM 12345`：同默认，显式指定 SIGTERM。  
  - `pkill nginx`：按**进程名**匹配，给匹配到的进程发 SIGTERM。  
  - `killall nginx`：按名字杀所有叫 nginx 的进程。  
- **背后结构**：kill 通过内核向进程发信号；进程注册了处理函数就执行，否则按默认行为（TERM=退出，KILL=立即终止）。  
- **建议**：先 `kill PID`，若进程不退出再用 `kill -9 PID`。

---

### 3.3 前台与后台

| 操作 | 在干什么 |
|------|----------|
| **Ctrl+C** | 给当前前台进程发 **SIGINT**，一般会终止。 |
| **Ctrl+Z** | 给当前前台进程发 **SIGTSTP**，进程**挂起**（暂停），回到 shell；再用 `fg` 可拉回。 |
| **命令 &** | 在命令后加 `&`，进程在**后台**启动，不占终端；如 `sleep 100 &`。 |
| **bg** | 把当前被挂起（Ctrl+Z）的作业放到**后台继续跑**。 |
| **fg** | 把**后台或挂起的作业**拉回**前台**。 |
| **jobs** | 列出当前 shell 的**作业**（后台或挂起的任务）。 |

- **背后结构**：shell 用「作业」管理前后台；一个终端一个前台，多个后台/挂起作业用 jobs 看编号，fg %1 拉回 1 号作业。

---

### 3.4 服务管理：systemctl（systemd）

```bash
systemctl status nginx
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl enable nginx
systemctl disable nginx
```

- **在干什么**：  
  - **status**：看服务是否在跑、最近日志、是否开机自启。  
  - **start / stop / restart**：启动、停止、重启该**单元**（服务）。  
  - **enable**：设为**开机自动启动**；**disable**：关掉开机自启。  
- **背后结构**：systemd 是多数现代 Linux 的** init 系统**，负责管服务、挂载、定时等；每个服务对应一个「单元文件」（如 nginx.service），systemctl 读这些配置并启停进程。  
- **注意**：服务名通常不带 .service，如 `nginx` 即可；若报错「未找到」，可用 `systemctl list-units --type=service` 查实际名称。

---

## 4. 总结 / 与其它知识的联系

- **ps** 看快照、**top** 看实时；**kill** 发信号结束进程，先 TERM 再 KILL。  
- **Ctrl+Z / bg / fg** 管前后台；**&** 直接后台起进程。  
- **systemctl** 管由 systemd 托管的服务；启停、开机自启都靠它。下一篇讲**查找、管道与重定向**（find、which、|、>、>>）。

---

## 5. 参考与延伸

- 本系列： [04-view-edit-and-text.md](04-view-edit-and-text.md)（grep 配合 ps）| [06-find-pipe-redirect.md](06-find-pipe-redirect.md)
