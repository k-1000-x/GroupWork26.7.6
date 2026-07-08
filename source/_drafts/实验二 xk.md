
---
title: 实验二：代码编辑与管理（VS Code + Git + Markdown）
date: 2026-07-08
categories: 实验报告
tags:
  - VS Code
  - Markdown
  - Git
  - 版本管理
---

## 一、实验目的

1. 掌握 Visual Studio Code（VS Code）的安装与基本配置
2. 学会安装和使用 Markdown 相关插件，提升写作效率
3. 掌握 Markdown 基础语法，能够使用 VS Code 完成文档的编辑和渲染
4. 熟悉 Git 的日常操作流程（add / commit / log / push / pull）
5. 理解代码编辑器与集成开发环境（IDE）的区别

## 二、实验环境

| 项目 | 详情 |
|------|------|
| 操作系统 | Windows 11 |
| 代码编辑器 | Visual Studio Code |
| 版本管理工具 | Git v2.55.0.2 |
| Markdown 插件 | Markdown All in One、Markdown Preview Enhanced |
| 浏览器 | Microsoft Edge |

## 三、实验内容与步骤

### 3.1 安装 Visual Studio Code

1. 访问 VS Code 官网 `https://code.visualstudio.com/`，下载 Windows 版本安装包。
2. 双击安装包，启动安装向导。
3. 在“选择附加任务”页面，**务必勾选以下选项**：
   - 创建桌面快捷方式
   - 将“通过 Code 打开”操作添加到 Windows 资源管理器上下文菜单
   - 将 Code 注册为受支持的文件类型的编辑器
   - **添加到 PATH**（必须勾选）
4. 其余保持默认，点击“下一步”完成安装。

### 3.2 首次运行与界面熟悉

1. 双击桌面上的 VS Code 快捷方式启动软件。
2. 熟悉 VS Code 的主界面布局：

| 区域 | 功能 |
|------|------|
| 活动栏（左侧最窄竖条） | 包含资源管理器、搜索、源代码管理、扩展等图标 |
| 侧边栏 | 显示当前活动栏工具的具体内容 |
| 编辑器区域（中央） | 用于编辑文件的主要区域 |
| 面板（底部） | 显示终端、输出、问题等 |
| 状态栏（底部最下方） | 显示当前文件信息、Git 分支、编码格式等 |



### 3.3 安装 Markdown 相关插件

#### 3.3.1 打开扩展面板

- 点击左侧活动栏中的**扩展图标**（四个小方块组成），或使用快捷键 `Ctrl + Shift + X`

#### 3.3.2 安装核心插件

| 插件名称 | 功能 |
|----------|------|
| **Markdown All in One** | 目录生成、快捷键支持、自动完成、列表自动延续等 |
| **Markdown Preview Enhanced** | 数学公式（LaTeX）、流程图（Mermaid）、导出为 PDF/HTML |
| **Paste Image**（可选） | 直接粘贴剪贴板中的图片到 Markdown 文件 |
| **markdownlint**（可选） | 检查 Markdown 语法错误和格式规范 |



### 3.4 配置编辑器优化写作体验

1. 开启自动保存：`Ctrl + ,` 打开设置，搜索 `files.autosave`，设置为 `afterDelay`
2. 开启单词折行：搜索 `editor.wordwrap`，设置为 `on`
3. 配置图片粘贴路径（如安装了 Paste Image 插件）：设置 `Paste Image: Path Type` 为 `relative`

### 3.5 创建并编辑第一个 Markdown 文件

1. 新建文件：`Ctrl + N`，保存为 `test.md`
2. 输入以下内容测试基础语法：

```markdown
# 我的第一个 Markdown 文档

## 什么是 Markdown？

Markdown 是一种**轻量级**的标记语言。

## Markdown 的核心特点

- **简洁性**：仅需少量符号即可完成排版
- **可读性**：纯文本仍然清晰易读
- **兼容性**：支持 GitHub、博客平台等多种场景

## 一个代码示例

```javascript
function hello() {
  console.log("Hello, Markdown!");
}
```
```

3. 使用快捷键 `Ctrl + Shift + V` 打开预览窗口，查看渲染效果。

> 📸 **此处可插入截图4**：Markdown 编辑界面与预览效果并排

### 3.6 使用 Markdown 编写实验报告框架

在实验一的基础上，用 Markdown 编写本次实验报告的框架：

```markdown
---
title: 实验二：代码编辑与管理
date: 2026-07-08
categories: 实验报告
tags:
  - VS Code
  - Markdown
  - Git
---

## 一、实验目的
...

## 二、实验环境
...

## 三、实验内容与步骤
...

## 四、实验结果
...

## 五、遇到的问题与解决方案
...

## 六、实验总结
...
```

### 3.7 Git 版本管理操作

#### 3.7.1 配置 Git 用户信息

```bash
git config --global user.name "你的拼音姓名"
git config --global user.email "你的QQ邮箱@qq.com"
```

#### 3.7.2 查看配置是否生效

```bash
git config --global --list
```

#### 3.7.3 将实验报告纳入版本管理

在项目目录下执行：

```bash
git status
git add source/_posts/实验二-代码编辑与管理.md
git commit -m "添加实验二报告"
git push origin main
```

#### 3.7.4 查看提交历史

```bash
git log --oneline
```


## 四、实验结果

通过本次实验，成功完成了以下目标：

| 检查项 | 状态 |
|--------|------|
| VS Code 安装成功并可正常运行 | ✅ |
| Markdown All in One 插件安装成功 | ✅ |
| Markdown Preview Enhanced 插件安装成功 | ✅ |
| Markdown 文件可正常编辑并预览 | ✅ |
| Git 用户信息配置成功 | ✅ |
| 实验报告已提交到 GitHub 仓库 | ✅ |
| `git log` 可查看提交历史 | ✅ |

## 五、遇到的问题与解决方案

| 问题现象 | 可能原因 | 解决方案 |
|----------|----------|----------|
| VS Code 无法识别 `git` 命令 | 安装 VS Code 时未勾选“添加到 PATH” | 重新运行安装程序，勾选“添加到 PATH”；或重启 VS Code |
| 扩展面板搜索不到插件 | 网络问题或搜索关键词不准确 | 检查网络连接；尝试使用准确的插件全名搜索 |
| 预览窗口不显示或显示异常 | 插件未正确安装或未启用 | 检查扩展面板中插件是否已启用；重启 VS Code |
| `git push` 提示认证失败 | 未配置 Personal Access Token | 在 GitHub 生成 Token，替代密码使用 |
| Markdown 表格不对齐 | 表格格式错误 | 检查每行的 `\|` 数量是否一致 |

## 六、实验总结

本次实验完成了 VS Code 代码编辑器的安装与配置，掌握了 Markdown 插件的安装和使用方法，能够熟练使用 Markdown 语法编写结构化的文档。

### 关键收获

1. **VS Code 是轻量级但功能强大的编辑器**：通过插件机制可以灵活扩展功能，满足不同开发场景的需求
2. **Markdown 是技术写作的首选工具**：语法简单、可读性强、兼容性好，适合编写实验报告和技术文档
3. **Git 是团队协作的基础**：掌握 `add` → `commit` → `push` → `pull` 的基本工作流，是后续小组协作的前提
4. **插件配置是提升效率的关键**：自动保存、单词折行、图片粘贴等配置能显著改善写作体验

后续实验将继续使用 VS Code 编写文档，通过 Git 进行版本管理，并与团队协作完成项目开发。

## 七、参考资料

1. Visual Studio Code 官方文档：https://code.visualstudio.com/docs
2. Markdown 官方教程：https://markdownguide.org
3. Git 官方文档：https://git-scm.com/doc
4. Hexo 官方文档：https://hexo.io/docs
