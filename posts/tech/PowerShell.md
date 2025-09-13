---
title : 'PowerShell 详细使用'
date : 2025-03-16T10:30:13+08:00
lastmod: 2025-03-16T10:40:13+08:00
description : "PowerShell学习笔记" 
categories : ["PowerShell学习笔记"]
tags : ["PowerShell"]
---

# PowerShell 详细使用

## 一、PowerShell 简介

PowerShell 是微软开发的一种命令行外壳和脚本语言，它基于 .NET 框架，不仅适用于 Windows 系统，也在 Linux 和 macOS 上提供支持。与传统的命令提示符相比，PowerShell 处理的是对象而非纯文本，使得数据传递和管理更为灵活高效。无论是日常管理任务还是复杂的自动化操作，PowerShell 都是非常强大的工具。

## 二、基本概念和语法

### 1. Cmdlet（命令单元）

- **定义**：Cmdlet 是 PowerShell 内置的轻量级命令，遵循“动词-名词”命名规则，如 `Get-Process`、`Set-Item`。
- **示例**：`Get-Process` 用于获取当前运行的进程列表。

### 2. 变量与数据类型

- 变量声明：使用 `$`符号，例如：

  ```powershell
  $greeting = "Hello World"
  ```

- **数据类型**：PowerShell 支持字符串、整数、布尔值、数组、哈希表等数据类型。

### 3. 管道（Pipeline）

- **概念**：管道符号 `|` 用于将一个命令的输出传递给下一个命令。

- 示例：

  ```powershell
  Get-Process | Sort-Object -Property CPU -Descending
  ```

  上述命令获取进程列表并按 CPU 占用降序排序。

### 4. 对象处理

- PowerShell 输出的是 .NET 对象，这使得你可以直接访问属性和方法，而不仅仅是文本。例如：

  ```powershell
  $process = Get-Process | Where-Object { $_.ProcessName -eq "notepad" }
  $process.Id  # 访问进程ID
  ```

### 5. 脚本文件

- **扩展名**：脚本文件以 `.ps1` 结尾。

- 执行方式：在 PowerShell 控制台中执行脚本，例如：

  ```powershell
  .\MyScript.ps1
  ```

## 三、常用命令及使用示例

### 1. 帮助与命令查找

- 获取帮助：

  ```powershell
  Get-Help Get-Process -Detailed
  ```

- 列出所有命令：

  ```powershell
  Get-Command
  ```

- 更新帮助文档：

  ```powershell
  Update-Help
  ```

### 2. 文件系统管理

- 列出目录内容（等同于 `ls` 或 `dir`）：

  ```powershell
  Get-ChildItem -Path C:\
  ```

- 切换目录：

  ```powershell
  Set-Location -Path C:\Users
  ```

- 复制文件或文件夹：

  ```powershell
  Copy-Item -Path .\file.txt -Destination D:\Backup\
  ```

- 移动文件或文件夹：

  ```powershell
  Move-Item -Path .\file.txt -Destination D:\Archive\
  ```

- 删除文件或文件夹：

  ```powershell
  Remove-Item -Path .\oldfile.txt
  ```

### 3. 进程与服务管理

- 获取进程信息：

  ```powershell
  Get-Process
  ```

- 停止指定进程：

  ```powershell
  Stop-Process -Name notepad
  ```

- 查看服务状态：

  ```powershell
  Get-Service
  ```

- 管理服务（启动、停止、重启）：

  ```powershell
  Start-Service -Name "wuauserv"
  Stop-Service -Name "wuauserv"
  Restart-Service -Name "wuauserv"
  ```

### 4. 环境变量管理

- 查看环境变量：

  ```powershell
  Get-ChildItem Env:
  ```

- 设置环境变量：

  ```powershell
  $Env:Path = "$Env:Path;C:\MyFolder"
  ```

### 5. 脚本与函数

- 定义和调用函数：

  ```powershell
  function Say-Hello {
      param([string]$Name)
      Write-Output "Hello, $Name!"
  }
  Say-Hello -Name "Alice"
  ```

- 条件判断与循环：

  ```powershell
  if ($x -gt 10) {
      Write-Output "大于10"
  } else {
      Write-Output "小于等于10"
  }
  
  for ($i = 1; $i -le 5; $i++) {
      Write-Output "数字：$i"
  }
  ```

### 6. 管道与对象操作

- 筛选与遍历：

  ```powershell
  Get-Service | Where-Object { $_.Status -eq "Running" } | ForEach-Object { $_.Name }
  ```

- 排序与格式化：

  ```powershell
  Get-Process | Sort-Object -Property CPU -Descending | Format-Table -Property Name, CPU, Id
  ```

### 7. 远程管理

- 启用远程功能：

  ```powershell
  Enable-PSRemoting -Force
  ```

- 进入远程会话：

  ```powershell
  Enter-PSSession -ComputerName <RemoteComputer>
  ```

- 执行远程命令：

  ```powershell
  Invoke-Command -ComputerName <RemoteComputer> -ScriptBlock { Get-Process }
  ```

### 8. 模块管理

- 查看已安装模块：

  ```powershell
  Get-Module -ListAvailable
  ```

- 导入模块：

  ```powershell
  Import-Module ModuleName
  ```

- 安装新模块

  （需安装 PowerShellGet 模块）：

  ```powershell
  Install-Module ModuleName
  ```

### 9. 执行策略（Execution Policy）

- 查看当前策略：

  ```powershell
  Get-ExecutionPolicy
  ```

- 设置执行策略

  （例如 RemoteSigned）：

  ```powershell
  Set-ExecutionPolicy RemoteSigned
  ```

  > 注意：修改执行策略时要确保了解其安全影响，防止执行未知来源的脚本。

### ⭐查看 某个文件的 sha256 哈希值

```shell
Get-FileHash "path" -Algorithm SHA256
```

获取小写

```shell
(Get-FileHash "C:\path\to\your\file.zip" -Algorithm SHA256).Hash.ToLower()
```

------

#### 1. 更换哈希算法

除了 SHA256，还可以指定其他算法，例如 SHA512、MD5、SHA1 等。示例：

```powershell
# 使用 SHA512 算法计算文件哈希值
Get-FileHash "C:\path\to\your\file.zip" -Algorithm SHA512
```

------

##### 2. 对比两个文件的哈希值

可用于验证两个文件是否一致：

```powershell
$hash1 = (Get-FileHash "C:\path\to\your\file1.zip" -Algorithm SHA256).Hash.ToLower()
$hash2 = (Get-FileHash "C:\path\to\your\file2.zip" -Algorithm SHA256).Hash.ToLower()

if ($hash1 -eq $hash2) {
    Write-Output "两个文件的哈希值相同，文件内容一致。"
} else {
    Write-Output "两个文件的哈希值不同，文件内容不一致。"
}
```

------

##### 3. 计算文件夹中所有文件的哈希值

结合 **Get-ChildItem** 和 **ForEach-Object** 可以批量计算文件夹中所有文件的哈希值：

```powershell
Get-ChildItem "C:\path\to\folder" -File | ForEach-Object {
    $fileHash = (Get-FileHash $_.FullName -Algorithm SHA256).Hash.ToLower()
    [PSCustomObject]@{
        FileName = $_.Name
        Hash     = $fileHash
    }
} | Format-Table -AutoSize
```

该命令遍历指定目录下的所有文件，并输出文件名与对应的 SHA256 哈希值。

------

##### 4. 将哈希值输出到文件

如果需要将哈希值保存到文件中，可使用 **Out-File**：

```powershell
(Get-FileHash "C:\path\to\your\file.zip" -Algorithm SHA256).Hash.ToLower() | Out-File "C:\path\to\output\hash.txt"
```

------

##### 5. 验证文件完整性

假设你已有一个已知的预期哈希值，可以将计算得到的哈希值与预期值进行比对，判断文件是否被篡改：

```powershell
$expectedHash = "abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890"  # 请替换为实际的预期哈希值
$actualHash = (Get-FileHash "C:\path\to\your\file.zip" -Algorithm SHA256).Hash.ToLower()

if ($expectedHash -eq $actualHash) {
    Write-Output "文件完整性验证通过。"
} else {
    Write-Output "文件完整性验证失败！"
}
```

------

##### 6. 结合脚本批量验证多个文件

如果你需要对多个文件进行完整性验证，可以把文件路径和预期哈希值保存在一个数据结构中，然后遍历验证：

```powershell
# 定义待验证的文件及其预期哈希值列表
$files = @(
    @{ Path = "C:\path\to\file1.zip"; ExpectedHash = "预期哈希值1" },
    @{ Path = "C:\path\to\file2.zip"; ExpectedHash = "预期哈希值2" }
)

foreach ($file in $files) {
    $actualHash = (Get-FileHash $file.Path -Algorithm SHA256).Hash.ToLower()
    if ($actualHash -eq $file.ExpectedHash.ToLower()) {
        Write-Output "$($file.Path) 验证通过。"
    } else {
        Write-Output "$($file.Path) 验证失败！"
    }
}
```

## 四、脚本编写与调试技巧

### 1. 脚本文件

- **编写与保存**：将常用命令写入 `.ps1` 文件中保存，便于重复使用。

- 执行脚本：

  ```powershell
  .\MyScript.ps1
  ```

### 2. 调试方法

- **输出调试信息**：使用 `Write-Output` 或 `Write-Host` 输出变量和状态。

- 逐步调试：可以启用逐行调试

  ```powershell
  Set-PSDebug -Step
  ```

- 错误处理：使用 Try-Catch 结构捕获和处理错误：

  ```powershell
  try {
      Get-Item "C:\nonexistentfile.txt"
  } catch {
      Write-Host "错误信息：$_"
  }
  ```

## 五、PowerShell 高级特性

### 1. 对象与管道的优势

- **对象传递**：不同于传统命令行将文本传递，PowerShell 传递的是对象，允许你直接操作属性和方法，极大地简化了数据处理流程。

### 2. 数据格式转换

- 转换为 JSON 或 XML：

  ```powershell
  Get-Process | ConvertTo-Json
  Get-Service | ConvertTo-XML
  ```

- 输出到文件：

  ```powershell
  Get-Process | Out-File -FilePath processes.txt
  ```

### 3. 自动化任务与计划任务

- **定时执行脚本**：可结合 Windows 任务计划程序（Task Scheduler）或 PowerShell 内置的 ScheduledJob 模块实现自动化任务。
- **批量管理**：利用 `Invoke-Command` 同时管理多台服务器，提高系统管理效率。

### 4. PowerShell Remoting

- **远程会话**：允许你通过网络远程管理计算机，是系统管理员日常管理服务器的重要工具。

## 六、结合Office

获取当前最占内存的十个进程，并在Excel生成饼图：

```powershell
# create new excel instance
 $objExcel = New-Object -comobject Excel.Application
 $objExcel.Visible = $True
 $objWorkbook = $objExcel.Workbooks.Add()
 $objWorksheet = $objWorkbook.Worksheets.Item(1)

# write information to the excel file
$i = 0
$first10 = (ps | sort ws -Descending | select -first 10)
$first10 | foreach -Process {$i++; $objWorksheet.Cells.Item($i,1) = $_.name; $objWorksheet.Cells.Item($i,2) = $_.ws}
$otherMem = (ps | measure ws -s).Sum - ($first10 | measure ws -s).Sum
$objWorksheet.Cells.Item(11,1) = "Others"; $objWorksheet.Cells.Item(11,2) = $otherMem

# draw the pie chart
$objCharts = $objWorksheet.ChartObjects()
$objChart = $objCharts.Add(0, 0, 500, 300)
$objChart.Chart.SetSourceData($objWorksheet.range("A1:B11"), 2)
$objChart.Chart.ChartType = 70
$objChart.Chart.ApplyDataLabels(5)
```

**参考资料：**

- [微软官方 PowerShell 文档](https://docs.microsoft.com/zh-cn/powershell/)
