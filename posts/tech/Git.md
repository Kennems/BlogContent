---
title : 'Git'
date : 2024-08-20T22:30:13+08:00
lastmod: 2024-08-20T22:20:13+08:00
description : "Git使用" 
image : img/cat.jpg
draft : false    
categories : ["Git学习笔记"]
tags : ["Git"]
---

# Git

git 由 linus 开发。

## Git流程图

![img](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015120901.png)

1. **clone**（克隆)）： 从远程仓库中克隆代码到本地仓库
2. **checkout**（检出）：从本地仓库中检出一个仓库分支然后进行修订
3. **add**（添加）：在提交前先将代码提交到暂存区
4. **commit**（提交）：提交到本地仓库，本地仓库中保存修改的各个历史版本
5. **fetch**（抓取）：从远程库，抓取到本地仓库，不进行任何的合并动作，一般操作比较少。
6. **pull**（拉取）：从远程库拉到本地库，自动进行合并（merge），然后放到工作区，相当于fetch + merge
7. **push**（推送）：修改完成后，需要和团队成员共享代码时，将代码推送到远程仓库

## Git基本配置

```bash
git config --global user.name "xxx"
git config --global user.email "xxx@gmail.com"
```

```bash
git config --global user.name
git config --global user.email
```

给一些长命令起别名：

在 ~/.bashrc 中添加：

```bash
alias git-log='git log --pretty=oneline --all --graph --abbrev-commit'
alias ll='ls -al'
```

之后 source ~/.bashrc 使其生效。

### 新建代码库

```bash
# 在当前目录新建一个git代码库
$ git init 
# 新建一个目录，将其初始化为git代码库
$ git init [project-name]
# 下载一个项目和它的整个代码历史
$ git clone [url]
```

### 配置

git的设置文件为 **.gitconfig** ,它可以在用户主目录下（全局配置），也可以在项目目录下（项目配置）。

```bash 
# 显示当前的git配置
$ git config --list 
#编辑git配置文件
$ git config -e [--global]
#设置提交代码时的用户信息
$ git config [--global] user.name "[name]"
$ git config [--global] user.name "[email address]"
```

### 增加/删除文件

```bash 
# 添加指定文件到暂存区
$ git add [file1] [file2]
# 添加指定目录到暂存区，包括子目录
$ git add [dir]
# 添加当前目录的所有文件到暂存区
$ git add .
# 添加每个变化前，都会要求确认
# 对于同一个文件的多处变化，可以实现分次提交
$ git add -p
# 删除工作区文件，并且将这次删除放入暂存区
$ git rm [file1] [file2]
# 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [file]
# 改名文件，并且将这个改名放入暂存区
$ git mv [file-original] [file-renamed]
```

### 代码提交

```bash 
# 提交暂存区到仓库区
$ git commit -m [message]

# 提交暂存区的指定文件到仓库区
$ git commit [file1] [file2] [file3] ... -m [message]

# 提交工作区自上次commit之后的变化，直接到仓库区 
$ git commit -a 

# 提交时显示所有diff信息
$ git commit -v

# 使用一次新的commit，替代上一次提交
# 如果代码没有任何新变化，则用来改写上一次commit的提交信息
$ git commit -amend -m [message]

# 重做上一次commit，并包括指定文件的新变化
$ git commit -amend [file1] [file2] ...
```

### 版本切换：

![image-20240814114817965](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240814114817965.png)

```bash
git reset --hard commitId
```

查看所有日志：

```bash
git reflog
```

### 换行符（LF & CRLF）

它控制着在提交和检出文件时是否自动地将行结束符转换为不同的格式。

```bash
git config --global core.autocrlf false
```

### 查看当前项目是由哪个`.git`目录管理的

#### 方法一：使用 `git rev-parse --show-toplevel`

```bash
git rev-parse --show-toplevel
```

这条命令会输出 **Git 仓库的根目录**，即 `.git` 所在目录。

```bash
/home/kenn/Projects/my-project
```

#### 方法二：查看 `.git` 目录的路径

```bash
git rev-parse --git-dir
```

这条命令直接输出 `.git` 目录的路径：

- 如果输出是 `.git`，说明当前目录（或上层）就有 `.git` 文件夹。
- 如果是类似 `.git` 是一个文件（子模块或工作区配置），则它可能是一个指向真实 `.git` 的路径（Git 工作树结构）。

## 代理

```shell
git config --global http.sslVerify "false"
```

### 设置代理

```shell
git config --global http.proxy http://127.0.0.1:端口号
```

```shell
git config --global http.proxy http://127.0.0.1:10809
git config --global https.proxy http://127.0.0.1:10809
```

### 取消代理

临时取消全局代理设置

```shell
git config --global --unset http.proxy
git config --global --unset https.proxy
```

仅对当前仓库禁用代理

```shell
git config --local http.proxy ""
git config --local https.proxy ""
```

通过环境变量临时禁用（适用于当前终端会话）

```shell
set HTTP_PROXY=
set HTTPS_PROXY=
```

#### 临时取消代理

直接使用无代理克隆（推荐）

```shell
git -c http.proxy= clone https://huggingface.co/XiaomiMiMo/MiMo-7B-RL
```

## 分支

查看本地分支

```bash
git branch
```

创建本地分支

```bash
git branch 分支名
```

切换分支

```bash
git checkout 分支名
```

切换并创建分支

```bash
git checkout -b 分支名
```

### 合并分支

```bash
git merge
```

### 删除分支

删除分支时，需要做各种检查

```bash
git branch -d b1 
```

不做任何检查，强制删除

```bash
git branch -D b1
```

## 解决冲突

```bash
git merge 需要合并的工作区
```

### 远程仓库

```bash
git push -f --set-upstream [远端名称[本地分支名][:远端分支名]]
```

```bash
git remote add origin 源
```

## 源管理

### 查看源

```shell
git remote -v
```

只查看 `origin` 的 URL

```shell
git remote get-url origin
```

### 更改远程仓库的 URL

```shell
git remote set-url origin 新的仓库地址
```

### 添加多个源

```bash
git remote add origin2 https://github.com/username/other-repo.git
```

### 删除源

```bash
git remote remove origin2
```

### 设置本地分支与远端分支绑定

```bash
git push --set-upstream origin main
```

或者`-u` 会设置 upstream

设置好之后，直接

```bash
git push 
```

##  克隆

```bash
git clone 地址
```

### 从远程仓库抓取和拉取

抓取，将仓库里的更新都抓取到本地，但不会进行合并

```bash
git fetch [remote name] [branch name]
```

拉取，就是将远端仓库的修改拉到本地并自动进行合并，等同于 fetch + merge

```bash
git pull [remote name] [branch name]
```

## 解决合并冲突

在 Git 中，合并冲突通常发生在你尝试将一个分支的修改合并到另一个分支时，且这些分支修改了相同的文件和行。Git 无法自动合并这些冲突，需要手动干预来解决。

## 远程有本地没有的提交，同时本地也有提交(或者改动)

#### 方法一：**先拉，再推**

```bash
bashCopyEditgit pull --rebase origin main
git push -u origin main
```

#### 方法二：**放弃远程的 main，强推**

```bash
git push -f origin main
```

#### 方法三：**创建一个新分支然后提 Merge Request**

```bash
bashCopyEditgit checkout -b my_main_v1
git push -u origin my_main_v1
```

#### 处理代码本地改动

提交

```bash
git add .
git commit -m "本地修改：提交前pull"
git pull --rebase origin main
git push -u origin main
```

暂时存起来

```bash
git stash
git pull --rebase origin main
git stash pop
git push -u origin main
```

### 解决 Git 合并冲突的步骤：

1. **触发合并冲突**

   通常，合并冲突在你执行 `git merge` 或 `git rebase` 时发生。例如，如果你正在将 `feature` 分支合并到 `main` 分支：

   ```bash
   git checkout main
   git merge feature
   ```

   如果有冲突，Git 会停止合并，并输出类似以下的信息：

   ```
   Auto-merging <file>
   CONFLICT (content): Merge conflict in <file>
   ```

2. **查看哪些文件有冲突**

   使用 `git status` 查看哪些文件存在冲突：

   ```bash
   git status
   ```

   Git 会列出有冲突的文件，通常显示为 **"both modified"**。

   例如：

   ```
   both modified:   example.txt
   ```

3. **打开并解决冲突**

   打开有冲突的文件（例如 `example.txt`），你会看到类似下面的内容：

   ```text
   <<<<<<< HEAD
   这是 main 分支的内容
   =======
   这是 feature 分支的内容
   >>>>>>> feature
   ```

   - `<<<<<<< HEAD` 和 `=======` 之间是当前分支（`HEAD`）的内容。
   - `=======` 和 `>>>>>>> feature` 之间是要合并的分支（`feature`）的内容。

   你需要手动修改这些冲突部分，选择保留哪个内容，或者进行合并。例如，可能会修改为：

   ```text
   这是合并后的内容，包含了 main 和 feature 分支的修改。
   ```

   解决完冲突后，删除标记 (`<<<<<<<`, `=======`, `>>>>>>>`)，并保存文件。

4. **标记文件为已解决**

   解决冲突并保存文件后，使用 `git add` 将文件标记为已解决：

   ```bash
   git add example.txt
   ```

5. **完成合并**

   一旦所有冲突都解决并标记为已解决，你可以提交合并：

   ```bash
   git commit
   ```

   如果是合并操作，Git 会自动生成一个合并提交信息，你可以修改这个信息，或者直接保存并提交。

6. **查看合并结果**

   使用 `git status` 确认冲突已解决，工作树干净，合并已完成：

   ```bash
   git status
   ```

   如果一切正常，Git 会显示如下：

   ```
   On branch main
   nothing to commit, working tree clean
   ```

### 使用图形化工具解决冲突

使用 Git 的图形化工具（如 `GitKraken`、`SourceTree`、`Git GUI` 等）。这些工具通常会以可视化方式显示冲突，并允许你选择或编辑解决方案。

### 合并冲突的常见场景

1. **编辑同一行**：两个分支修改了相同的文件的同一行。
2. **删除和修改**：一个分支删除了文件，另一个分支修改了文件内容。
3. **相同内容的修改**：两个分支在同一文件中修改了相同的内容，但修改的方式略有不同。

## 推送

### 推送所有本地分支

```bash
git push --all
```

### 强推

```bash
git push --force origin main
```

### 使用 `--force-with-lease` 替代 `--force`

为了避免强推造成的潜在数据丢失问题，可以使用 `--force-with-lease` 代替 `--force`。这个命令可以确保你仅在远程仓库的分支没有新的提交时进行强推，否则会拒绝推送，避免覆盖其他人的提交。

```bash
git push --force-with-lease
```

## 恢复

### 1. 恢复到特定提交

如果你想将分支恢复到某个特定的提交（如 `abc1234`），可以使用以下命令：

```bash
git reset --hard abc1234
```

这将重置当前分支到该提交，并且会丢失该提交之后的所有更改。

### 2. 还原最近的提交（但保留更改）

如果你想撤销最近的提交，但保留工作区的更改，可以使用：

```bash
git reset --soft HEAD~1
```

这会将 HEAD 移动到上一个提交，同时保留你的更改在暂存区。

### 3. 恢复被删除的提交

如果你需要恢复一个已经被删除的提交，可以使用 `git reflog` 查找并恢复它：

```bash
git reflog
```

找到需要恢复的提交哈希（如 `abc1234`），然后使用：

```  bash
git checkout abc1234
```

或者，如果你想将当前分支移动到该提交：

```bash
git reset --hard abc1234
```

### 4. 撤销最近的提交（保持更改）

如果你想撤销最近的提交，但想保留更改在工作目录中，可以使用：

```bash
git revert HEAD
```

这将创建一个新的提交，撤销最后一次提交的更改。

## 修改 commit 的 message 

### 1. 修改最近一次的 commit message

1. 使用 `--amend`：

   ```bash
   git commit --amend -m "新的提交信息"
   ```

   这条命令会替换最近一次的 commit message。

### 2. 修改更早的 commit message

如果需要修改更早的 commit message，可以使用 `rebase` 命令：

1. **启动交互式 rebase**：

   ```bash
   git rebase -i HEAD~n
   ```

   其中 `n` 是你想要回溯的提交数量。

2. **在打开的文本编辑器中找到要修改的 commit**，将 `pick` 改为 `reword`（或 `r`）。

3. **保存并退出编辑器**，接着会弹出一个新的编辑器窗口，你可以在这里修改 commit message。

4. **保存并关闭编辑器**，完成后 Git 将会应用修改。

## SSH配置

### 1. **生成 SSH 密钥**

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

解释：

- `-t rsa`：指定使用 RSA 算法生成密钥。
- `-b 4096`：指定密钥的长度为 4096 位，越长的密钥越安全。
- `-C`：添加一个注释，通常用来标识密钥的用途（例如你的电子邮件地址）。

1. 接下来，系统会提示你选择密钥保存位置。默认情况下，SSH 密钥会保存在 `~/.ssh/id_rsa`（Linux 和 macOS）或 `C:\Users\<YourUsername>\.ssh\id_rsa`（Windows）中。直接按 **Enter** 即可使用默认位置。
2. 你会被要求输入一个密钥的 **密码**。这个密码是可选的，如果设置了密码，每次使用 SSH 密钥时需要输入密码，增加安全性。
3. 完成后，SSH 密钥对将会生成：
   - 公钥：`~/.ssh/id_rsa.pub`
   - 私钥：`~/.ssh/id_rsa`

### 2. **将 SSH 公钥添加到 Git 服务器**

将你生成的公钥添加到 Git 服务器（如 GitHub、GitLab 或 Bitbucket）以便验证你的身份。这里以 GitHub 为例：

1. **复制 SSH 公钥**：

   使用以下命令将公钥复制到剪贴板（Linux/macOS）：

   ```bash
   cat ~/.ssh/id_rsa.pub
   ```

   可以手动复制输出的公钥，或者在 macOS 上使用 `pbcopy`（Windows 上可以直接打开文件并复制）：

   ```bash
   pbcopy < ~/.ssh/id_rsa.pub  # macOS
   ```

   对于 Windows，可以直接打开 `~/.ssh/id_rsa.pub` 文件，并复制内容。

2. **将公钥添加到 GitHub**：

   - 登录到你的 GitHub 账户。
   - 进入 **Settings**（设置） > **SSH and GPG keys** > **New SSH key**。
   - 将刚刚复制的公钥粘贴到 **Key** 字段中，并为该密钥设置一个合适的标题（例如：`My Laptop`）。
   - 点击 **Add SSH key**。

### 3. **测试 SSH 连接**

确保一切配置正确后，运行以下命令来测试 SSH 是否连接成功：

```bash
ssh -T git@github.com
```

如果你使用的是 GitHub，这条命令应该返回类似以下的信息：

```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

如果返回成功信息，则说明 SSH 配置成功。

### 4. **配置 Git 使用 SSH URL**

现在，你已经成功配置了 SSH 密钥，并将其添加到 GitHub 账户。接下来，确保 Git 使用 SSH 协议而非 HTTPS 协议进行操作。

1. **克隆仓库时使用 SSH URL**：

   ```bash
   git clone git@github.com:username/repository.git
   ```

2. **更改现有仓库的远程 URL 为 SSH**：

   如果你已经克隆了一个仓库，但它是使用 HTTPS URL，想改成 SSH URL，可以使用以下命令：

   ```bash
   git remote set-url origin git@github.com:username/repository.git
   ```

   这样，之后所有 `git push` 和 `git pull` 操作都会通过 SSH 进行。

### 5. **常见问题**

- **无法连接到 Git 服务器**：确认公钥已正确添加到服务器，并检查防火墙或代理设置是否阻止了 SSH 连接。

- **密钥权限问题**：确保你的私钥文件权限正确（`~/.ssh/id_rsa` 应该是 600 权限）：

  ```bash
  chmod 600 ~/.ssh/id_rsa
  ```

- **使用多个 Git 账户**：如果你有多个 Git 账户（例如，一个用于工作，一个用于个人项目），你可以通过在 `~/.ssh/config` 文件中配置不同的密钥来区分不同的账户。

```bash
# Default GitHub Account (for personal use)
Host github.com
  User git
  HostName github.com
  IdentityFile ~/.ssh/id_rsa_github_personal

# Work GitHub Account (for work use)
Host github-work.com
  User git
  HostName github.com
  IdentityFile ~/.ssh/id_rsa_github_work
  IdentitiesOnly yes

# GitLab Personal Account
Host gitlab.com
  User git
  HostName gitlab.com
  IdentityFile ~/.ssh/id_rsa_gitlab_personal
  IdentitiesOnly yes
```

- **`IdentityFile`**：指定用于 SSH 连接的密钥文件。
- **`IdentitiesOnly yes`**：确保只有你指定的密钥会被使用，避免其他默认密钥的干扰。

### 使用配置的 Git 操作：

1. **克隆仓库时指定 `Host`**：

   你可以使用配置中指定的 `Host` 来轻松地选择要使用的远程仓库。例如，假设你要克隆 GitHub 个人账户的仓库，你可以使用如下命令：

   ```bash
   git clone git@github.com:username/repository.git
   ```

   对于工作 GitHub 账户，可以使用：

   ```bash
   git clone git@github-work.com:username/repository.git
   ```

   对于 GitLab 和 Bitbucket 也同理。

2. **推送到远程仓库时**：

   当你执行 `git push` 或 `git pull` 时，Git 会自动选择相应的 SSH 密钥。

## Git Bash

`git bash` 设置home目录

```bash
setx HOME "C:\Projects"
```

## `.gitignore`

检查`.gitignore` 是否设置生效

```bash
git check-ignore -v .idea
```

