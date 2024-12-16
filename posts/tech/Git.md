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

### 版本切换：

![image-20240814114817965](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240814114817965.png)

```bash
git reset --hard commitId
```

查看所有日志：

```bash
git reflog
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

查看源

```shell
git remote -v
```

只查看 `origin` 的 URL

```shell
git remote get-url origin
```

更换源

```shell
git remote set-url origin 新的仓库地址
```

### 设置本地分支与远端分支绑定

```bash
git push --set-upstream origin main
```

设置好之后，直接

```bash
git push 
```

##  克隆

```bash
git clone 地址
```

## 从远程仓库抓取和拉取

抓取，将仓库里的更新都抓取到本地，但不会进行合并

```bash
git fetch [remote name] [branch name]
```

拉取，就是将远端仓库的修改拉到本地并自动进行合并，等同于 fetch + merge

```bash
git pull [remote name] [branch name]
```

## 解决合并冲突



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
