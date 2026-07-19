---
title : 'Mac 开发环境配置'
date : 2026-05-04T18:11:00+08:00
lastmod: 2026-05-04T18:11:00+08:00
description : "Mac 开发环境配置" 
# image : img/cat.jpg
# draft : false    
categories : ["Mac"]
tags : ["Mac"]
---


# Mac 开发环境配置

> 本文档基于 2026-05-03 实际系统状态编写，所有命令和配置均来自本人已验证的安装记录。
> 硬件：Apple Silicon Mac | 系统：macOS 26

---

## 目录

1. [macOS 自带好用的工具](#1-macos-自带好用的工具)
2. [基础开发环境](#2-基础开发环境)
3. [终端与 Shell 配置](#3-终端与-shell-配置)
4. [版本控制：Git](#4-版本控制git)
5. [开发工具](#5-开发工具)
6. [效率工具](#6-效率工具)
7. [AI 工具](#7-ai-工具)
8. [网络与代理](#8-网络与代理)
9. [写作与笔记](#9-写作与笔记)
10. [容器与虚拟化](#10-容器与虚拟化)
11. [多媒体与办公](#11-多媒体与办公)
12. [系统设置优化](#12-系统设置优化)
13. [Dotfiles 管理](#13-dotfiles-管理)
14. [一键恢复脚本](#14-一键恢复脚本)

---

## 1. macOS 自带好用的工具

很多人觉得 Mac 开箱是"毛坯房"，其实 macOS 自带了很多实用工具，先了解它们再决定要不要装第三方替代品。

### 1.1 终端 Terminal

路径：`/System/Applications/Utilities/Terminal.app`

自带终端已经够用：多标签、分屏、主题切换。装上 Oh My Zsh 之后体验不输 iTerm2。

| 快捷键 | 功能 |
|--------|------|
| `Cmd + T` | 新建标签 |
| `Cmd + D` | 垂直分屏 |
| `Cmd + Shift + D` | 水平分屏 |
| `Cmd + K` | 清屏 |

### 1.2 活动监视器 Activity Monitor

路径：`/System/Applications/Utilities/Activity Monitor.app`

相当于 Windows 任务管理器。CPU、内存、能耗、磁盘、网络一目了然。电脑发烫/卡顿时的第一站。

**技巧**：Dock 上右键活动监视器 → 显示 CPU 使用率，Dock 图标直接显示柱状图。

### 1.3 截图 Screenshot

| 快捷键 | 功能 |
|--------|------|
| `Cmd + Shift + 3` | 全屏截图 |
| `Cmd + Shift + 4` | 区域截图 |
| `Cmd + Shift + 4` 再按 `Space` | 窗口截图 |
| `Cmd + Shift + 5` | 完整截图/录屏面板（最推荐） |

截图时按住 `Ctrl` → 保存到剪贴板而不是文件。

### 1.4 预览 Preview

不止是看图/PDF 工具，还能做：

- PDF 标注（高亮、签名、填表）
- 简单图片裁剪和调色
- 合并 PDF（把图片/PDF 拖到一起）
- 简单抠图（工具栏 → 即时 Alpha）

### 1.5 磁盘工具 Disk Utility

路径：`/System/Applications/Utilities/Disk Utility.app`

格式化、分区、磁盘映像、急救修复。外接硬盘出问题时靠它。

### 1.6 数码测色计 Digital Color Meter

路径：`/System/Applications/Utilities/Digital Color Meter.app`

屏幕取色工具，显示任意像素 RGB/HEX 值。`Cmd + Shift + C` 复制颜色值。

### 1.7 控制台 Console

路径：`/System/Applications/Utilities/Console.app`

查看系统日志、崩溃报告。排查 macOS 级问题时的利器。

### 1.8 其他值得了解的

| 工具 | 用途 |
|------|------|
| **便签 Stickies** | 桌面便签，比 Notes 更快 |
| **自动操作 Automator** | 批量处理文件、创建右键快捷操作 |
| **快捷指令 Shortcuts** | 现代化自动化工具，支持菜单栏 |
| **钥匙串访问** | 管理系统密码/证书/密钥 |
| **语音备忘录** | 录音，开会/灵感记录 |
| **词典 Dictionary** | 三指轻点查词 |

---

## 2. 基础开发环境

### 2.1 Xcode（含 Command Line Tools）

**当前状态**：✅ 已安装（完整 Xcode.app）

```bash
# 确认安装
xcode-select -p
# 输出: /Applications/Xcode.app/Contents/Developer
```

如果只装 Command Line Tools（不需要完整 Xcode）：

```bash
xcode-select --install
```

### 2.2 Homebrew 包管理器

**当前状态**：✅ 已安装，版本 5.1.8

Homebrew 是 macOS 最重要的包管理器，几乎所有 CLI 工具都通过它管理。

```bash
# 确认版本
brew --version
# 输出: Homebrew 5.1.8（当前实际版本，2026-05-03）
```

**安装方式（初次）：**

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
# Apple Silicon 机器安装后需执行:
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

**常用命令：**

```bash
brew search <keyword>    # 搜索软件
brew info <formula>      # 查看详情
brew install <formula>   # 安装 CLI 工具
brew install --cask <name>  # 安装 GUI 应用
brew list                # 已安装的 CLI 工具
brew list --cask         # 已安装的 GUI 应用（brew 方式）
brew update && brew upgrade  # 更新所有
brew cleanup             # 清理旧版本
brew doctor              # 检查系统问题
```

**本机实际已安装的 CLI 工具（80+，按用途分类）：**

| 类别 | 已安装工具 |
|------|-----------|
| **语言运行时** | `go`、`node`、`python@3.13`、`python@3.14`、`openjdk@17`、`llvm@20`、`gcc` |
| **包/构建** | `uv`（Python 包管理）、`golangci-lint`、`protobuf@33` |
| **版本控制** | `gh`（GitHub CLI） |
| **搜索/文本** | `ripgrep`、`tree` |
| **终端** | `tmux`、`fastfetch` |
| **多媒体** | `ffmpeg`、`openai-whisper` |
| **AI CLI** | `gemini-cli`、`opencode`、`obsidian-cli` |
| **趣味/杂项** | `cowsay`、`fortune`、`camsnap`、`gifgrep`、`songsee`、`hugo` |

**本机已通过 brew cask 安装的应用（4 个）：**

| 应用 | 类型 | 用途 |
|------|------|------|
| `stats` | GUI | 菜单栏系统监控 |
| `codexbar` | GUI | Codex 菜单栏助手 |
| `1password-cli` | CLI | 1Password 命令行工具 |
| `xurl` | CLI | URL 处理工具 |

> 注意：`1password-cli` 和 `xurl` 虽是 cask 安装，但实际是命令行工具而非 GUI 应用。

> **注意**：本机大部分 GUI 应用是手动安装或从 App Store 下载的，不在 brew cask 列表中。这不影响使用——两种方式都可以。后续统一用 brew cask 更好管理。

**Homebrew 镜像加速（国内用户）：**

```bash
# 写入 ~/.zshrc
export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.ustc.edu.cn/brew.git"
export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.ustc.edu.cn/homebrew-core.git"
export HOMEBREW_BOTTLE_DOMAIN="https://mirrors.ustc.edu.cn/homebrew-bottles"
```

---

## 3. 终端与 Shell 配置

### 3.1 当前终端选择

**已安装**：iTerm.app（手动安装） + 系统自带 Terminal.app

我目前主要使用 iTerm。如果你刚开始配置，系统自带 Terminal + Oh My Zsh 已经足够好。

```bash
# iTerm 通过 brew 安装:
brew install --cask iterm2
```

**iTerm 推荐设置：**

1. 开启 Quake 风格全局热键
   `Preferences → Keys → Hotkey → 勾选 "Show/hide iTerm2 with a system-wide hotkey"`
   推荐 `Cmd +``

2. 无限滚动
   `Preferences → Profiles → Terminal → 勾选 "Unlimited scrollback"`

3. 配色方案
   `Preferences → Profiles → Colors → Color Presets → Solarized Dark`
   更多主题：[iTerm2-Color-Schemes](https://github.com/mbadolato/iTerm2-Color-Schemes)

### 3.2 Oh My Zsh

**当前状态**：✅ 已安装

Oh My Zsh 是 zsh 的配置框架，提供插件和主题管理。macOS Catalina 起默认 Shell 就是 zsh。

```bash
# 安装命令:
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

配置文件：`~/.zshrc`

### 3.3 当前主题和插件

**本机实际配置**（从 `~/.zshrc` 提取）：

```bash
ZSH_THEME="ys"    # 当前使用 ys 主题（简洁风格）

plugins=(
    git                        # Git 别名和补全
    zsh-syntax-highlighting    # 命令输入时的语法着色
    zsh-autosuggestions        # 灰色提示历史命令
)
```

**已安装的第三方插件**（在 `~/.oh-my-zsh/custom/plugins/`）：

- `zsh-autosuggestions` — 输入时灰色提示历史命令，按 `→` 补全
- `zsh-syntax-highlighting` — 正确命令绿色，错误命令红色

**如果需要安装这两个插件：**

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions \
    ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

git clone https://github.com/zsh-users/zsh-syntax-highlighting.git \
    ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

然后在 `~/.zshrc` 的 `plugins=()` 中添加对应名称。

### 3.4 关于 Powerlevel10k

当前使用 `ys` 主题已经足够简洁好用。如果你想要更炫的提示符（带 Git 状态图标、执行时间等），可以安装 p10k：

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git \
    ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

然后在 `~/.zshrc` 中改为 `ZSH_THEME="powerlevel10k/powerlevel10k"`，重启终端后会进入交互式配置向导。

**注意**：p10k 需要 Nerd Font 才能正常显示图标，否则会乱码。本机目前未检测到 Nerd Font，如需安装：

```bash
brew install --cask font-meslo-lg-nerd-font
```

### 3.5 Zsh 实用快捷键

| 快捷键 | 功能 |
|--------|------|
| `Ctrl + U` | 清除光标前内容 |
| `Ctrl + K` | 清除光标后内容 |
| `Ctrl + W` | 删除前一个单词 |
| `Ctrl + A` | 跳到行首 |
| `Ctrl + E` | 跳到行尾 |
| `Alt + ←/→` | 按单词移动光标 |
| `!!` | 执行上一条命令 |
| `!$` | 上一条命令的最后一个参数 |
| `Ctrl + R` | 模糊搜索历史命令 |

### 3.6 tmux

**当前状态**：✅ 已安装（brew install tmux），但未配置 `~/.tmux.conf`

```bash
brew install tmux
```

基础用法：

```bash
tmux new -s myproject    # 创建命名会话
tmux ls                  # 列出所有会话
tmux attach -t myproject # 重新连接会话
# 在 tmux 内: Ctrl+B 然后按 D → 断开会话
```

如果需要深度定制 tmux，推荐创建 `~/.tmux.conf`：

```bash
# 启用鼠标
set -g mouse on

# 前缀键从 Ctrl+B 改为 Ctrl+A
set -g prefix C-a
unbind C-b

# 256 色支持
set -g default-terminal "screen-256color"

# 增大回滚行数
set -g history-limit 50000

# 重载配置: Ctrl+A 然后按 r
bind r source-file ~/.tmux.conf \; display "Config reloaded!"
```

---

## 4. 版本控制：Git

### 4.1 当前配置

**本机实际 Git 配置**（从 `~/.gitconfig` 提取）：

```ini
[credential]
    helper = osxkeychain          # 密码存钥匙串，不需要每次输入
[user]
    name = kennem
    email = kennem@foxmail.com
[core]
    autocrlf = input              # macOS 防止换行符混乱
[http]
    proxy = http://127.0.0.1:10808   # HTTP 代理
[https]
    proxy = https://127.0.0.1:10808 # HTTPS 代理
```

### 4.2 推荐补充配置

Git 配置

```bash
# 默认分支名
git config --global init.defaultBranch main

# VS Code 作为默认编辑器（确认 code 命令在 PATH 中）
git config --global core.editor "code --wait"

# push 时自动设置远程分支
git config --global push.autoSetupRemote true

# pull 时默认 rebase
git config --global pull.rebase true
```

**推荐别名**（粘贴到 `~/.gitconfig` 的 `[alias]` 段）：

```ini
[alias]
    st = status
    co = checkout
    br = branch
    ci = commit
    lg = log --oneline --graph --decorate --all
    unstage = reset HEAD --
    last = log -1 HEAD
    undo = reset --soft HEAD~1      # ⚠️ 撤销上次 commit，保留改动
```

### 4.3 SSH 配置

**生成 SSH 密钥（首次配置时）：**

```bash
# ED25519 比 RSA 更安全更快
ssh-keygen -t ed25519 -C "kennem@foxmail.com"

# 启动 ssh-agent 并添加密钥
eval "$(ssh-agent -s)"
ssh-add --apple-use-keychain ~/.ssh/id_ed25519

# 复制公钥
pbcopy < ~/.ssh/id_ed25519.pub
```

将公钥添加到 GitHub → Settings → SSH and GPG keys。

**推荐 `~/.ssh/config`：**

```
Host *
    AddKeysToAgent yes
    UseKeychain yes
    IdentityFile ~/.ssh/id_ed25519

Host github.com
    HostName github.com
    User git
```

验证：`ssh -T git@github.com`

### 4.4 GitHub CLI (gh)

**当前状态**：✅ 已安装

```bash
brew install gh
gh auth login
```

常用操作：

```bash
gh repo clone owner/repo
gh pr create --title "feat: xxx" --body "描述"
gh pr status
gh pr checks
```

---

## 5. 开发工具

### 5.1 Android Studio

**加速 Gradle 构建**（`~/.gradle/gradle.properties`）：

```properties
org.gradle.jvmargs=-Xmx4096m -XX:MaxMetaspaceSize=512m -XX:+HeapDumpOnOutOfMemoryError
org.gradle.parallel=true
org.gradle.caching=true
org.gradle.daemon=true
android.useAndroidX=true
```

### 5.2 IntelliJ IDEA

JetBrains 全栈 IDE，用于 Java/Kotlin 开发。

### 5.3 Visual Studio Code

**推荐扩展**：GitLens、ESLint、Prettier、Markdown Preview Enhanced

### 5.4 Xcode

**当前状态**：✅ 已安装（完整版，路径 `/Applications/Xcode.app`）

包含完整的 iOS/macOS SDK、模拟器、Instruments 性能分析工具。

```bash
# 列出模拟器
xcrun simctl list devices

# 擦除模拟器数据
xcrun simctl erase all
```

### 5.5 其他开发相关

| 工具 | 状态 | 说明 |
|------|------|------|
| **Sublime Text** | ✅ 已安装 | 轻量文本编辑器 |
| **Figma** | ✅ 已安装 | UI/UX 设计（桌面端） |
| **Hacktivate** | ✅ 已安装 | 键盘快捷键学习 |
| **Swift Playground** | ✅ 已安装 | Swift 学习工具 |

---

## 6. 效率工具

### 6.1 Raycast（最重要，没有之一）

**当前状态**：✅ 已安装

Raycast 完全替代 Spotlight，内置了大量扩展。是本机最重要的效率工具。

```bash
brew install --cask raycast
```

安装后：
1. 系统设置 → 键盘 → 键盘快捷键 → 聚焦 → 取消 "显示聚焦搜索"
2. Raycast 快捷键设为 `Cmd + Space`

**最高频场景：**

| 功能 | 用法 |
|------|------|
| 应用启动 | 输入应用名，回车 |
| 剪贴板历史 | `Cmd + Shift + V`（可找回之前复制的内容） |
| 窗口管理 | 输入应用名后选左半屏/右半屏/全屏 |
| Snippets | 设定关键字自动展开常用文本 |
| 计算器 | 直接输入算式 |
| 货币换算 | `100 usd to cny` |
| 文件搜索 | 比 Finder 快 |
| 系统命令 | 锁屏、休眠、清空废纸篓 |

**推荐扩展**（Store 中搜索安装）：Clipboard History、Window Management、Snippets、Color Picker、Kill Process、Brew

### 6.2 Stats（菜单栏系统监控）

**当前状态**：✅ 已安装（brew cask 安装）

```bash
brew install --cask stats
```

菜单栏显示：CPU、内存、网络速度、磁盘、温度。

### 6.3 Karabiner-Elements（键盘映射）

macOS 最强键盘映射工具，可以：
- `caps_lock` → `left_control`（大写锁定键改 Control）
- `right_command` → `f18`（腾出一个独立快捷键）
- 导入社区规则：[ke-complex-modifications.pqrs.org](https://ke-complex-modifications.pqrs.org/)

```bash
brew install --cask karabiner-elements
```

### 6.4 BetterDisplay（显示器管理）

外接显示器必备：
- 解锁 HiDPI 分辨率
- 软件调节外接显示器亮度和音量
- 一键切换色彩模式

```bash
brew install --cask betterdisplay
```

### 6.5 CC Switch

快速切换 claude/codex/gemini/openclaw 配置

---

## 7. AI 工具

本机 AI 工具非常齐全，是我日常开发的核心辅助。

### 7.1 Claude Code

**当前状态**：✅ 已安装（npm 全局安装）

```bash
# 安装方式：本机通过 nvm 管理的 npm 全局安装
npm install -g @anthropic-ai/claude-code
# 本机版本: @anthropic-ai/claude-code@2.1.126
# PATH 中的 claude 位置:
#   /Users/ken/.nvm/versions/node/v22.22.1/bin/claude
#   /opt/homebrew/bin/claude
#   /Users/ken/.local/bin/claude
```

启动：在终端输入 `claude`

### 7.2 ChatGPT 桌面端

**当前状态**：✅ 已安装

OpenAI 官方桌面客户端。手动安装或 `brew install --cask chatgpt`。

### 7.3 Ollama（本地大模型）

**当前状态**：✅ 已安装（CLI + GUI 桌面端）

`ollama` CLI 通过 brew formula 安装，`Ollama.app` 桌面端存在且为真实 app bundle。

在本地运行 LLM，数据完全本地，无需联网。

```bash
brew install ollama
# 或桌面版
brew install --cask ollama
```

```bash
# 拉取模型
ollama pull llama3.2
ollama pull qwen2.5-coder:7b

# 运行
ollama run llama3.2

# 查看已有模型
ollama list

# 启动 API 服务
ollama serve
```

### 7.4 Gemini CLI

**当前状态**：✅ 已安装（`gemini-cli`）

Google AI 的命令行工具。

```bash
brew install gemini-cli
```

### 7.5 Codex / OpenCode

**当前状态**：✅ 已安装（`opencode`、`Codex.app`、`CodexBar.app`）

OpenAI 的编程 CLI 和桌面工具。

```bash
brew install opencode
```

### 7.6 Antigravity

**当前状态**：✅ 已安装（手动安装）

AI 编程辅助工具。

---

## 8. 网络与代理

### 8.1 代理设置

**本机实际配置**：

- Git 代理：`http://127.0.0.1:10808` / `https://127.0.0.1:10808`（已配置在 `~/.gitconfig`）

**已安装的代理相关应用**：

| 工具 | 状态 | 用途 |
|------|------|------|
| **v2rayN.app** | ✅ 已安装 | 网络代理客户端（注意：v2rayN 原生是 Windows 应用，macOS 版功能有限） |
| **ProxyBridge.app** | ✅ 已安装 | 代理桥接 |

**macOS 命令行代理开关**（添加到 `~/.zshrc`）：

```bash
alias proxy='export http_proxy=http://127.0.0.1:10809 https_proxy=http://127.0.0.1:10809 all_proxy=socks5://127.0.0.1:10808'
alias unproxy='unset http_proxy https_proxy all_proxy'
```

### 8.2 Wireshark

网络抓包分析，排查网络问题的终极工具。

```bash
brew install --cask wireshark
# 安装后配置抓包权限:
sudo chmod 644 /dev/bpf*
```

---

## 9. 写作与笔记

### 9.1 Typora

所见即所得的 Markdown 编辑器，实时渲染，写作体验极佳。

```bash
brew install --cask typora
```

**推荐配置**（偏好设置 → 图像）：
- 插入图片时：复制到指定文件夹 `./assets`
- 图片上传服务：PicGo（配合使用，自动上传图片到云端）

**主题推荐**：`Newsprint`（暖色调）、`Github`（清爽）、`Night`（暗色）

### 9.2 PicGo（图床）

配合 Typora 使用，粘贴图片自动上传到云端图床。

```bash
brew install --cask picgo
```

常用图床：GitHub（免费）、SM.MS（免费 5MB）、腾讯云 COS（收费快）、阿里云 OSS

### 9.3 Obsidian

本地 Markdown 知识库，核心特色是 `[[双向链接]]`。

```bash
brew install --cask obsidian
```

**核心概念**：
- Vault：一个文件夹就是一个知识库
- 双向链接：`[[笔记名]]` 自动关联
- 图谱视图：可视化笔记关联
- Canvas：白板式布局

**推荐社区插件**：Calendar、Dataview、Templater、Excalidraw、Git（自动备份）

**同步方案**：iCloud（免费，Apple 设备间）、Git 插件（免费）、Obsidian Sync（官方付费）

---

## 10. 容器与虚拟化

### 10.1 OrbStack

Docker Desktop 的轻量级替代，macOS 原生，启动快、内存占用低。

```bash
brew install --cask orbstack
```

**实际证据**：SSH config 中已自动添加 `Include ~/.orbstack/ssh/config`

**核心优势**：
- 冷启动 < 2 秒（vs Docker Desktop 30 秒+）
- Apple Silicon 原生支持，Rosetta 运行 x86 镜像
- 内置轻量 k3s Kubernetes
- 支持 Linux 虚拟机

```bash
# docker 命令完全兼容
docker ps
docker compose up -d

# 创建 Linux 虚拟机
orb create ubuntu myvm
orb ssh myvm
orb list
```

---

## 11. 多媒体与办公

### 11.1 IINA

macOS 最好用的视频播放器，基于 mpv，原生设计。

```bash
brew install --cask iina
```

特色：所有格式通吃、在线字幕搜索、画中画、Touch Bar 适配。

### 11.2 Keka

压缩解压工具，支持 7z/Zip/RAR/Tar 等格式。

```bash
brew install --cask keka
```

### 11.3 WPS Office

国内最实用的 Office 套件。

```bash
brew install --cask wpsoffice
```

### 11.4 ffmpeg

**当前状态**：✅ 已安装（`brew install ffmpeg`）

万能音视频命令行工具：

```bash
# 转换格式
ffmpeg -i input.mov output.mp4

# 压缩（H.265）
ffmpeg -i input.mp4 -c:v libx265 -crf 28 output.mp4

# 提取音频
ffmpeg -i input.mp4 -q:a 0 -map a output.mp3
```

### 11.5 其他

| 工具 | 状态 | 用途 |
|------|------|------|
| **GarageBand** | ✅ 已安装 | 免费音乐制作 |
| **iMovie** | ✅ 已安装 | 免费视频剪辑 |
| ** BaiduNetdisk** | ✅ 已安装 | 百度网盘（实际文件名 `BaiduNetdisk_mac.app`） |

### 11.6 Apple 创作者工具

| 工具 | 状态 |
|------|------|
| **Keynote Creator Studio** | ✅ 已安装 |
| **Pages Creator Studio** | ✅ 已安装 |
| **Numbers Creator Studio** | ✅ 已安装 |
| **Logic Pro Creator Studio** | ✅ 已安装 |
| **Pixelmator Pro Creator Studio** | ✅ 已安装 |

---

## 12. 系统设置优化

以下是我验证过的 macOS 系统偏好调整，每次重装后必做：

### Finder

```bash
# 显示文件扩展名
defaults write NSGlobalDomain AppleShowAllExtensions -bool true

# 显示路径栏
defaults write com.apple.finder ShowPathbar -bool true

# 显示状态栏
defaults write com.apple.finder ShowStatusBar -bool true

# 搜索默认搜当前文件夹
defaults write com.apple.finder FXDefaultSearchScope -string "SCcf"

# 显示隐藏文件
defaults write com.apple.finder AppleShowAllFiles -bool true

killall Finder
```

### Dock

```bash
# 自动隐藏
defaults write com.apple.dock autohide -bool true

# 去除显示延迟，更流畅
defaults write com.apple.dock autohide-delay -float 0
defaults write com.apple.dock autohide-time-modifier -float 0.3

# 图标大小
defaults write com.apple.dock tilesize -int 36

killall Dock
```

### 触控板

```bash
# 轻点点击
defaults write com.apple.AppleMultitouchTrackpad Clicking -bool true

# 三指拖移
defaults write com.apple.AppleMultitouchTrackpad TrackpadThreeFingerDrag -bool true
```

### 键盘

```bash
# 关闭按住出音标（加快重复速度）
defaults write NSGlobalDomain ApplePressAndHoldEnabled -bool false

# 加速键盘响应
defaults write NSGlobalDomain KeyRepeat -int 2
defaults write NSGlobalDomain InitialKeyRepeat -int 15
```

### 截图

```bash
# 截图保存到指定文件夹
mkdir -p ~/Screenshots
defaults write com.apple.screencapture location -string "~/Screenshots"

# 截图去除阴影
defaults write com.apple.screencapture disable-shadow -bool true
```

---

## 13. Dotfiles 管理

把配置文件用 Git 管理起来，重装系统时一键恢复：

```bash
mkdir -p ~/dotfiles
cd ~/dotfiles
git init

# 备份核心配置
cp ~/.zshrc ~/dotfiles/zshrc
cp ~/.gitconfig ~/dotfiles/gitconfig
cp ~/.ssh/config ~/dotfiles/ssh_config

git add .
git commit -m "backup dotfiles"
git remote add origin git@github.com:kennem/dotfiles.git
git push -u origin main
```

恢复时：

```bash
git clone git@github.com:kennem/dotfiles.git ~/dotfiles
ln -sf ~/dotfiles/zshrc ~/.zshrc
ln -sf ~/dotfiles/gitconfig ~/.gitconfig
ln -sf ~/dotfiles/ssh_config ~/.ssh/config
```

也可以使用专业 dotfiles 管理工具：`brew install chezmoi` 或 `brew install yadm`

---

## 14. 一键恢复脚本

以下脚本基于本机实际安装清单，逐条验证。**每个命令都需要你二次确认后执行**。

```bash
#!/bin/bash
# Mac 恢复安装脚本 - 基于 2026-05-03 实际配置
# 使用前：逐条确认命令，不要全量盲跑

set -e

echo "===== 1. Xcode Command Line Tools ====="
echo "确认安装？(y/n)"; read -r ans; [ "$ans" != "y" ] && exit 0
xcode-select --install 2>/dev/null || echo "→ 已安装"

echo "===== 2. Homebrew (版本 5.x) ====="
echo "确认安装？(y/n)"; read -r ans; [ "$ans" != "y" ] && exit 0
if ! command -v brew &> /dev/null; then
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
    eval "$(/opt/homebrew/bin/brew shellenv)"
fi

echo "===== 3. CLI 工具 ====="
echo "将安装: gh go node python@3.13 openjdk@17 ripgrep tmux ffmpeg fastfetch tree"
echo "确认？(y/n)"; read -r ans; [ "$ans" != "y" ] && exit 0
brew install gh go node python@3.13 openjdk@17 ripgrep tmux ffmpeg fastfetch tree

echo "===== 4. GUI 应用 (brew cask) ====="
echo "将安装: stats orbstack keka iina typora picgo obsidian wpsoffice raycast iterm2 google-chrome"
echo "确认？(y/n)"; read -r ans; [ "$ans" != "y" ] && exit 0
brew install --cask stats orbstack keka iina typora picgo obsidian wpsoffice raycast iterm2 google-chrome

echo "===== 5. Oh My Zsh ====="
echo "确认安装？(y/n)"; read -r ans; [ "$ans" != "y" ] && exit 0
if [ ! -d ~/.oh-my-zsh ]; then
    sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended
fi

echo "===== 6. Zsh 插件 ====="
echo "将安装: zsh-autosuggestions, zsh-syntax-highlighting"
echo "确认？(y/n)"; read -r ans; [ "$ans" != "y" ] && exit 0
ZSH_CUSTOM=${ZSH_CUSTOM:-~/.oh-my-zsh/custom}
git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions 2>/dev/null || echo "→ autosuggestions 已存在"
git clone https://github.com/zsh-users/zsh-syntax-highlighting $ZSH_CUSTOM/plugins/zsh-syntax-highlighting 2>/dev/null || echo "→ syntax-highlighting 已存在"

echo "===== 7. Git 初始配置 ====="
echo "确认配置 Git？(y/n)"; read -r ans; [ "$ans" != "y" ] && exit 0
git config --global init.defaultBranch main
git config --global push.autoSetupRemote true
git config --global pull.rebase true

echo "===== 8. macOS 系统偏好 ====="
echo "将修改 Finder/Dock/触控板/键盘设置。确认？(y/n)"; read -r ans; [ "$ans" != "y" ] && exit 0
defaults write NSGlobalDomain AppleShowAllExtensions -bool true
defaults write com.apple.finder ShowPathbar -bool true
defaults write com.apple.finder FXDefaultSearchScope -string "SCcf"
defaults write com.apple.dock autohide -bool true
defaults write com.apple.dock tilesize -int 36
defaults write com.apple.dock autohide-delay -float 0
defaults write com.apple.AppleMultitouchTrackpad Clicking -bool true
defaults write NSGlobalDomain ApplePressAndHoldEnabled -bool false
killall Finder Dock

echo "===== 完成 ====="
echo "建议重启终端: exec zsh"
```

保存为 `setup-mac.sh` 后使用：

```bash
chmod +x setup-mac.sh
./setup-mac.sh
# 每一步都会要求确认，安全可控
```

