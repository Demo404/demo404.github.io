---
title: Ubuntu 22.04上安装Nginx步骤
date: 2025-11-13 11:21:07
tags: [中间件, Nginx]
---

#### 使用apt安装nginx

使用命令 `apt list -a nginx` 查看有哪些版本可以安装。
如果不想指定版本，可以执行 `sudo apt install nginx -y` 直接安装最新的版本（本文以此为例）。

##### 安装 🚀

- 1、安装nginx
```
sudo apt install nginx -y
```

- 2、执行以下命令检查 Nginx 是否成功安装
```
nginx -v
```

- 3、成功会输出版本号
```
nginx version: nginx/1.29.3
```

##### 启动和设置开机自启 🍃

- 1、启动 Nginx
```
sudo systemctl start nginx
```

- 2、设置开机自启
```
sudo systemctl enable nginx
```

- 3、查看运行状态
```
sudo systemctl status nginx
```

如果看到输出中包含：`Active: active (running)` 说明 Nginx 已经启动成功。

##### 配置防火墙 🧱
```
sudo ufw allow 'Nginx Full'
sudo ufw reload
```
使用 `sudo ufw status` 查看规则是否生效。

##### Nginx 默认文件结构说明 📖
| 目录/文件                         | 说明                       |
| ----------------------------- | ------------------------ |
| `/etc/nginx/nginx.conf`       | 主配置文件                    |
| `/etc/nginx/sites-available/` | 存放虚拟主机配置文件               |
| `/etc/nginx/sites-enabled/`   | 指向启用的虚拟主机配置（通过软链接）       |
| `/var/www/html/`              | 默认网页目录（`index.html` 放这里） |
| `/var/log/nginx/access.log`   | 访问日志                     |
| `/var/log/nginx/error.log`    | 错误日志                     |


##### 配置HTTPS（免费证书）✅
1️⃣ 安装 Certbot（Let's Encrypt 工具）：
```
sudo apt install certbot python3-certbot-nginx -y
```

2️⃣ 获取证书：
```
sudo certbot --nginx -d blog.example.com
```
Certbot 会自动：
- 验证域名；
- 生成 SSL 证书；
- 修改 Nginx 配置文件；
- 自动配置 HTTPS。

3️⃣ 自动续期检查：
```
sudo certbot renew --dry-run
```

##### 常用命令速查 👀
| 操作       | 命令                                       |
| -------- | ---------------------------------------- |
| 启动 Nginx | `sudo systemctl start nginx`             |
| 停止 Nginx | `sudo systemctl stop nginx`              |
| 重启 Nginx | `sudo systemctl restart nginx`           |
| 平滑加载配置   | `sudo systemctl reload nginx`            |
| 检查配置文件   | `sudo nginx -t`                          |
| 查看日志     | `sudo tail -f /var/log/nginx/access.log` |

