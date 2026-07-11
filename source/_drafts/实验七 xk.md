
---
title: 实验七：远程登录管理（SSH）
date: 2026-07-10
categories: 实验报告
tags:
  - SSH
  - 远程管理
  - 密钥认证
  - SCP
  - rsync
  - Linux
---

## 一、实验目的

1. 掌握 OpenSSH 服务器的安装与配置方法
2. 理解 SSH 的两种认证方式（密码认证与密钥认证）及其适用场景
3. 掌握 SSH 密钥对的生成与公钥部署方法，实现免密登录
4. 掌握 SCP 和 rsync 命令的使用，实现跨机文件传输与同步
5. 理解多机开发环境下的远程管理流程
6. 了解 SSH 端口转发的基本概念

## 二、实验环境

| 项目 | 详情 |
|------|------|
| 服务器端操作系统 | Ubuntu Server 22.04.5 LTS |
| 客户端操作系统 | Windows 11（宿主机）/ Ubuntu Server 22.04.5 LTS |
| SSH 服务 | OpenSSH Server 8.9p1 |
| SSH 客户端 | OpenSSH Client（Windows 10/11 自带） |
| 网络模式 | NAT + 端口转发（宿主机 22 → 虚拟机 22） |
| 远程连接工具 | 命令行 SSH（CMD / PowerShell） |

## 三、实验内容与步骤

### 3.1 检查 SSH 服务状态

在虚拟机上执行：

```bash
sudo systemctl status ssh
```

如果服务未安装，执行：

```bash
sudo apt install openssh-server -y
```

如果服务未运行，执行：

```bash
sudo systemctl start ssh
sudo systemctl enable ssh
```

> 📸 **此处可插入截图1**：SSH 服务运行状态

### 3.2 SSH 服务配置文件

SSH 服务的主配置文件为 `/etc/ssh/sshd_config`。

**查看配置文件：**

```bash
sudo cat /etc/ssh/sshd_config
```

**关键配置项说明：**

| 配置项 | 推荐值 | 说明 |
|--------|--------|------|
| `Port` | 22 | SSH 监听端口（默认 22） |
| `PermitRootLogin` | no | 禁止 root 用户直接登录（安全建议） |
| `PubkeyAuthentication` | yes | 允许公钥认证 |
| `PasswordAuthentication` | yes | 允许密码认证（实验环境保留） |
| `AllowUsers` | 用户名列表 | 限制允许登录的用户（可选） |
| `ClientAliveInterval` | 60 | 保持连接活跃的间隔 |
| `ClientAliveCountMax` | 3 | 未响应超时次数 |

**本实验中的配置（已配置）：**

```bash
Port 22
PermitRootLogin no
PubkeyAuthentication yes
PasswordAuthentication yes
```

修改配置后需重启服务：

```bash
sudo systemctl restart ssh
```

> 📸 **此处可插入截图2**：sshd_config 关键配置

### 3.3 SSH 密码认证登录

#### 3.3.1 从宿主机（Windows）连接到虚拟机

打开 CMD 或 PowerShell，执行：

```bash
ssh groupwork@10.0.2.15
```

输入密码 `123456`，成功登录后显示：

```
groupwork@ubuntu-server:~$
```

#### 3.3.2 从虚拟机连接到其他虚拟机（多机环境）

如果有多台虚拟机，可以互相 SSH 连接：

```bash
ssh groupwork@10.150.126.51
```

> 📸 **此处可插入截图3**：SSH 密码登录成功

### 3.4 SSH 密钥认证配置（免密登录）

#### 3.4.1 生成 SSH 密钥对

**在宿主机（Windows）的 Git Bash 或 WSL 中执行：**

```bash
ssh-keygen -t ed25519 -C "你的邮箱@qq.com"
```

一路回车（不设密码），密钥对生成在 `~/.ssh/` 目录：
- `id_ed25519`：私钥（保密，不可外传）
- `id_ed25519.pub`：公钥（可公开，部署到服务器）

**在宿主机 CMD 中查看公钥：**

```bash
type C:\Users\你的用户名\.ssh\id_ed25519.pub
```

**在虚拟机 Linux 中查看公钥：**

```bash
cat ~/.ssh/id_ed25519.pub
```

#### 3.4.2 将公钥复制到服务器

**方法一：使用 `ssh-copy-id`（Windows Git Bash / Linux 中可用）：**

```bash
ssh-copy-id groupwork@10.0.2.15
```

输入密码 `123456`，公钥自动添加到服务器的 `~/.ssh/authorized_keys`。

**方法二：手动复制（如果 `ssh-copy-id` 不可用）：**

在宿主机查看公钥：

```bash
type C:\Users\你的用户名\.ssh\id_ed25519.pub
```

复制输出内容，然后在虚拟机中执行：

```bash
mkdir -p ~/.ssh
echo "粘贴公钥内容" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh
```

#### 3.4.3 测试免密登录

在宿主机执行：

```bash
ssh groupwork@10.0.2.15
```

如果不再提示输入密码，直接进入系统，说明密钥认证配置成功。

> 📸 **此处可插入截图4**：免密登录验证

#### 3.4.4 安全强化（可选）

密钥认证配置成功后，可以关闭密码认证以提高安全性：

```bash
sudo sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
sudo systemctl restart ssh
```

**注意**：关闭密码认证前，确保密钥认证已正常工作，否则可能无法登录。

### 3.5 SCP 命令：跨机文件传输

SCP（Secure Copy）是基于 SSH 的加密文件传输工具，适合一次性、少量文件传输。

#### 3.5.1 从宿主机上传文件到虚拟机

**在宿主机 CMD 中执行：**

```bash
# 上传单个文件
scp D:\test.txt groupwork@10.0.2.15:/home/groupwork/

# 上传整个目录（-r 递归）
scp -r D:\project groupwork@10.0.2.15:/home/groupwork/
```

#### 3.5.2 从虚拟机下载文件到宿主机

```bash
# 下载单个文件
scp groupwork@10.0.2.15:/home/groupwork/log.txt D:\

# 下载整个目录（-r 递归）
scp -r groupwork@10.0.2.15:/home/groupwork/backup D:\
```

#### 3.5.3 SCP 常用参数

| 参数 | 说明 |
|------|------|
| `-r` | 递归传输目录 |
| `-P 端口` | 指定 SSH 端口（默认 22） |
| `-C` | 启用压缩传输 |
| `-v` | 详细输出（调试用） |

> 📸 **此处可插入截图5**：SCP 文件传输

### 3.6 rsync 命令：高效同步工具

rsync 支持增量同步，只传输发生变化的部分，比 SCP 更适合大文件和目录的定期同步。

#### 3.6.1 安装 rsync

```bash
sudo apt install rsync -y
```

#### 3.6.2 本地同步

```bash
rsync -avz /home/groupwork/source/ /home/groupwork/backup/
```

#### 3.6.3 远程推送（宿主机 → 虚拟机）

```bash
rsync -avz -e ssh D:\project\ groupwork@10.0.2.15:/home/groupwork/project/
```

#### 3.6.4 远程拉取（虚拟机 → 宿主机）

```bash
rsync -avz -e ssh groupwork@10.0.2.15:/home/groupwork/logs/ D:\logs\
```

#### 3.6.5 保持目录完全一致（删除目标端多余文件）

```bash
rsync -avz --delete -e ssh D:\source\ groupwork@10.0.2.15:/home/groupwork/dest/
```

#### 3.6.6 排除特定文件或目录

```bash
rsync -avz --exclude='*.log' --exclude='tmp/' -e ssh D:\source\ groupwork@10.0.2.15:/home/groupwork/dest/
```

#### 3.6.7 rsync 常用参数

| 参数 | 说明 |
|------|------|
| `-a` | 归档模式（保留权限、时间戳等） |
| `-v` | 详细输出 |
| `-z` | 传输时压缩 |
| `-e ssh` | 使用 SSH 作为传输协议 |
| `--delete` | 删除目标端多余文件 |
| `--exclude` | 排除特定模式的文件 |
| `-n` / `--dry-run` | 模拟运行，不实际传输 |

> 📸 **此处可插入截图6**：rsync 同步执行结果

### 3.7 多机环境下的用户管理

#### 3.7.1 创建多机共享账号

在多台虚拟机（node1、node2、node3）上分别创建相同的用户：

```bash
sudo adduser xiaoming
```

#### 3.7.2 分发 SSH 公钥

将用户的公钥分发到所有需要登录的服务器：

```bash
ssh-copy-id xiaoming@node1
ssh-copy-id xiaoming@node2
ssh-copy-id xiaoming@node3
```

#### 3.7.3 配置 ~/.ssh/config 简化连接

在用户端创建 `~/.ssh/config` 文件（Windows 中位于 `C:\Users\用户名\.ssh\config`）：

```
Host node1
    HostName 192.168.56.101
    User xiaoming

Host node2
    HostName 192.168.56.102
    User xiaoming

Host node3
    HostName 192.168.56.103
    User xiaoming
```

配置后可直接使用：

```bash
ssh node1
```

> 📸 **此处可插入截图7**：SSH config 配置及连接

### 3.8 SSH 端口转发（扩展）

#### 3.8.1 本地端口转发

将远程服务器的端口映射到本地：

```bash
ssh -L 8080:localhost:80 groupwork@10.0.2.15
```

访问本地的 `http://localhost:8080` 即访问虚拟机的 Nginx 服务。

#### 3.8.2 远程端口转发

将本地的端口暴露到远程服务器：

```bash
ssh -R 9090:localhost:3000 groupwork@10.0.2.15
```

## 四、实验结果

通过本次实验，成功完成了以下目标：

| 检查项 | 状态 |
|--------|------|
| SSH 服务正常运行（systemctl status） | ✅ |
| 密码认证 SSH 登录成功 | ✅ |
| SSH 密钥对生成成功（ed25519） | ✅ |
| 公钥部署到服务器（ssh-copy-id / 手动） | ✅ |
| 免密登录验证通过 | ✅ |
| SCP 文件上传/下载成功 | ✅ |
| rsync 目录同步成功（含 --delete 和 --exclude） | ✅ |
| 多机 SSH config 配置简化连接 | ✅ |
| 端口转发概念验证 | ✅ |

## 五、遇到的问题与解决方案

| 问题现象 | 可能原因 | 解决方案 |
|----------|----------|----------|
| `ssh: command not found` | SSH 客户端未安装 | Windows 10/11 自带 OpenSSH Client，可在“设置→可选功能”中安装；Linux 执行 `sudo apt install openssh-client` |
| `Connection refused` | SSH 服务未运行或端口被防火墙拦截 | 检查服务状态：`sudo systemctl status ssh`；检查防火墙：`sudo ufw allow 22` |
| `Permission denied (publickey,password)` | 密码错误或密钥未正确配置 | 确认用户名和密码正确；检查 `~/.ssh/authorized_keys` 是否包含公钥 |
| 免密登录仍需密码 | `authorized_keys` 文件权限过大 | 执行 `chmod 600 ~/.ssh/authorized_keys` 和 `chmod 700 ~/.ssh` |
| `ssh-copy-id` 命令不存在 | Windows CMD 不支持该命令 | 使用 Git Bash 执行，或手动复制公钥 |
| SCP 连接失败 | 服务器 SSH 端口非默认 | 使用 `-P 端口号` 指定端口 |
| rsync 同步速度慢 | 未启用压缩 | 添加 `-z` 参数启用压缩传输 |
| rsync `--delete` 误删文件 | 源端路径写错 | 先执行 `rsync -avzn --delete` 模拟测试，确认无误后再正式执行 |

## 六、实验总结

本次实验系统学习了 SSH 远程管理技术，从服务配置到密码登录，从密钥认证到文件传输，建立了完整的远程管理技能体系。

### 关键收获

1. **SSH 是现代 Linux 运维的基础工具**：无论是单机管理还是多机协作，SSH 都是最核心的远程管理协议，掌握 SSH 的配置和使用是 Linux 管理者的基本功。

2. **密钥认证是生产环境的标配**：相比密码认证，密钥认证更安全（防暴力破解）、更便捷（免密登录），是自动化脚本和 CI/CD 流水线的基础。

3. **SCP 和 rsync 各有所长**：
   - SCP 适合简单、单次的小文件传输
   - rsync 适合大目录、定期同步（增量传输、带宽高效）

4. **多机环境的核心是统一的用户管理**：在多台服务器上保持用户和权限的一致性，配合 `~/.ssh/config` 简化连接，可以显著提高管理效率。

5. **远程管理的安全原则**：
   - 禁用 root 直接登录
   - 使用密钥认证代替密码认证
   - 限制允许登录的用户和 IP
   - 定期更换密钥和密码

后续实验将在此基础上，结合 Git、Hexo 和 Nginx，实现从开发到部署的完整自动化流程（CI/CD）。

## 七、参考资料

1. OpenSSH 官方文档：https://www.openssh.com/manual.html
2. Ubuntu SSH 指南：https://ubuntu.com/server/docs/security-ssh
3. SCP 手册：https://man.openbsd.org/scp
4. rsync 官方文档：https://rsync.samba.org/documentation.html
5. SSH 密钥认证指南：https://wiki.archlinux.org/title/SSH_keys
