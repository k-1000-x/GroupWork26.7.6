
---
title: 实验一：Web开发环境搭建与静态站点构建
date: 2026-07-08
categories: 实验报告
tags:
  - Hexo
  - Node.js
  - Git
---

## 一、实验目的

1. 掌握 Node.js、Git、Hexo 的安装与配置方法
2. 理解静态站点生成器（SSG）的工作原理
3. 能够使用 Hexo 在本地构建并预览静态博客站点
4. 熟悉 Web 开发环境的基础工具链

## 二、实验环境

| 项目 | 详情 |
|------|------|
| 操作系统 | Windows 11 |
| Node.js | v24.18.0（LTS 版本） |
| Git | v2.55.0.2 |
| Hexo CLI | v4.3.2 |
| 代码编辑器 | Visual Studio Code |
| 浏览器 | Microsoft Edge |

## 三、实验内容与步骤

### 3.1 安装 Node.js 与 npm

1. 访问 Node.js 官网（或国内镜像站 `https://nodejs.cn/download/`），下载 LTS 版本的 Windows 安装包（`node-v24.18.0-x64.msi`）。
2. 双击安装包，在安装过程中**务必勾选“Add to PATH”**，其余保持默认，一路点击“Next”完成安装。
3. 打开命令提示符（CMD），输入以下命令验证安装是否成功：

```bash
node -v
npm -v
```

4. 配置 npm 国内镜像源，加速后续依赖包的下载：

```bash
npm config set registry https://registry.npmmirror.com
```

{% asset_img 01-node-version.png Node.js版本验证 %}

### 3.2 安装 Git 版本管理工具

1. 访问 Git 官网 `https://git-scm.com/download/win`，下载 Windows 版本安装包。
2. 双击安装包，关键配置项如下：
   - **Select Components**：保持默认，直接点击 Next
   - **Choosing the default editor**：选择 **“Use Visual Studio Code as Git's default editor”**
   - **Adjusting your PATH environment**：选择 **“Git from the command line and also from 3rd-party software”**（中间选项）
   - **Configuring the line ending conversions**：保持默认 **“Checkout Windows-style, commit Unix-style line endings”**
   - **Choosing HTTPS transport backend**：选择 **“Use the OpenSSL library”**
   - **Configuring the terminal emulator**：保持默认 **“Use MinTTY”**
   - **Choose the default behavior of `git pull`**：选择 **“Merge”**
   - **Choosing a credential helper**：保持默认
3. 其余保持默认，一路 Next 完成安装。
4. 验证安装并配置用户信息：

```bash
git --version
git config --global user.name "你的姓名"
git config --global user.email "你的QQ邮箱@qq.com"
```

{% asset_img 02-git-version.png Git版本验证 %}

### 3.3 安装 Hexo CLI 并初始化博客项目

1. 全局安装 Hexo 命令行工具：

```bash
npm install -g hexo-cli
hexo -v
```

2. 在 D 盘创建项目目录并初始化博客：

```bash
D:
mkdir Blog
cd Blog
hexo init my-blog
```

> **说明**：如果 GitHub 连接超时，Hexo 会自动切换为内置缓存复制，不影响最终结果，耐心等待即可。

3. 进入项目目录并安装项目依赖：

```bash
cd my-blog
npm install
```

{% asset_img 03-hexo-version.png Hexo版本信息 %}

### 3.4 本地预览博客

1. 在项目目录下启动本地预览服务：

```bash
hexo server
```

看到如下提示表示服务启动成功：

```
INFO Hexo is running at http://localhost:4000 . Press Ctrl+C to stop.
```

2. 打开浏览器，访问 `http://localhost:4000`，即可看到 Hexo 默认的博客首页。

{% asset_img 04-localhost-preview.png 本地预览博客首页 %}

### 3.5 创建并发布一篇博客文章

1. 使用以下命令创建一篇新文章：

```bash
hexo new "我的第一篇文章"
```

2. 用 VS Code 打开 `source/_posts/我的第一篇文章.md`，修改 Front-matter 信息和正文内容后保存。

3. 重新生成静态页面并再次预览：

```bash
hexo generate
hexo server
```

{% asset_img 05-vscode-markdown.png VS Code编辑Markdown %}

### 3.6 将源码推送到 GitHub（小组协作准备）

1. 在 GitHub 创建私有仓库，命名为 `GroupWork26.7.6`

2. 初始化 Git 并关联远程仓库：

```bash
git init
git remote add origin https://github.com/你的用户名/GroupWork26.7.6.git
```

3. 创建 `.gitignore` 文件，忽略不需要上传的文件夹：

```bash
echo "node_modules/" >> .gitignore
echo "public/" >> .gitignore
echo ".deploy_git/" >> .gitignore
```

4. 提交并推送到 GitHub：

```bash
git add .
git commit -m "初始化Hexo博客项目"
git branch -M main
git push -u origin main
```

{% asset_img 06-github-repo.png GitHub仓库文件列表 %}

## 四、实验结果

通过本次实验，成功完成了以下目标：

| 检查项 | 状态 |
|--------|------|
| Node.js 安装成功（`node -v` 正常输出） | ✅ |
| npm 安装成功（`npm -v` 正常输出） | ✅ |
| Git 安装成功（`git --version` 正常输出） | ✅ |
| Hexo CLI 安装成功（`hexo -v` 正常输出） | ✅ |
| 本地预览服务正常（`hexo server` 可运行） | ✅ |
| 浏览器可访问 `http://localhost:4000` | ✅ |
| 新文章创建并显示在博客首页 | ✅ |
| 源码已推送到 GitHub 远程仓库 | ✅ |

## 五、遇到的问题与解决方案

| 问题现象 | 可能原因 | 解决方案 |
|----------|----------|----------|
| `nodejs.org` 官网打不开 | 海外服务器访问不稳定 | 改用国内镜像站 `https://nodejs.cn/download/` |
| `hexo init` 克隆超时报错 | GitHub 网络连接被重置 | Hexo 自动切换为内置缓存复制，等待即可 |
| Git 安装后 `git` 命令不被识别 | 安装时未勾选“Add to PATH” | 重新运行安装程序，确保勾选“Add to PATH” |
| `git push` 提示认证失败 | 未配置 Personal Access Token | 在 GitHub 生成 Token，替代密码使用 |
| VS Code 无法识别 `git` 命令 | VS Code 终端未加载 PATH | 重启 VS Code 或重新加载窗口 |

## 六、实验总结

本次实验是《软件开发工具实践》课程的起点，完成了 Web 开发环境的基础搭建。通过安装 Node.js、Git 和 Hexo，成功在本地运行了一个静态博客站点，并将源码推送到 GitHub 进行版本管理。

关键收获：
1. **理解了工具链的依赖关系**：Hexo 依赖 Node.js 运行，npm 负责包管理，Git 负责版本控制
2. **掌握了静态站点生成器的工作流程**：Markdown 编写 → `hexo generate` → 生成静态 HTML → `hexo server` 本地预览
3. **熟悉了 Git 的基本协作流程**：`add` → `commit` → `push` → `pull`

后续实验将在本实验搭建的环境基础上，进一步完成 Linux 虚拟机部署、Nginx 配置、远程管理等内容。