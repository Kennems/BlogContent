---
title : 'Conda使用'
date : 2025-02-23T22:30:13+08:00
lastmod: 2025-02-23T22:20:13+08:00
description : "Conda使用" 
image : img/cat.jpg
draft : false    
categories : ["深度学习"]
tags : ["深度学习"]
---

# Conda使用

## 使 Conda 可以在 PowerShell 中使用

在 PowerShell 中启用 `conda`，通常需要确保 `conda` 的路径已添加到系统环境变量中。可以按照以下步骤操作：

### 1. 安装 Anaconda 或 Miniconda

如果你还没有安装 Anaconda 或 Miniconda，可以先去其官网下载安装包并进行安装：

- [Anaconda 官网](https://www.anaconda.com/products/distribution)
- [Miniconda 官网](https://docs.conda.io/en/latest/miniconda.html)

### 2. 设置 `conda` 在 PowerShell 中启用

#### 方式一：通过 Anaconda Prompt 启动 PowerShell

1. **打开 Anaconda Prompt**。

2. 输入以下命令：

   ```bash
   conda init powershell
   ```

   该命令会将 `conda` 初始化配置添加到 PowerShell 配置文件中。

3. 关闭并重新启动 PowerShell，然后你就能直接在 PowerShell 中使用 `conda` 命令了。

#### 方式二：手动配置

如果 `conda init powershell` 没有生效，可以手动将 Conda 安装目录添加到 PowerShell 配置文件中。

1. **找到 Conda 安装路径**，通常位于以下位置（取决于你安装的是 Anaconda 还是 Miniconda）：

   - Anaconda: `C:\Users\<YourUsername>\Anaconda3`
   - Miniconda: `C:\Users\<YourUsername>\Miniconda3`

2. **打开 PowerShell 配置文件**。如果是第一次配置，可能需要手动创建配置文件。输入以下命令打开 PowerShell 配置文件：

   ```powershell
   notepad.exe $PROFILE
   ```

   如果没有文件，PowerShell 会提示你创建一个。

3. **添加 Conda 安装路径**，在文件中加入以下行：

   ```powershell
   $condaPath = "C:\Users\<YourUsername>\Anaconda3\Scripts"
   $env:Path = $condaPath + ";" + $env:Path
   ```

   替换 `<YourUsername>` 为你系统中的用户名，并根据实际情况修改 Anaconda 或 Miniconda 的路径。

4. **保存并关闭文件**，然后重启 PowerShell。

### 3. 检查是否成功

打开 PowerShell，输入以下命令：

```powershell
conda --version
```

如果安装和配置成功，你应该能看到类似于 `conda 4.x.x` 的版本号输出。

### 4. 激活环境

你现在可以在 PowerShell 中激活 `conda` 环境了，使用命令：

```powershell
conda activate <env_name>
```

如果你没有创建任何环境，默认情况下可以激活 `base` 环境：

```powershell
conda activate
```

这样，`conda` 就能在 PowerShell 中正常工作了。

## Conda 常用命令使用

### 1. 创建和管理环境

- **创建新环境**：

  ```bash
  conda create --name <env_name> python=<version>
  ```

  例如，创建一个名为 `myenv` 的环境，使用 Python 3.8：

  ```bash
  conda create --name myenv python=3.8
  ```

- **激活环境**：

  ```bash
  conda activate <env_name>
  ```

  例如，激活 `myenv` 环境：

  ```bash
  conda activate myenv
  ```

- **退出当前环境**：

  ```bash
  conda deactivate
  ```

- **列出所有环境**：

  ```bash
  conda env list
  ```

- **删除环境**：

  ```bash
  conda remove --name <env_name> --all
  ```

### 2. 包管理

- **安装包**：

  ```bash
  conda install <package_name>
  ```

  例如，安装 `numpy` 包：

  ```bash
  conda install numpy
  ```

- **安装特定版本的包**：

  ```bash
  conda install <package_name>=<version>
  ```

  例如，安装 `numpy` 1.21.0：

  ```bash
  conda install numpy=1.21.0
  ```

- **查看已安装的包**：

  ```bash
  conda list
  ```

- **更新包**：

  ```bash
  conda update <package_name>
  ```

  例如，更新 `numpy` 包：

  ```bash
  conda update numpy
  ```

- **更新所有包**：

  ```bash
  conda update --all
  ```

- **卸载包**：

  ```bash
  conda remove <package_name>
  ```

### 3. 环境导出与导入

- **导出环境配置文件**（生成 `.yml` 文件）：

  ```bash
  conda env export > environment.yml
  ```

- **通过环境配置文件创建环境**：

  ```bash
  conda env create -f environment.yml
  ```

### 4. 查找和搜索包

- **搜索包**：

  ```bash
  conda search <package_name>
  ```

- **查看包的信息**：

  ```bash
  conda info <package_name>
  ```

### 5. 配置与管理

- **查看conda配置信息**：

  ```bash
  conda config --show
  ```

- **添加conda源**：

  ```bash
  conda config --add channels <channel_name>
  ```

- **列出conda的所有配置项**：

  ```bash
  conda config --show channels
  ```



## pip 换源

https://www.cnblogs.com/hack747/articles/18515020

## conda换源

原来的源

```
channels:
  - https://repo.anaconda.com/pkgs/main
  - https://repo.anaconda.com/pkgs/r
```

替换：

```
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```



