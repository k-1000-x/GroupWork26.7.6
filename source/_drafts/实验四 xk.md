
---
title: 实验四：Web站点构建（Hexo静态博客）
date: 2026-07-10
categories: 实验报告
tags:
  - Hexo
  - 静态站点
  - NexT主题
  - GitHub
  - Markdown
---

## 一、实验目的

1. 掌握静态站点生成器（SSG）的工作原理与基本用法
2. 学会使用 Hexo 初始化博客项目、编写文章、更换主题
3. 理解 Markdown 在内容创作中的优势
4. 掌握站点配置文件的常用参数设置
5. 了解将静态站点部署到 Web 服务器的基本流程

## 二、实验环境

| 项目 | 详情 |
|------|------|
| 操作系统 | Windows 11 / Ubuntu Server 22.04 |
| 静态站点生成器 | Hexo v8.1.2 |
| Node.js | v20.19.0 |
| 主题 | NexT v8.12.1 |
| 代码编辑器 | Visual Studio Code |
| 版本控制 | Git + GitHub |
| Web 服务器 | Nginx（虚拟机） |

## 三、实验内容与步骤

### 3.1 安装 Hexo CLI

在宿主机（Windows）和虚拟机（Ubuntu）中分别安装 Hexo 命令行工具：

```bash
npm install -g hexo-cli
hexo -v
```

> 📸 **此处可插入截图1**：Hexo 版本信息

### 3.2 初始化博客项目

在宿主机 D 盘创建项目目录并初始化：

```bash
D:
mkdir Blog
cd Blog
hexo init my-blog
cd my-blog
npm install
```

初始化完成后，项目目录结构如下：

```
my-blog/
├── _config.yml      # 站点主配置文件
├── package.json     # 项目依赖清单
├── scaffolds/       # 文章模板
├── source/          # 内容源文件
│   └── _posts/      # 文章存放目录
└── themes/          # 主题目录
```

> 📸 **此处可插入截图2**：项目目录结构

### 3.3 本地预览博客

启动本地预览服务：

```bash
hexo server
```

访问 `http://localhost:4000`，看到 Hexo 默认博客首页。

> 📸 **此处可插入截图3**：默认博客首页

### 3.4 更换为 NexT 主题

NexT 是 Hexo 生态中最流行的主题之一，功能丰富、文档完善。

**第一步：克隆 NexT 主题到 themes 目录**

```bash
git clone https://github.com/theme-next/hexo-theme-next themes/next
```

**第二步：修改站点配置文件 `_config.yml`**

找到 `theme` 字段，改为：

```yaml
theme: next
```

**第三步：启用 NexT 主题的 Tabs 标签功能**

编辑 `themes/next/_config.yml`，找到：

```yaml
tabs:
  enable: false
```

改为：

```yaml
tabs:
  enable: true
```

**第四步：配置资源引用方式（避免 CDN 加载失败）**

在 `themes/next/_config.yml` 中，将 `vendors` 设置为 `local`：

```yaml
vendors: local
```

或添加：

```yaml
vendors: local
```

> 📸 **此处可插入截图4**：NexT 主题配置文件修改

### 3.5 编写实验报告文章

使用 Hexo 命令创建新文章：

```bash
hexo new "实验一-Web开发环境搭建"
```

生成的 `.md` 文件位于 `source/_posts/` 目录下，编辑内容，使用 Markdown 语法编写实验报告。

**Front-matter 示例：**

```yaml
---
title: 实验一：Web开发环境搭建与静态站点构建
date: 2026-07-08
categories: 实验报告
tags:
  - Hexo
  - Node.js
  - Git
---
```

**使用 Tabs 标签汇总多篇报告**

创建 `实验报告合集.md`，使用 NexT 主题的 Tabs 标签：

```markdown
---
title: 软件开发工具实践 - 实验报告合集
date: 2026-07-08
categories: 实验报告
tags:
  - 实验报告
  - 合集
---

{% tabs 实验报告合集, 1 %}

<!-- tab 实验一：Web开发环境搭建 -->
（实验一内容）
<!-- endtab -->

<!-- tab 实验二：代码编辑与管理 -->
（实验二内容）
<!-- endtab -->

<!-- tab 实验三：虚拟机安装与配置 -->
（实验三内容）
<!-- endtab -->

{% endtabs %}
```

> 📸 **此处可插入截图5**：Markdown 编辑界面

### 3.6 生成静态页面

```bash
hexo generate
```

`public/` 目录下生成完整的静态 HTML 文件。

### 3.7 将站点部署到 GitHub（源码托管）

**第一步：初始化 Git 仓库**

```bash
git init
git remote add origin https://github.com/k-1000-x/GroupWork26.7.6.git
```

**第二步：创建 `.gitignore` 文件**

```
node_modules/
public/
.deploy_git/
```

**第三步：提交并推送**

```bash
git add .
git commit -m "初始化Hexo博客项目"
git branch -M main
git push -u origin main
```

> 📸 **此处可插入截图6**：GitHub 仓库文件列表

### 3.8 部署到虚拟机 Nginx 服务器

**第一步：在虚拟机中拉取代码**

```bash
cd ~/GroupWork26.7.6
git pull
```

**第二步：安装项目依赖并生成静态文件**

```bash
npm install
hexo generate
```

**第三步：将静态文件复制到 Nginx 网站根目录**

```bash
sudo cp -r public/* /var/www/html/
```

**第四步：通过端口转发访问站点**

宿主机浏览器访问 `http://宿主机IP:8080`，即可看到博客页面。

> 📸 **此处可插入截图7**：部署后的博客首页（含 Tabs 标签切换效果）

## 四、实验结果

通过本次实验，成功完成了以下目标：

| 检查项 | 状态 |
|--------|------|
| Hexo 本地预览正常 | ✅ |
| NexT 主题安装并启用成功 | ✅ |
| Tabs 标签功能正常 | ✅ |
| 多篇实验报告通过 Tabs 汇总 | ✅ |
| 源码推送到 GitHub 远程仓库 | ✅ |
| 虚拟机拉取并生成静态页面 | ✅ |
| Nginx 正常展示博客页面 | ✅ |
| 组员可通过 `宿主机IP:8080` 访问 | ✅ |

## 五、遇到的问题与解决方案

| 问题现象 | 可能原因 | 解决方案 |
|----------|----------|----------|
| `hexo init` 克隆超时 | GitHub 访问不稳定 | 等待 Hexo 自动切换为内置缓存复制，或使用 `--no-clone` 参数 |
| `tabs` 标签报错 `unknown block tag` | 主题未启用 Tabs 功能或未正确加载主题 | 安装 NexT 主题，并在 `_config.yml` 中设置 `theme: next`，同时启用主题内的 `tabs.enable: true` |
| NexT 主题页面无样式、布局错乱 | 主题默认使用国外 CDN 资源，国内加载失败 | 在主题配置中设置 `vendors: local`，使用本地资源 |
| `git push` 认证失败 | 未配置 Personal Access Token | 在 GitHub 生成 Token，替代密码使用 |
| 虚拟机 `hexo generate` 报 ESM 错误 | Node.js 版本与 Hexo 版本不兼容 | 升级 Node.js 到 20.x，并更新 Hexo 到最新版：`npm install hexo@latest` |
| 宿主机无法访问 `宿主机IP:8080` | Windows 防火墙拦截 | 暂时关闭防火墙测试，或添加入站规则放行 8080 端口 |

## 六、实验总结

本次实验完成了从零构建 Hexo 静态博客站点的全过程，涵盖了项目初始化、主题配置、内容编写、版本管理、服务器部署等环节。

### 关键收获

1. **静态站点生成器的工作流程**：Markdown 源文件 → `hexo generate` → 静态 HTML → 部署到 Web 服务器，整个过程清晰高效
2. **主题的重要性**：选择合适的主题不仅影响站点外观，还决定了可用的功能扩展（如 Tabs 标签、数学公式等）
3. **版本管理是团队协作的基石**：通过 Git 和 GitHub，组员可以各自编写内容，由组长统一拉取、生成、部署，实现高效分工
4. **Tabs 标签在实验报告中的应用**：将多篇实验报告汇总到一篇文章中，通过页签切换查看，既整洁又便于归档

后续实验中，将继续使用 Hexo 编写和发布更多内容，并逐步完善站点的功能和外观。

## 七、参考资料

1. Hexo 官方文档：https://hexo.io/docs/
2. NexT 主题文档：https://theme-next.js.org/docs/
3. GitHub 文档：https://docs.github.com/
4. Nginx 官方文档：https://nginx.org/en/docs/