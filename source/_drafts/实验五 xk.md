
---
title: 实验五：Linux 环境配置与基本操作
date: 2026-07-10
categories: 实验报告
tags:
  - Linux
  - 命令行
  - 用户管理
  - 权限管理
  - 环境变量
---

## 一、实验目的

1. 掌握 Linux 命令行的基本操作（文件操作、目录管理、系统查询）
2. 学会使用命令行进行用户与组的管理
3. 理解 Linux 文件权限模型，掌握 `chmod`、`chown` 等权限管理命令
4. 掌握环境变量的查看、设置与管理方法
5. 熟悉 Linux 系统配置文件的编辑与作用范围

## 二、实验环境

| 项目 | 详情 |
|------|------|
| 操作系统 | Ubuntu Server 22.04.5 LTS |
| 内核版本 | Linux 5.15.0-185-generic |
| Shell 环境 | Bash 5.1.16 |
| 用户身份 | groupwork（普通用户，具有 sudo 权限） |
| 网络模式 | NAT（可访问外网） |
| 远程访问 | SSH（端口 22） |

## 三、实验内容与步骤

### 3.1 登录系统与查看基本信息

通过虚拟机控制台或 SSH 远程登录系统。

**查看当前用户信息：**

```bash
whoami
id
```

**查看系统信息：**

```bash
uname -a
hostname
lsb_release -a
```

**查看当前工作目录：**

```bash
pwd
```

> 📸 **此处可插入截图1**：系统信息查询结果

### 3.2 文件与目录的基本操作

**列出目录内容（详细格式，易读大小）：**

```bash
ls -lh
```

**切换目录：**

```bash
cd ~          # 切换到用户主目录
cd /etc       # 切换到 /etc 目录
cd -          # 返回上一个目录
```

**创建目录：**

```bash
mkdir test_dir
mkdir -p test_dir/sub_dir   # 递归创建多级目录
```

**创建空文件：**

```bash
touch test.txt
```

**复制文件：**

```bash
cp test.txt test_copy.txt
cp -r test_dir test_dir_backup   # 递归复制目录
```

**移动/重命名文件：**

```bash
mv test.txt /tmp/
mv test_copy.txt renamed.txt
```

**删除文件：**

```bash
rm test.txt
rm -rf test_dir   # 强制递归删除目录（谨慎使用）
```

> 📸 **此处可插入截图2**：文件操作命令执行结果

### 3.3 查看系统资源状态

**查看内存使用情况：**

```bash
free -h
```

**查看磁盘使用情况：**

```bash
df -h
```

**查看当前目录下各子目录大小：**

```bash
du -sh *
```

**查看进程和资源占用：**

```bash
top
```

（按 `q` 退出）

> 📸 **此处可插入截图3**：系统资源状态

### 3.4 用户与组管理

**查看用户与组配置文件：**

```bash
cat /etc/passwd          # 用户账户信息
sudo cat /etc/shadow     # 加密密码信息（仅 root 可读）
cat /etc/group           # 用户组信息
```

**创建新用户：**

```bash
sudo adduser testuser
```

系统会提示设置密码（本实验中统一设为 `123456`），其余信息直接回车跳过。

**赋予用户 `sudo` 权限：**

```bash
sudo usermod -aG sudo testuser
```

**查看用户所属组：**

```bash
groups testuser
```

**切换用户：**

```bash
su - testuser
```

（输入 `testuser` 的密码）

**删除用户：**

```bash
sudo deluser --remove-home testuser
```

> 📸 **此处可插入截图4**：用户创建与管理

### 3.5 文件权限管理

**查看文件权限：**

```bash
ls -l test.txt
```

输出格式：`-rw-r--r--`，共 10 个字符：
- 第 1 位：文件类型（`-` 普通文件，`d` 目录）
- 第 2-4 位：所有者权限
- 第 5-7 位：所属组权限
- 第 8-10 位：其他用户权限

**权限数字对照表：**

| 数字 | 权限 | 含义 |
|------|------|------|
| 7 | rwx | 读 + 写 + 执行 |
| 6 | rw- | 读 + 写 |
| 5 | r-x | 读 + 执行 |
| 4 | r-- | 只读 |
| 0 | --- | 无权限 |

**使用数字法修改权限：**

```bash
chmod 755 test.sh          # 所有者 rwx，组 r-x，其他 r-x
chmod 644 test.txt         # 所有者 rw-，组 r--，其他 r--
```

**使用符号法修改权限：**

```bash
chmod u+x test.sh          # 所有者增加执行权限
chmod g-w test.txt         # 去掉组的写权限
chmod o+r test.txt         # 其他用户增加读权限
```

**修改文件所有者和所属组：**

```bash
sudo chown testuser:testuser test.txt
```

**递归修改目录权限：**

```bash
sudo chown -R testuser:testuser /home/testuser
```

> 📸 **此处可插入截图5**：权限管理命令执行结果

### 3.6 环境变量的查看与设置

**查看所有环境变量：**

```bash
env
```

**查看常用环境变量：**

```bash
echo $PATH          # 可执行文件搜索路径
echo $HOME          # 用户主目录
echo $USER          # 当前用户名
echo $SHELL         # 当前 Shell 类型
```

**临时设置环境变量：**

```bash
export MY_VAR="Hello Linux"
echo $MY_VAR
```

**删除环境变量：**

```bash
unset MY_VAR
```

**临时追加 PATH：**

```bash
export PATH=$PATH:/home/groupwork/mybin
```

> 📸 **此处可插入截图6**：环境变量操作

### 3.7 永久设置环境变量

**用户级配置文件：**

| 配置文件 | 加载时机 | 作用范围 |
|----------|----------|----------|
| `~/.bashrc` | 每次打开新终端时执行 | 当前用户 |
| `~/.profile` | 用户登录时执行一次 | 当前用户 |
| `~/.bash_profile` | 用户登录时执行（优先于 profile） | 当前用户 |

**编辑 `~/.bashrc` 添加自定义配置：**

```bash
nano ~/.bashrc
```

在文件末尾添加：

```bash
export MY_CUSTOM_VAR="自定义变量"
alias ll='ls -alF'
```

保存后使配置生效：

```bash
source ~/.bashrc
```

**验证：**

```bash
echo $MY_CUSTOM_VAR
ll
```

> 📸 **此处可插入截图7**：`.bashrc` 配置及生效

### 3.8 网络配置

**查看网络接口信息：**

```bash
ip addr
```

**查看路由表：**

```bash
ip route
```

**测试网络连通性：**

```bash
ping 8.8.8.8 -c 4
ping github.com -c 2
```

**配置 DNS（临时）：**

```bash
echo "nameserver 114.114.114.114" | sudo tee /etc/resolv.conf
echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.conf
```

> 📸 **此处可插入截图8**：网络配置与测试

## 四、实验结果

通过本次实验，成功完成了以下目标：

| 检查项 | 状态 |
|--------|------|
| 文件与目录基本操作（ls、cd、mkdir、cp、mv、rm） | ✅ |
| 系统资源查看（free、df、du、top） | ✅ |
| 用户创建与管理（adduser、usermod、deluser） | ✅ |
| 文件权限管理（chmod、chown） | ✅ |
| 环境变量查看与临时设置（env、echo、export） | ✅ |
| 永久环境变量配置（~/.bashrc） | ✅ |
| 网络配置与测试（ip addr、ping） | ✅ |

## 五、遇到的问题与解决方案

| 问题现象 | 可能原因 | 解决方案 |
|----------|----------|----------|
| `sudo: command not found` | 当前用户不在 `sudo` 组中 | 以 root 用户登录或使用 `su -` 切换到 root，执行 `usermod -aG sudo 用户名` |
| `Permission denied` | 权限不足，操作需要管理员权限 | 使用 `sudo` 提权执行命令 |
| 修改 `~/.bashrc` 后配置不生效 | 未执行 `source` 重新加载 | 执行 `source ~/.bashrc` 或重新登录终端 |
| `ping` 报 `Temporary failure in name resolution` | DNS 配置错误或未配置 | 手动配置 DNS：`echo "nameserver 114.114.114.114" \| sudo tee /etc/resolv.conf` |
| `apt update` 卡在 `0% [Connecting...]` | 软件源连接慢或无法访问 | 替换为国内镜像源（阿里云/清华源） |
| `chown: invalid user` | 用户名不存在或拼写错误 | 确认用户名正确：`cat /etc/passwd \| grep 用户名` |

## 六、实验总结

本次实验深入学习了 Linux 命令行的基本操作，涵盖了文件管理、用户管理、权限控制、环境变量配置和网络配置等核心内容。

### 关键收获

1. **文件操作是 Linux 使用的基础**：`ls`、`cd`、`cp`、`mv`、`rm` 等命令是日常操作中最常用的工具，熟练掌握这些命令能大幅提高工作效率。

2. **用户与权限模型是 Linux 的安全基石**：理解用户、组、文件权限之间的关系，有助于在多用户环境下合理分配资源，避免权限滥用。

3. **环境变量配置体现了系统的灵活性**：通过修改 `~/.bashrc` 等配置文件，可以个性化定制 Shell 环境，提高工作效率。

4. **网络配置是服务器运维的基本功**：理解 `ip addr`、`ping`、DNS 配置等网络工具的使用，是后续网络服务部署的前提。

后续实验将继续在本次配置好的 Linux 环境基础上，学习远程管理（SSH、SCP、rsync）和软件部署（Nginx、自动化部署）等高级主题。

## 七、参考资料

1. Ubuntu 官方文档：https://ubuntu.com/server/docs
2. Linux 命令行大全：https://linuxcommand.org/
3. Bash 参考手册：https://www.gnu.org/software/bash/manual/
4. 清华大学镜像站：https://mirrors.tuna.tsinghua.edu.cn/
