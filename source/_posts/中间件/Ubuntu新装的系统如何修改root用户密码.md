---
title: Ubuntu 22.04 新装的系统如何修改root用户密码
date: 2025-11-18 09:52:41
tags: [操作系统, Ubuntu]
---

#### ✅ 使用 sudo 修改 root 密码
Ubuntu 默认给普通用户配置了 sudo，执行下面命令即可：
```
sudo passwd root
```
系统首先会提示输出当前用户的密码，输入当前用户密码后会提示你输入两次root用户的新密码。


#### 🔐 启用 root SSH 登录
启用 root SSH 登录
```
sudo vi /etc/ssh/sshd_config
```
找到 `PermitRootLogin prohibit-password` 这行配置，改为 `PermitRootLogin yes`，然后重启SSH服务 `sudo systemctl restart ssh`。
