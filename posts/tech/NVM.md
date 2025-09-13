---
title : 'NVM使用（Linux）'
date : 2025-03-16T10:30:13+08:00
lastmod: 2025-03-16T10:40:13+08:00
description : "NVM使用" 
categories : ["NVM使用"]
tags : ["NVM"]
---

# NVM使用（Linux）

在 **Linux** 上安装 `nvm`（Node Version Manager）的方法如下：

------

## **1. 安装 NVM**

在 Linux 上，可以使用 `curl` 或 `wget` 安装 `nvm`。

### **方法 1：使用 `curl`（推荐）**

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.4/install.sh | bash
```

### **方法 2：使用 `wget`**

```bash
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.4/install.sh | bash
```

**⚠️ 注意：**
 `v0.39.4` 是 `nvm` 最新稳定版，建议去 [官方 GitHub](https://github.com/nvm-sh/nvm/releases) 检查最新版本。

------

## **2. 加载 NVM**

安装完成后，你需要让 `nvm` 生效：

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

为了让 `nvm` 每次打开终端都可用，可以把上面的代码加到 `~/.bashrc` 或 `~/.zshrc`：

```bash
echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.bashrc
echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ~/.bashrc
source ~/.bashrc  # 使其生效
```

如果你使用的是 `zsh`：

```bash
echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.zshrc
echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ~/.zshrc
source ~/.zshrc  # 使其生效
```

------

## **3. 验证 NVM 是否安装成功**

运行：

```bash
nvm --version
```

如果能正确输出版本号，说明安装成功。

------

## **4. 安装 Node.js**

### **安装最新稳定版**

```bash
nvm install --lts
```

### **安装指定版本**

```bash
nvm install 18
```

### **查看已安装的 Node.js 版本**

```bash
nvm list
```

### **切换 Node.js 版本**

```bash
nvm use 18
```

### **设置默认版本**

```bash
nvm alias default 18
```

------

## **5. 验证 Node.js 安装**

```bash
node -v
npm -v
```

如果 `node -v` 输出正确的版本号，说明安装成功 🎉！

------

### **6. 可能遇到的问题**

#### **(1) `nvm: command not found`**

**解决方法：**

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

然后重新启动终端或执行：

```bash
source ~/.bashrc  # 或 source ~/.zshrc
```

#### **(2) 终端关闭后 `nvm` 失效**

检查 `~/.bashrc` 或 `~/.zshrc` 是否有 `nvm` 配置：

```bash
cat ~/.bashrc | grep nvm
```

如果没有，就手动添加：

```bash
echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.bashrc
echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ~/.bashrc
source ~/.bashrc
```

------

这就是在 Linux 上安装和使用 `nvm` 的完整步骤！🚀
