# Linux 体系与常用指令

本目录整理 Linux 的系统结构、目录体系（FHS）以及日常最常用的 shell 指令；每条指令都说明「在干什么」和背后的结构，便于记忆和排查问题。

| 文件 | 说明 |
|------|------|
| [01-linux-overview-and-fhs.md](01-linux-overview-and-fhs.md) | Linux 概述、发行版、目录体系 FHS、一切皆文件 |
| [02-file-and-directory-commands.md](02-file-and-directory-commands.md) | 文件与目录：ls / cd / pwd / mkdir / touch / cp / mv / rm / 路径 |
| [03-permissions-and-ownership.md](03-permissions-and-ownership.md) | 权限与归属：rwx、chmod、chown、数字与符号表示 |
| [04-view-edit-and-text.md](04-view-edit-and-text.md) | 查看与编辑：cat / less / head / tail / grep / 简单 vim |
| [05-process-and-system.md](05-process-and-system.md) | 进程与系统：ps / top / kill / systemctl / 前台后台 |
| [06-find-pipe-redirect.md](06-find-pipe-redirect.md) | 查找、管道与重定向：find / which / \| / > / >> / 2>&1 |
| [07-network-and-remote.md](07-network-and-remote.md) | 网络与远程：ping / curl / wget / ssh / scp |

建议按 01 → 07 顺序阅读；用到哪条指令可随时回查对应文件。
