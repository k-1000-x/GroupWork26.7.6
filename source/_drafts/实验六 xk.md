
---
title: 实验六：Linux 账户与权限管理
date: 2026-07-10
categories: 实验报告
tags:
  - Linux
  - 用户管理
  - 权限管理
  - 环境变量
  - 系统配置
---

## 一、实验目的

1. 掌握 Linux 系统用户账户的创建、修改与删除操作
2. 理解用户组的概念，掌握组管理与成员管理
3. 深入理解 Linux 文件权限模型（rwx 权限位与数字表示法）
4. 掌握使用 `chmod`、`chown`、`chgrp` 命令管理文件权限
5. 理解特殊权限（SUID、SGID、Sticky Bit）的作用与配置
6. 掌握 `sudo` 权限的配置方法
7. 掌握系统级与用户级环境变量的配置与管理

## 二、实验环境

| 项目 | 详情 |
|------|------|
| 操作系统 | Ubuntu Server 22.04.5 LTS |
| 内核版本 | Linux 5.15.0-185-generic |
| Shell 环境 | Bash 5.1.16 |
| 用户身份 | groupwork（具有 sudo 权限） |
| 网络模式 | NAT（可访问外网） |
| 远程访问 | SSH（端口 22） |

## 三、实验内容与步骤

### 3.1 查看系统配置文件

Linux 系统的用户和组信息存储在以下配置文件中：

| 文件 | 作用 |
|------|------|
| `/etc/passwd` | 用户账户基本信息 |
| `/etc/shadow` | 用户加密密码信息（仅 root 可读） |
| `/etc/group` | 用户组信息 |
| `/etc/gshadow` | 用户组加密密码信息（仅 root 可读） |
| `/etc/sudoers` | sudo 权限配置 |

**查看用户配置文件：**

```bash
cat /etc/passwd
```

`/etc/passwd` 文件的格式为：
```
用户名:密码占位符:UID:GID:用户描述:主目录:登录Shell
```

例如：
```
root:x:0:0:root:/root:/bin/bash
groupwork:x:1000:1000:GroupWork:/home/groupwork:/bin/bash
```

**查看密码文件（需 sudo 权限）：**

```bash
sudo cat /etc/shadow
```

`/etc/shadow` 文件的格式为：
```
用户名:加密密码:最后修改日期:最短有效期:最长有效期:警告期限:...
```

**查看组配置文件：**

```bash
cat /etc/group
```

`/etc/group` 文件的格式为：
```
组名:密码占位符:GID:成员列表
```

> 📸 **此处可插入截图1**：`/etc/passwd` 和 `/etc/group` 文件内容

### 3.2 用户账户管理

#### 3.2.1 创建新用户

**使用 `adduser` 命令（交互式，推荐）：**

```bash
sudo adduser devuser
```

系统会依次提示：
- 设置密码（本实验统一用 `123456`）
- 用户全名（直接回车跳过）
- 房间号、工作电话、家庭电话、其他（直接回车跳过）
- 确认信息（输入 `Y`）

**使用 `useradd` 命令（底层命令，需手动指定参数）：**

```bash
sudo useradd -m -s /bin/bash -G sudo testuser
```

参数说明：
- `-m`：创建用户主目录
- `-s /bin/bash`：指定登录 Shell
- `-G sudo`：将用户添加到 sudo 组

> 📸 **此处可插入截图2**：用户创建命令执行结果

#### 3.2.2 修改用户属性

**修改用户密码：**

```bash
sudo passwd devuser
```

**修改用户主目录：**

```bash
sudo usermod -d /home/newhome devuser
```

**修改用户登录 Shell：**

```bash
sudo usermod -s /bin/zsh devuser
```

**修改用户名（需谨慎操作）：**

```bash
sudo usermod -l newname oldname
```

#### 3.2.3 删除用户

```bash
sudo userdel -r devuser
```

`-r` 参数会同时删除用户的主目录和邮件池。

### 3.3 用户组管理

#### 3.3.1 查看用户所属组

```bash
groups devuser
id devuser
```

#### 3.3.2 创建用户组

```bash
sudo groupadd devteam
```

#### 3.3.3 将用户添加到组

```bash
sudo usermod -aG devteam devuser
```

注意：`-a` 参数表示追加，不加 `-a` 会覆盖用户原有的附属组。

#### 3.3.4 从组中移除用户

```bash
sudo gpasswd -d devuser devteam
```

#### 3.3.5 删除用户组

```bash
sudo groupdel devteam
```

> 📸 **此处可插入截图3**：组管理命令执行结果

### 3.4 文件权限管理

#### 3.4.1 理解权限位

**查看文件权限：**

```bash
ls -l test.txt
```

输出示例：`-rwxr-xr-- 1 groupwork groupwork 1024 Jul 10 10:00 test.txt`

| 位置 | 含义 |
|------|------|
| 第 1 位 | 文件类型（`-` 普通文件，`d` 目录，`l` 符号链接） |
| 第 2-4 位 | 所有者权限（读/写/执行） |
| 第 5-7 位 | 所属组权限（读/写/执行） |
| 第 8-10 位 | 其他用户权限（读/写/执行） |

#### 3.4.2 使用数字法修改权限

**权限数字对照表：**

| 数字 | 权限 | 含义 |
|------|------|------|
| 7 | rwx | 读 + 写 + 执行 |
| 6 | rw- | 读 + 写 |
| 5 | r-x | 读 + 执行 |
| 4 | r-- | 只读 |
| 3 | -wx | 写 + 执行 |
| 2 | -w- | 只写 |
| 1 | --x | 只执行 |
| 0 | --- | 无权限 |

**常用权限组合：**

```bash
chmod 755 script.sh      # 所有者 rwx，组 r-x，其他 r-x
chmod 644 config.txt     # 所有者 rw-，组 r--，其他 r--
chmod 600 secret.txt     # 所有者 rw-，组 ---，其他 ---
chmod 777 dangerous.txt  # 所有人可读、可写、可执行（不推荐）
```

#### 3.4.3 使用符号法修改权限

```bash
chmod u+x file.txt       # 所有者增加执行权限
chmod g-w file.txt       # 去掉组的写权限
chmod o+r file.txt       # 其他用户增加读权限
chmod a+x file.txt       # 所有用户增加执行权限
chmod u=rwx,g=rx,o=r file.txt  # 精确设置权限
```

#### 3.4.4 递归修改目录权限

```bash
chmod -R 755 /path/to/dir
```

#### 3.4.5 修改文件所有者和所属组

```bash
sudo chown devuser:devteam file.txt
```

```bash
sudo chown -R devuser:devteam /home/devuser
```

```bash
sudo chgrp devteam file.txt   # 仅修改所属组
```

> 📸 **此处可插入截图4**：权限管理命令执行结果

### 3.5 特殊权限（SUID、SGID、Sticky Bit）

#### 3.5.1 SUID（Set User ID）

**作用**：执行文件时，进程以文件所有者的权限运行，而非执行者。

**典型应用**：`passwd` 命令需要修改 `/etc/shadow`，普通用户通过 SUID 获得 root 权限。

**设置 SUID：**

```bash
chmod u+s /path/to/program
# 或使用数字法 4xxx
chmod 4755 /path/to/program
```

**查看 SUID 权限：**

```bash
ls -l /usr/bin/passwd
# 输出：-rwsr-xr-x（所有者权限中 x 变为 s）
```

#### 3.5.2 SGID（Set Group ID）

**作用**：
- 对于可执行文件：进程以文件所属组的权限运行
- 对于目录：新创建的文件继承目录的所属组

**设置 SGID：**

```bash
chmod g+s /path/to/dir
# 或使用数字法 2xxx
chmod 2755 /path/to/dir
```

#### 3.5.3 Sticky Bit（粘滞位）

**作用**：在目录上设置后，只有文件所有者才能删除该目录中的文件，即使其他用户对该目录有写权限。

**典型应用**：`/tmp` 目录。

**设置 Sticky Bit：**

```bash
chmod +t /path/to/dir
# 或使用数字法 1xxx
chmod 1777 /path/to/dir
```

**查看 Sticky Bit：**

```bash
ls -ld /tmp
# 输出：drwxrwxrwt（其他用户权限中 x 变为 t）
```

> 📸 **此处可插入截图5**：特殊权限配置及验证

### 3.6 sudo 权限配置

#### 3.6.1 理解 sudo

`sudo` 允许普通用户以 root 或其他用户的身份执行命令，通过 `/etc/sudoers` 文件控制权限。

#### 3.6.2 使用 visudo 编辑 sudoers 文件

```bash
sudo visudo
```

**添加自定义配置（在文件末尾追加）：**

```bash
# 允许 devuser 执行所有命令（需要密码）
devuser ALL=(ALL) ALL

# 允许 devuser 执行所有命令（免密码）
devuser ALL=(ALL) NOPASSWD: ALL

# 允许 devuser 执行特定命令（免密码）
devuser ALL=(ALL) NOPASSWD: /usr/bin/systemctl, /usr/bin/apt

# 允许组 devteam 的所有成员执行所有命令
%devteam ALL=(ALL) ALL
```

#### 3.6.3 验证 sudo 权限

```bash
sudo -l
```

该命令会列出当前用户允许执行的所有命令。

> 📸 **此处可插入截图6**：sudo 权限配置及验证

### 3.7 环境变量配置

#### 3.7.1 查看环境变量

```bash
env                    # 查看所有环境变量
echo $PATH            # 查看 PATH 变量
echo $HOME            # 查看用户主目录
echo $USER            # 查看当前用户名
echo $SHELL           # 查看当前 Shell
echo $PWD             # 查看当前工作目录
```

#### 3.7.2 临时设置环境变量

```bash
export MY_VAR="Hello Linux"
export PATH=$PATH:/home/groupwork/mybin
```

#### 3.7.3 删除环境变量

```bash
unset MY_VAR
```

#### 3.7.4 永久设置环境变量（用户级）

**编辑 `~/.bashrc`（推荐，每次打开新终端生效）：**

```bash
nano ~/.bashrc
```

在文件末尾添加：

```bash
# 自定义环境变量
export MY_CUSTOM_VAR="自定义变量"

# 自定义 PATH
export PATH=$PATH:/home/groupwork/mybin

# 自定义别名
alias ll='ls -alF'
alias gs='git status'
alias update='sudo apt update && sudo apt upgrade -y'
```

保存后使配置生效：

```bash
source ~/.bashrc
```

**编辑 `~/.profile`（登录时执行一次）：**

```bash
nano ~/.profile
```

在文件末尾添加自定义配置。

> 📸 **此处可插入截图7**：`.bashrc` 配置及生效

#### 3.7.5 永久设置环境变量（系统级）

**方法一：编辑 `/etc/environment`（所有用户生效，键值对格式，不使用 export）**

```bash
sudo nano /etc/environment
```

添加（不使用 `export`）：
```
MY_GLOBAL_VAR="全局变量"
```

重启后生效。

**方法二：编辑 `/etc/profile`（所有用户登录时执行）**

```bash
sudo nano /etc/profile
```

在文件末尾添加（需要使用 `export`）：
```bash
export MY_GLOBAL_VAR="全局变量"
```

**方法三（推荐）：在 `/etc/profile.d/` 创建独立脚本**

```bash
sudo nano /etc/profile.d/myenv.sh
```

添加：
```bash
export MY_GLOBAL_VAR="全局变量"
export PATH=$PATH:/opt/myapp/bin
```

所有用户登录时自动加载，便于管理和维护。

> 📸 **此处可插入截图8**：系统级环境变量配置

### 3.8 用户配置文件管理

Linux 用户配置文件位于 `~/.config` 和 `~/.local` 目录，以及根目录下的各种点文件（dotfiles）。

**常用的用户级配置文件：**

| 文件 | 用途 |
|------|------|
| `~/.bashrc` | Bash 交互式非登录 Shell 配置 |
| `~/.profile` | 用户登录时执行（登录 Shell） |
| `~/.bash_logout` | 用户注销时执行 |
| `~/.bash_history` | 命令行历史记录 |
| `~/.gitconfig` | Git 用户级配置 |
| `~/.ssh/` | SSH 密钥和配置 |

**查看当前用户的所有配置文件：**

```bash
ls -la ~ | grep "^\."
```

## 四、实验结果

通过本次实验，成功完成了以下目标：

| 检查项 | 状态 |
|--------|------|
| 查看并理解 `/etc/passwd`、`/etc/shadow`、`/etc/group` 文件 | ✅ |
| 用户创建（adduser）、修改（usermod）、删除（userdel） | ✅ |
| 用户组管理（groupadd、groupdel、gpasswd） | ✅ |
| 文件权限管理（chmod 数字法 + 符号法） | ✅ |
| 文件所有者修改（chown、chgrp） | ✅ |
| 递归修改目录权限（chmod -R、chown -R） | ✅ |
| SUID/SGID/Sticky Bit 特殊权限配置与验证 | ✅ |
| sudo 权限配置（visudo） | ✅ |
| 环境变量查看、临时设置与永久配置 | ✅ |
| `~/.bashrc` 自定义配置（别名、PATH、自定义变量） | ✅ |
| 系统级环境变量配置（/etc/profile.d/） | ✅ |

## 五、遇到的问题与解决方案

| 问题现象 | 可能原因 | 解决方案 |
|----------|----------|----------|
| `userdel: user is currently logged in` | 用户当前已登录 | 先退出用户或使用 `sudo pkill -u 用户名` 终止进程后再删除 |
| `chown: invalid user` | 用户名不存在或拼写错误 | 使用 `cat /etc/passwd \| grep 用户名` 确认用户名正确 |
| 修改 `~/.bashrc` 后配置不生效 | 未重新加载配置文件 | 执行 `source ~/.bashrc` 或重新登录终端 |
| 执行 `sudo` 命令报 `user is not in the sudoers file` | 用户未被授予 sudo 权限 | 以 root 用户登录，执行 `usermod -aG sudo 用户名` |
| `visudo` 保存时报语法错误 | sudoers 文件配置格式有误 | 检查语法，确保每行符合 `用户 主机=(用户) 命令` 格式 |
| 设置 SUID 后权限位显示为 `S` 而非 `s` | 文件没有执行权限 | 先用 `chmod u+x` 添加执行权限，再设置 SUID |
| 无法删除 `/tmp` 目录中其他用户的文件 | Sticky Bit 机制阻止 | 这是正常行为，只有文件所有者或 root 才能删除 |
| `/etc/profile.d/` 中的配置未生效 | 需要重新登录 | 重新登录终端，或执行 `source /etc/profile.d/myenv.sh` |

## 六、实验总结

本次实验深入学习了 Linux 的账户与权限管理系统，从配置文件结构到用户管理命令，从文件权限到特殊权限，从环境变量到系统配置，系统地掌握了 Linux 系统管理的基础知识。

### 关键收获

1. **Linux 一切皆文件的设计理念**：用户、组、权限等核心概念都通过配置文件（`/etc/passwd`、`/etc/group` 等）体现，理解这些文件是理解 Linux 管理的基础。

2. **权限模型的多层设计**：
   - 文件权限（rwx）控制对文件的访问
   - 所有者（owner）和所属组（group）定义资源的归属
   - 特殊权限（SUID/SGID/Sticky Bit）提供了更精细的控制
   - sudo 机制实现了细粒度的授权管理

3. **环境变量是系统灵活性的体现**：通过 `~/.bashrc` 等配置文件，用户可以自定义工作环境，提高日常操作的效率。

4. **实验环境的延续性**：本实验配置的用户、权限和环境变量，将直接服务于后续实验中的文件传输、软件安装和自动化部署等任务。

## 七、参考资料

1. Ubuntu 服务器文档：https://ubuntu.com/server/docs
2. Linux 用户管理指南：https://ubuntu.com/server/docs/security-users
3. sudo 官方文档：https://www.sudo.ws/docs/
4. Linux 文件权限详解：https://wiki.archlinux.org/title/File_permissions_and_attributes
5. Bash 环境变量：https://www.gnu.org/software/bash/manual/bash.html#Bash-Startup-Files
