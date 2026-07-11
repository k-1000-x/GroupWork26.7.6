
---
title: 实验八：Web 组件部署（从开发到上线全流程）
date: 2026-07-10
categories: 实验报告
tags:
  - Hexo
  - Nginx
  - CI/CD
  - GitHub
  - 自动化部署
  - 端口转发
---

## 一、实验目的

1. 综合运用前七个实验所学的全部工具与技能
2. 完整体验 Web 静态站点从开发到部署的全过程
3. 掌握 Linux 服务器（Nginx）的安装与配置方法
4. 理解 CI/CD（持续集成/持续部署）的基本概念
5. 实现基于 GitHub + Hexo + Nginx 的自动化部署流程
6. 培养从零搭建完整 Web 服务的能力

## 二、实验环境

| 项目 | 详情 |
|------|------|
| 开发环境 | Windows 11 + VS Code + Node.js 20.x + Git |
| 静态站点生成器 | Hexo v8.1.2 |
| 主题 | NexT v8.12.1 |
| 版本控制 | GitHub（私有仓库） |
| 服务器操作系统 | Ubuntu Server 22.04.5 LTS（虚拟机） |
| Web 服务器 | Nginx 1.18.0 |
| 部署方式 | 手动 git pull + hexo generate + sudo cp |
| 访问方式 | NAT + 端口转发（宿主机 8080 → 虚拟机 80） |
| 组员访问地址 | `http://宿主机IP:8080` |

## 三、实验内容与步骤

### 3.1 整体架构设计

本实验的整体架构分为四个层次：

```
┌─────────────────────────────────────────────────────────────────┐
│                        开发阶段（主机）                         │
├─────────────────────────────────────────────────────────────────┤
│  组员各自在本地写 Markdown 文章 → git push 到 GitHub           │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                        存储阶段（GitHub）                       │
├─────────────────────────────────────────────────────────────────┤
│  GitHub 私有仓库作为云端中央代码库，存储所有源码                │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                        构建阶段（虚拟机）                       │
├─────────────────────────────────────────────────────────────────┤
│  git pull → npm install → hexo generate → 生成 public/        │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                        部署阶段（虚拟机）                       │
├─────────────────────────────────────────────────────────────────┤
│  sudo cp -r public/* /var/www/html/ → Nginx 提供服务           │
│  用户通过 http://宿主机IP:8080 访问                             │
└─────────────────────────────────────────────────────────────────┘
```

> 📸 **此处可插入截图1**：整体架构图

### 3.2 第一阶段：源码管理与协作开发

#### 3.2.1 创建 GitHub 仓库

1. 登录 GitHub，点击右上角 **“+”** → **“New repository”**
2. Repository name 填 `GroupWork26.7.6`
3. 选择 **Private**（私有仓库）
4. **不要勾选** “Add a README file”
5. 点击 **“Create repository”**

#### 3.2.2 邀请组员协作

1. 进入仓库页面 → **Settings** → **Collaborators**
2. 点击 **“Add people”**，输入组员的 GitHub 用户名
3. 组员接受邀请后即拥有推送权限

#### 3.2.3 组员分支开发流程

每个组员在自己电脑上：

```bash
# 克隆仓库
git clone https://github.com/k-1000-x/GroupWork26.7.6.git
cd GroupWork26.7.6

# 安装依赖
npm install

# 创建个人分支
git checkout -b chennian

# 写文章
hexo new "文章标题"
# 编辑 source/_posts/ 下的 .md 文件

# 提交
git add .
git commit -m "chennian: 添加文章"
git push -u origin chennian
```

#### 3.2.4 Pull Request 合并

1. 组员在 GitHub 上创建 Pull Request（`chennian` → `main`）
2. 组长审核后点击 **“Merge pull request”**
3. 所有组员拉取最新代码：

```bash
git checkout main
git pull origin main
```

> 📸 **此处可插入截图2**：GitHub 协作流程（PR 列表）

### 3.3 第二阶段：服务器环境准备

#### 3.3.1 安装 Nginx

在虚拟机中执行：

```bash
sudo apt update
sudo apt install nginx -y
```

#### 3.3.2 验证 Nginx 安装

```bash
sudo systemctl status nginx
```

确认显示 `active (running)`。

#### 3.3.3 配置 Nginx 站点

创建站点配置文件：

```bash
sudo tee /etc/nginx/sites-available/blog <<EOF
server {
    listen 80;
    server_name _;
    root /var/www/html;
    index index.html;

    location / {
        try_files \$uri \$uri/ =404;
    }
}
EOF
```

启用站点：

```bash
sudo ln -s /etc/nginx/sites-available/blog /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

> 📸 **此处可插入截图3**：Nginx 配置与状态

### 3.4 第三阶段：部署

#### 3.4.1 在虚拟机中拉取代码

```bash
cd ~
git clone https://github.com/k-1000-x/GroupWork26.7.6.git
cd GroupWork26.7.6
```

#### 3.4.2 安装项目依赖

```bash
npm install
```

#### 3.4.3 生成静态网页

```bash
hexo generate
```

`public/` 目录即完整的静态站点文件。

#### 3.4.4 部署到 Nginx

```bash
sudo cp -r public/* /var/www/html/
```

> 📸 **此处可插入截图4**：部署命令执行结果

### 3.5 第三阶段：访问验证

#### 3.5.1 端口转发配置

由于虚拟机使用 NAT 模式，需要配置端口转发让组员访问：

1. 关闭虚拟机：`sudo shutdown now`
2. VirtualBox → 设置 → 网络 → 高级 → 端口转发
3. 添加规则：
   - **名称**：`web`
   - **协议**：`TCP`
   - **主机端口**：`8080`
   - **子系统端口**：`80`
4. 启动虚拟机

#### 3.5.2 查看宿主机 IP

在 Windows CMD 中：

```bash
ipconfig
```

找到 WLAN 或以太网的 IPv4 地址（如 `10.150.178.86`）。

#### 3.5.3 访问验证

组员在浏览器访问：

```
http://10.150.178.86:8080
```

应看到 Hexo 博客首页。

> 📸 **此处可插入截图5**：浏览器访问页面

### 3.6 第四阶段：自动化更新部署

#### 3.6.1 日常更新流程

**组员推送新文章：**

```bash
git add .
git commit -m "添加新文章"
git push origin main
```

**组长在虚拟机中更新部署：**

```bash
cd ~/GroupWork26.7.6
git pull
npm install          # 如有新依赖
hexo clean
hexo generate
sudo cp -r public/* /var/www/html/
```

> 📸 **此处可插入截图6**：更新部署流程

#### 3.6.2 自动化部署脚本（可选优化）

创建部署脚本 `/home/groupwork/deploy.sh`：

```bash
#!/bin/bash
cd ~/GroupWork26.7.6
git pull
npm install
hexo clean
hexo generate
sudo cp -r public/* /var/www/html/
echo "部署完成！"
```

赋予执行权限：

```bash
chmod +x ~/deploy.sh
```

每次更新只需执行：

```bash
./deploy.sh
```

### 3.7 第五阶段：CI/CD 自动化（GitHub Actions）

#### 3.7.1 创建工作流文件

在项目根目录创建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy to Server

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm install

      - name: Build static site
        run: npx hexo generate

      - name: Deploy via SSH
        uses: appleboy/scp-action@v0.1.7
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USER }}
          password: ${{ secrets.SERVER_PASSWORD }}
          source: "public/"
          target: "/var/www/html/"
          strip_components: 1
```

#### 3.7.2 配置 GitHub Secrets

在 GitHub 仓库 → **Settings** → **Secrets** → **Actions** 中添加：

| Secret 名称 | 值 |
|-------------|-----|
| `SERVER_HOST` | 虚拟机 IP 地址 |
| `SERVER_USER` | `groupwork` |
| `SERVER_PASSWORD` | `123456` |

#### 3.7.3 触发自动化部署

当组员向 `main` 分支推送代码时，GitHub Actions 自动执行构建并部署到服务器。

> 📸 **此处可插入截图7**：GitHub Actions 运行成功

## 四、实验结果

通过本次实验，成功完成了以下目标：

| 检查项 | 状态 |
|--------|------|
| GitHub 私有仓库创建及组员邀请 | ✅ |
| 组员分支开发与 PR 合并流程 | ✅ |
| Nginx 安装与站点配置 | ✅ |
| Hexo 静态站点生成与部署 | ✅ |
| 端口转发配置（宿主机 8080 → 虚拟机 80） | ✅ |
| 组员通过 `宿主机IP:8080` 访问站点 | ✅ |
| 自动化更新部署流程（git pull → hexo g → cp） | ✅ |
| CI/CD 工作流配置（GitHub Actions） | ✅ |
| 全流程串联（开发 → 存储 → 构建 → 部署） | ✅ |

## 五、遇到的问题与解决方案

| 问题现象 | 可能原因 | 解决方案 |
|----------|----------|----------|
| 宿主机无法访问 `10.0.2.15:80` | NAT 模式下端口未映射 | 配置端口转发（宿主机 8080 → 虚拟机 80） |
| 组员访问 `宿主机IP:8080` 超时 | Windows 防火墙拦截 | 暂时关闭防火墙测试，或添加入站规则放行 8080 端口 |
| 端口转发后仍无法访问 | 宿主机 IP 地址变化 | 重新 `ipconfig` 查新 IP，更新发给组员 |
| `sudo cp -r public/* /var/www/html/` 权限被拒 | `/var/www/html/` 属 root 所有 | 执行 `sudo chown -R groupwork:groupwork /var/www/html/` 更改权限 |
| Nginx 显示默认页面而非博客 | 站点配置未启用或优先级问题 | 检查 `/etc/nginx/sites-enabled/` 中是否有博客站点配置 |
| 虚拟机重启后 IP 变化 | NAT 模式 DHCP 分配 | 重新 `ip addr` 查 IP，通知组员更新 |
| GitHub Actions 部署失败 | Secrets 未配置或网络不通 | 检查 Secrets 中的服务器 IP、用户名、密码是否正确 |
| `hexo generate` 报 ESM 错误 | Node.js 版本与 Hexo 不兼容 | 升级 Node.js 到 20.x，更新 Hexo：`npm install hexo@latest` |
| 页面样式丢失 | NexT 主题 CDN 资源加载失败 | 在主题配置中设置 `vendors: local` |

## 六、实验总结

本次实验是《软件开发工具实践》课程的收尾实验，综合运用了前七个实验所学全部技能，完整实现了从代码编写到网站上线的全流程。

### 全流程回顾

| 阶段 | 涉及实验 | 核心操作 |
|------|----------|----------|
| 环境准备 | 实验一、二 | 安装 Node.js、Git、VS Code、Hexo |
| 内容编写 | 实验二、四 | Markdown 写文章，Hexo 本地预览 |
| 版本管理 | 实验二、四 | Git add/commit/push，GitHub 协作 |
| 服务器搭建 | 实验三、五、六 | VirtualBox + Ubuntu Server，用户与权限管理 |
| 远程管理 | 实验七 | SSH 免密登录，SCP/rsync 文件传输 |
| 部署上线 | 实验八 | Nginx 配置，hexo generate，静态文件部署 |

### 关键收获

1. **工具链的完整理解**：从本地开发（VS Code + Hexo）到版本管理（Git + GitHub）到服务器部署（Ubuntu + Nginx），每个环节都是完整链路中不可或缺的一环。

2. **自动化是效率的核心**：通过 CI/CD（GitHub Actions）将构建和部署流程自动化，减少了人工操作的出错风险，提高了交付速度。

3. **团队协作的规范化**：通过分支管理、Pull Request 审核、自动化部署，实现了“组员写文章 → 组长审核 → 自动上线”的规范化流程。

4. **问题排查能力**：本课程中遇到的网络问题、DNS 问题、端口冲突、版本兼容、权限配置等问题，都是实际运维中的常见挑战，解决这些问题的经验非常宝贵。

### 后续方向

- 升级为 HTTPS（SSL 证书配置）
- 添加自定义域名
- 集成评论系统（如 Giscus）
- 完善 CI/CD 流程（自动测试、自动回滚）

## 七、参考资料

1. Hexo 官方文档：https://hexo.io/docs/
2. Nginx 官方文档：https://nginx.org/en/docs/
3. GitHub Actions 文档：https://docs.github.com/actions
4. NexT 主题文档：https://theme-next.js.org/docs/
5. Ubuntu 服务器指南：https://ubuntu.com/server/docs
6. SSH 远程管理指南：https://www.openssh.com/manual.html
7. rsync 官方文档：https://rsync.samba.org/documentation.html