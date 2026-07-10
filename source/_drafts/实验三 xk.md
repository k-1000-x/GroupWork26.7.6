
---
title: 实验三：虚拟机安装与配置
date: 2026-07-10
categories: 实验报告
tags:
  - VirtualBox
  - Ubuntu Server
  - 虚拟机
  - SSH
  - 端口转发
---

## 一、实验目的

1. 掌握 VirtualBox 虚拟机的安装与基本配置方法
2. 学会在虚拟机中安装 Ubuntu Server 22.04 LTS 操作系统
3. 理解虚拟机网络的三种模式（NAT、桥接、仅主机）及其适用场景
4. 掌握虚拟机的网络配置方法，实现宿主机与虚拟机的互联
5. 了解多机虚拟化环境的构建思路

## 二、实验环境

| 项目 | 详情 |
|------|------|
| 宿主机操作系统 | Windows 11 |
| 虚拟化平台 | Oracle VirtualBox 7.0.26 |
| 客户机操作系统 | Ubuntu Server 22.04.5 LTS |
| 虚拟机内存 | 2048 MB |
| 虚拟机硬盘 | 20 GB（动态分配） |
| 虚拟机网络 | NAT（用于联网更新软件）/ 端口转发（用于组员访问） |
| SSH 工具 | OpenSSH Server |
| 远程连接方式 | SSH（端口 22，通过端口转发映射到宿主机 8080 端口） |

## 三、实验内容与步骤

### 3.1 安装 VirtualBox

1. 访问 VirtualBox 官网 `https://www.virtualbox.org/`，下载 Windows 版本安装包（本实验使用 7.0.26 版本）。
2. 右键点击安装包，选择 **“以管理员身份运行”**，启动安装向导。
3. 在“自定义安装”页面，保持所有选项默认勾选（VirtualBox Application、VirtualBox USB Support、VirtualBox Networking 等），点击“下一步”。
4. 安装过程中如弹出“网络接口警告”，点击 **“是(Y)”** 继续（虚拟网卡驱动安装需要短暂断开网络，属正常现象）。
5. 安装完成后，启动 VirtualBox，确认主界面正常显示。

> 📸 **此处可插入截图1**：VirtualBox 主界面


### 3.2 下载 Ubuntu Server 22.04 LTS ISO 镜像

1. 访问 Ubuntu 官网 `https://ubuntu.com/download/server`，下载 22.04.5 LTS 版本。
2. 如官网下载速度较慢，可改用国内镜像站：
   - 清华镜像：`https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/22.04/`
   - 下载 `ubuntu-22.04.5-live-server-amd64.iso` 文件（约 1.8 GB）

> 📸 **此处可插入截图2**：ISO 镜像文件


### 3.3 创建虚拟机

1. 在 VirtualBox 主界面，点击 **“新建(N)”**。
2. 填写虚拟机信息：
   - **名称**：`ubuntu-server`
   - **文件夹**：`D:/VirtualBoxVMS/ubuntu-server`（建议放在非系统盘）
   - **ISO 映像**：选择已下载的 `ubuntu-22.04.5-live-server-amd64.iso`
   - **类型**：Linux
   - **版本**：Ubuntu (64-bit)
3. 点击 **“下一步”**。
4. **硬件配置**：
   - **内存大小**：2048 MB（至少 2 GB）
   - **处理器**：1 CPU
   - **启用 EFI**：**不勾选**（保持关闭）
5. 点击 **“下一步”**。
6. **虚拟硬盘**：
   - **磁盘空间**：20 GB
   - **预先分配全部空间**：**不勾选**（使用动态分配，按需增长）
7. 点击 **“完成”** 开始创建虚拟机。

> 📸 **此处可插入截图3**：虚拟机配置摘要


### 3.4 安装 Ubuntu Server 系统

1. 启动虚拟机，进入 Ubuntu 安装界面。
2. **选择语言**：选择 `English`（推荐，便于后续问题排查），按回车确认。
3. **安装程序更新提示**：选择 **“Continue without updating”**（不更新安装程序，避免耗时）。
4. **安装类型**：选择 **“Ubuntu Server”**（标准安装），不勾选“Search for third-party drivers”，选择 **“Done”**。
5. **网络配置**：保持默认 DHCP 设置（`enp0s3` 网卡），选择 **“Done”**。
6. **代理配置**：留空（无需代理），选择 **“Done”**。
7. **镜像源配置**：系统自动选择清华镜像源（`mirrors.tuna.tsinghua.edu.cn`），保持默认，选择 **“Done”**。
8. **磁盘分区**：选择 **“Use an entire disk”**，勾选 **“Set up this disk as an LVM group”**，**不勾选**加密选项，选择 **“Done”**。
9. **确认分区写入**：选择 **“Continue”**。
10. **用户信息配置**：
    - **Your name**：`GroupWork`
    - **Your server's name**：`ubuntu-server`
    - **Pick a username**：`groupwork`
    - **Choose a password**：`123456`
    - **Confirm your password**：`123456`
11. **SSH 配置**：**务必按空格键选中 `Install OpenSSH server`**，保持 `Allow password authentication over SSH` 为勾选状态，选择 **“Done”**。
12. **Ubuntu Pro 升级**：选择 **“Skip for now”**，选择 **“Continue”**。
13. **Snap 软件包选择**：全部不选（无需安装额外 Snap 包），选择 **“Done”**。
14. 等待安装完成（约 5-10 分钟），选择 **“Reboot Now”** 重启虚拟机。

> 📸 **此处可插入截图4**：安装过程中的关键界面（语言选择、分区、SSH 配置等）


### 3.5 登录系统并配置网络（NAT 模式）

重启后，系统显示登录提示符：

```
ubuntu-server login:
```

输入用户名 `groupwork`，密码 `123456` 登录。

**第一步：修复 DNS 配置（关键）**

由于 Ubuntu Server 默认 DNS 配置在部分网络环境下可能无法正常解析域名，需要手动配置 DNS 服务器：

```bash
echo "nameserver 114.114.114.114" | sudo tee /etc/resolv.conf
echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.conf
```

**第二步：更换软件源为国内镜像（加速软件安装）**

```bash
sudo tee /etc/apt/sources.list <<EOF
deb http://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
EOF
```

**第三步：更新软件包列表**

```bash
sudo apt update
```

> 📸 **此处可插入截图5**：DNS 配置和 apt update 成功执行


### 3.6 创建小组用户账号

为每位组员创建独立的 SSH 登录账号（本小组共 4 人）：

```bash
sudo adduser cn
sudo adduser hcy
sudo adduser xrh
sudo adduser wyj
```

每个用户设置密码为 `123456`，其余信息（全名、房间号等）直接按回车跳过。

验证用户创建成功：

```bash
cat /etc/passwd | grep -E 'cn|hcy|xrh|wyj'
```

> 📸 **此处可插入截图6**：用户列表


### 3.7 配置端口转发（让组员远程访问）

由于校园网环境下桥接模式存在认证限制，本实验采用 **NAT + 端口转发** 方案，让组员通过宿主机 IP 访问虚拟机。

**第一步：确认虚拟机 IP 地址**

```bash
ip addr
```

本实验中虚拟机 NAT 模式下的 IP 为 `10.0.2.15`。

**第二步：关闭虚拟机**

```bash
sudo shutdown now
```

**第三步：在 VirtualBox 中设置端口转发**

1. 在 VirtualBox 主界面，选中 `ubuntu-server`，点击 **“设置”** → **“网络”**。
2. 在 **“高级”** 区域，点击 **“端口转发”**。
3. 添加规则：
   - **名称**：`web`
   - **协议**：`TCP`
   - **主机端口**：`8080`
   - **子系统端口**：`80`（HTTP 服务）
4. 点击 **“确定”**，保存设置。

**第四步：重新启动虚拟机并登录**

```bash
# 启动虚拟机后
ssh groupwork@10.0.2.15
# 密码: 123456
```

**第五步：查看宿主机 IP 地址**

在宿主机（Windows）CMD 中执行：

```bash
ipconfig
```

找到当前网卡的 IPv4 地址（如 `10.150.178.86`），将此 IP 和端口号 `8080` 发给小组成员。

> 📸 **此处可插入截图7**：端口转发配置界面


### 3.8 安装必要软件（Node.js、Git、Nginx）

```bash
# 安装 Node.js 20.x
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs

# 验证安装
node -v

# 安装 Git
sudo apt install -y git

# 安装 Nginx
sudo apt install -y nginx

# 验证 Nginx 状态
sudo systemctl status nginx
```

> 📸 **此处可插入截图8**：软件版本验证


### 3.9 克隆博客项目并部署

```bash
cd ~
git clone https://github.com/k-1000-x/GroupWork26.7.6.git
cd GroupWork26.7.6
npm install
hexo generate
sudo cp -r public/* /var/www/html/
```

> 📸 **此处可插入截图9**：部署成功后访问页面


## 四、实验结果

通过本次实验，成功完成了以下目标：

| 检查项 | 状态 |
|--------|------|
| VirtualBox 安装成功 | ✅ |
| Ubuntu Server 22.04 LTS 安装成功 | ✅ |
| 系统网络配置正常（NAT 模式可上网） | ✅ |
| DNS 配置生效（`apt update` 正常） | ✅ |
| 小组用户账号创建完成（4 人） | ✅ |
| SSH 服务正常运行 | ✅ |
| 端口转发配置完成（宿主机 8080 → 虚拟机 80） | ✅ |
| Node.js 20.x 安装成功 | ✅ |
| Git 安装成功 | ✅ |
| Nginx 安装成功 | ✅ |
| Hexo 博客项目部署成功，可访问 | ✅ |

## 五、遇到的问题与解决方案

| 问题现象 | 可能原因 | 解决方案 |
|----------|----------|----------|
| `apt update` 报 `Temporary failure resolving` | DNS 解析失败，系统未配置可用的 DNS 服务器 | 手动配置 DNS：`echo "nameserver 114.114.114.114" \| sudo tee /etc/resolv.conf` |
| `apt update` 卡在 `0% [Connecting...]` | 软件源连接慢或无法访问 | 替换为国内镜像源（阿里云或清华源） |
| `/etc/apt/sources.list` 文件损坏或丢失 | 误操作导致文件被删除 | 使用 `sudo tee` 重新创建并写入阿里云源配置 |
| 宿主机无法访问 `10.0.2.15:80` | NAT 模式下端口未映射到宿主机 | 配置端口转发（宿主机 8080 → 虚拟机 80） |
| 组员访问 `宿主机IP:8080` 超时 | Windows 防火墙拦截了 8080 端口 | 暂时关闭 Windows 防火墙测试，或添加入站规则放行 8080 端口 |
| 克隆仓库后 `hexo generate` 报错 | 主题文件或 Node.js 版本不兼容 | 更新依赖：`npm install hexo@latest --save`，重新 `npm install` |
| 虚拟机端口转发后组员仍无法访问 | 宿主机 IP 地址发生变化 | 组员通过 `ipconfig` 查询新 IP，更新访问地址 |

## 六、实验总结

本次实验完成了从零开始搭建 Linux 虚拟机的全过程，涵盖了 VirtualBox 安装、Ubuntu Server 系统部署、网络配置、用户管理、端口转发配置以及基础软件环境搭建。

### 关键收获

1. **虚拟机网络模式的理解**：
   - **NAT 模式**：虚拟机通过宿主机上网，适合安装软件和更新系统，但外部无法直接访问
   - **桥接模式**：虚拟机独立接入局域网，其他设备可直接访问，但在校园网环境下可能面临认证问题
   - **端口转发**：NAT 模式下的折中方案，通过端口映射实现外部访问，兼顾了联网便利性和可访问性

2. **Linux 系统管理基础**：
   - 掌握了 `apt` 包管理工具的使用和软件源配置
   - 理解了 DNS 配置在系统网络通信中的关键作用
   - 熟悉了用户管理命令（`adduser`、`passwd`）

3. **小组协作的基础设施**：
   - 通过创建独立用户账号，实现了多人共用一台服务器的权限隔离
   - SSH 服务和端口转发配置为后续的远程协作奠定了基础

后续实验将在此基础上，进一步学习 Linux 文件与权限管理、环境变量配置、远程文件传输等高级功能。

## 七、参考资料

1. VirtualBox 官方文档：https://www.virtualbox.org/wiki/Documentation
2. Ubuntu Server 安装指南：https://ubuntu.com/server/docs
3. Ubuntu 22.04 国内镜像源配置：https://mirrors.aliyun.com/ubuntu/
4. Hexo 官方文档：https://hexo.io/docs
