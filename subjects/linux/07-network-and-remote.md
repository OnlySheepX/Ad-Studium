# 网络与远程：ping、curl、wget、ssh、scp

> 测连通（ping）、下文件或调接口（curl、wget）、登录远程机（ssh）、在本地与远程之间拷文件（scp）；每条在干什么、常用参数与注意点。

- **标签**：`#Linux` `#网络` `#ping` `#curl` `#ssh` `#scp`
- **日期**：2025-03-11
- **来源**：Ad-Studium 学习笔记

---

## 1. 核心概念 / 定义

| 术语 | 含义 |
|------|------|
| **ICMP** | 网络层协议；ping 用 ICMP Echo 请求/应答测「对方是否可达、延迟大概多少」。 |
| **SSH** | 安全壳层协议；在加密通道里登录远程主机、执行命令或传文件。 |
| **SCP** | 基于 SSH 的「复制」；在本地与远程之间传文件，走的是 SSH 通道。 |

---

## 2. 要点 / 步骤

1. **测能否连通**：`ping 主机或IP`；Ctrl+C 停。  
2. **下载或发 HTTP 请求**：`curl URL`、`wget URL`；curl 更偏「请求+看响应」，wget 更偏「把 URL 当文件下」。  
3. **登录远程**：`ssh 用户@主机`；首次会问是否信任指纹。  
4. **拷文件**：`scp 本地 用户@主机:远程路径` 或 `scp 用户@主机:远程路径 本地`。

---

## 3. 细节与推导

### 3.1 ping：测连通与延迟

```bash
ping -c 4 8.8.8.8
ping -c 4 example.com
```

- **在干什么**：向目标发 **ICMP Echo 请求**，对方若在线会回 Echo 应答；显示**是否通、延迟（RTT）**。  
- **背后结构**：走的是 ICMP，不是 TCP；很多防火墙会禁 ping，所以「ping 不通」不一定代表「服务不可用」。  
- **-c 4**：发 4 个包后自动停；不加 -c 会一直发，需 Ctrl+C。  
- **用途**：快速看网络是否通、延迟是否正常。

---

### 3.2 curl：发请求、看响应、下载

```bash
curl https://example.com
curl -o saved.html https://example.com
curl -I https://example.com
curl -X POST -d "key=value" https://api.example.com/endpoint
```

- **在干什么**：按 **URL** 发请求（默认 GET），把**响应体**打到 stdout；可配合重定向或 -o 存文件。  
  - `curl URL`：把页面/接口返回内容打印到终端。  
  - `curl -o saved.html URL`：把响应**保存**为 saved.html。  
  - `curl -I URL`：只发 **HEAD** 请求，只看**响应头**（状态码、Content-Type 等）。  
  - `curl -X POST -d "key=value" URL`：发 **POST**，请求体为 key=value（表单项或 JSON 等）。  
- **背后结构**：curl 支持 HTTP/HTTPS、FTP 等；-d、-H、-u 等可构造各种请求，调 API、测接口常用。  
- **与 wget 区别**：curl 默认把结果打到 stdout，要存文件需 -o 或重定向；wget 默认就是「下载到当前目录」。

---

### 3.3 wget：下载文件

```bash
wget https://example.com/file.zip
wget -O myfile.zip https://example.com/file.zip
wget -c https://example.com/large.iso
```

- **在干什么**：把 **URL** 对应的资源**下载**到当前目录（或 -O 指定文件名）。  
  - `wget URL`：下载，文件名一般从 URL 或响应头取。  
  - `wget -O myfile.zip URL`：保存为指定文件名 myfile.zip。  
  - `wget -c URL`：**断点续传**；若之前下了一部分，会从断处继续。  
- **背后结构**：适合「把整个文件下下来」；脚本里批量下载常用。不支持发复杂请求（如 POST body），那是 curl 的强项。

---

### 3.4 ssh：登录远程与在远程执行命令

```bash
ssh user@192.168.1.100
ssh user@hostname
ssh user@host "ls /tmp"
```

- **在干什么**：  
  - `ssh user@主机`：用 **user** 身份**登录**那台主机；主机可以是 IP 或域名。首次连接会问是否信任主机密钥指纹，输入 yes 后提示输入 user 在该机上的**密码**（若已配密钥则免密）。  
  - `ssh user@host "命令"`：不进入交互 shell，在远程**执行这一条命令**，结果显示在本地终端。  
- **背后结构**：走 SSH 协议（默认 22 端口），全程加密；认证方式可以是密码或公钥。  
- **常用**：改端口用 `-p 端口`；用密钥：`ssh -i /path/to/key user@host`。

---

### 3.5 scp：在本地与远程之间拷文件

```bash
# 本地 → 远程
scp local.txt user@host:/home/user/
scp -r mydir user@host:/opt/

# 远程 → 本地
scp user@host:/var/log/app.log ./
scp -r user@host:/home/user/backup ./
```

- **在干什么**：  
  - **推**：`scp 本地路径  user@host:远程路径`；-r 表示递归拷目录。  
  - **拉**：`scp user@host:远程路径 本地路径`。  
- **背后结构**：底层用 SSH 建立加密通道，再传文件；所以**认证方式与 ssh 相同**（密码或密钥），端口也用 -P（注意 scp 的端口是大写 -P）。  
- **注意**：路径里有空格要用引号；远程路径不写的话会落到 user 的家目录。

---

## 4. 总结 / 与其它知识的联系

- **ping** 测连通与延迟；**curl** 发请求、看响应、下载；**wget** 专注下载、支持断点续传。  
- **ssh** 登录远程或执行远程命令；**scp** 在本地与远程间拷文件，认证同 ssh。  
- 和 GitHub 系列结合：在 Linux 服务器上配好 ssh 密钥后，可用 ssh 拉代码、用 scp 传配置；git 的 remote 也可用 `git@github.com:...` 这种 SSH 地址。

---

## 5. 参考与延伸

- 本系列： [06-find-pipe-redirect.md](06-find-pipe-redirect.md)；Git 系列 [05-remote-and-push.md](../github/05-remote-and-push.md)（SSH 与 HTTPS 选哪种）
