---
title : 'Linux扩展磁盘空间大小'
date : 2025-01-04T15:37:01+08:00
lastmod: 2025-02-04T15:37:01+08:00
description : "Linux" 
image : img/cat.jpg
draft : false    
categories : ["Linux"]
tags : ["Linux笔记"]
# password : leetcode
---

## Linux扩展磁盘空间大小

## sda

在 Linux 中，`sda` 是系统中常见的块设备名称，用于表示磁盘设备。

- **`sda`** 是系统中第一个 **SCSI (Small Computer System Interface)** 或 **SATA (Serial ATA（**Advanced Technology Attachment**）)** 磁盘的标识符，通常代表硬盘或固态硬盘。
- 在 Linux 系统中，所有的存储设备（如硬盘、SSD、U盘）都会被标记为块设备，使用类似 `sdX` 的命名规则，其中：
- - **`sd`** 表示 `SCSI/SATA` 设备（尽管现在也适用于大多数现代磁盘，包括 `NVMe` 磁盘）。
  - **`a`** 表示第一个磁盘，`b` 是第二个磁盘，`c` 是第三个磁盘，依此类推。

##  sda 的分区**

磁盘（如 `sda`）可以被分成多个分区，每个分区也有自己的标识符。例如：

- **`sda1`**：表示 `sda` 磁盘的第一个分区。
- **`sda2`**：表示 `sda` 磁盘的第二个分区。
- **`sda3`**：表示 `sda` 磁盘的第三个分区。

## 查看sda以及其他磁盘信息

`lsblk` 用于列出所有块设备及其挂载点：

```bash
lsblk
```

`fdisk` 可以查看磁盘分区表：

```
sudo fdisk -l
```

`df` 查看每个分区的磁盘使用情况：

```
df -h
```

`du`（disk usage）命令用于查看指定目录的磁盘使用情况。常用的命令是：

```
du -sh /path/to/directory
```

### **其他常见的设备命名**

| 名称      | 说明                                           |
| --------- | ---------------------------------------------- |
| `sda`     | 第一个 SCSI/SATA 磁盘。                        |
| `sdb`     | 第二个 SCSI/SATA 磁盘。                        |
| `nvme0n1` | 第一个 NVMe 磁盘（用于 NVMe 协议的固态硬盘）。 |
| `vda`     | 虚拟磁盘（在虚拟机中常见）。                   |
| `sr0`     | 光驱设备（如 CD/DVD 驱动器）。                 |
| `loop`    | 环回设备（如 ISO 文件挂载时生成的虚拟磁盘）。  |

## 扩展卷组的可用空间

从你的输出来看，`lvextend` 命令无法扩展 `/dev/centos/root` 逻辑卷，因为卷组 `centos` 中没有足够的空闲空间。需要首先扩展卷组的可用空间，才能继续扩展逻辑卷。

### 解决方法：

#### 1. **扩展物理卷（PV）**

你可以将 `/dev/sda` 设备的剩余空间添加到 LVM 卷组 `centos` 中，首先需要在物理卷上分配剩余的空间。

##### 1.1**创建分区**： 

使用 `fdisk` 或 `parted` 工具在剩余的空间上创建新分区，记住新分区的类型需要设置为 `8e`（Linux LVM）。

使用 `fdisk`：

```bash
fdisk /dev/sda
```

在交互式界面中，创建一个新的分区并将其设置为 `8e` 类型。

##### 详细步骤

为了在 `/dev/sda` 上创建一个新的分区，你可以按照以下步骤使用 `fdisk` 工具来操作：

##### 步骤 1：启动 `fdisk` 工具

首先，运行 `fdisk` 命令来修改磁盘分区（假设目标磁盘是 `/dev/sda`）：

```bash
fdisk /dev/sda
```

##### 步骤 2：查看现有分区

输入 `p` 来显示当前的分区表：

```
Command (m for help): p
```

这将显示类似以下的分区信息：

```
Disk /dev/sda: 64.4 GB, 64424509440 bytes, 125829120 sectors
Units = sectors of 1 * 512 = 512 bytes
Disk label type: dos
Disk identifier: 0x000ed922

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048      411647      204800   83  Linux
/dev/sda2          411648    83886079    41737216   8e  Linux LVM
```

##### 步骤 3：创建新分区

输入 `n` 来创建新分区：

```
Command (m for help): n
```

你将被提示选择分区类型：

- `p` 用于主分区。
- `e` 用于扩展分区（如果有需要）。

通常，你选择 `p` 创建主分区。

接下来，输入分区号。你可以选择默认的分区号，通常是下一个可用的编号。

接下来，会要求你指定起始和结束位置。你可以按提示选择默认值，或者手动输入起始和结束扇区。**确保选择适当的空间**（例如，你可以选择使用磁盘的剩余空间）。

##### 步骤 4：设置分区类型

输入 `t` 来设置分区类型：

```
Command (m for help): t
```

然后，你需要设置分区类型为 `8e`，即 `Linux LVM` 类型：

```
Hex code (type L to list all codes): 8e
```

##### 步骤 5：保存并退出

一旦完成分区创建，你可以输入 `w` 来写入分区表并退出 `fdisk`：

```
Command (m for help): w
```

这将保存更改并退出。

##### 步骤 6：刷新分区表

执行完 `w` 后，系统可能需要重新加载分区表。你可以使用以下命令来刷新：

```bash
partprobe /dev/sda
```

##### 步骤 7：创建物理卷（PV）

现在，创建的分区（假设是 `/dev/sda3`）需要被设置为物理卷。使用 `pvcreate` 来创建物理卷：

```bash
pvcreate /dev/sda3
```

##### 步骤 8：扩展卷组（VG）

之后，你可以将新的物理卷添加到现有的卷组（假设是 `centos`）中：

```bash
vgextend centos /dev/sda3
```

##### 1.2、**刷新分区表**： 

在创建新分区后，可以使用以下命令刷新分区表：

```bash
partprobe /dev/sda
```

##### 1.3、**创建物理卷**：

 将新分区（假设是 `/dev/sda3`）转换为物理卷（PV）：

```bash
pvcreate /dev/sda3
```

##### 1.4、**扩展卷组**： 

将新的物理卷添加到卷组 `centos` 中：

```bash
vgextend centos /dev/sda3
```

#### 2. **扩展逻辑卷**

现在，`centos` 卷组应该有了足够的空间，可以继续扩展逻辑卷。

##### 2.1**扩展逻辑卷**： 

假设你想给 `/dev/centos/root` 增加 10GB，可以使用：

```bash
lvextend -L +10G /dev/centos/root
```

##### 2.2**扩展文件系统**：

 逻辑卷扩展后，还需要扩展文件系统以使用新的空间。假设文件系统是 `xfs`，可以使用：

```bash
xfs_growfs /dev/centos/root
```

如果是 `ext4`，则使用：

```bash
resize2fs /dev/centos/root
```

## 扩展快满的分区（比如 `/dev/mapper/centos-root`）

### 1、确保 VMware 已经成功扩展虚拟磁盘的大小

```bash
fdisk -l
```

### 2、使用`lvextend`扩展LVM分区

**查看当前的逻辑卷**： 使用 `lvdisplay` 命令来查看当前的逻辑卷配置：

```bash
lvdisplay
```

**扩展逻辑卷**： 假设卷组名为 `centos`，你可以通过 `lvextend` 命令扩展该逻辑卷。例如，要将 `/dev/mapper/centos-root` 增加 10G：

```bash
lvextend -L +10G /dev/centos/root
```

**扩展到剩余的所有可用空间**，可以使用：

```bash
lvextend -l +100%FREE /dev/centos/root
```

###  3：扩展文件系统

扩展逻辑卷之后，你还需要扩展文件系统以便使用新的空间。如果你的文件系统是 `xfs`，使用以下命令：

```bash
xfs_growfs /dev/centos/root
```

如果是 `ext4` 文件系统，可以使用：

```bash
resize2fs /dev/centos/root
```

### 步骤 4：确认扩展结果

使用 `df -h` 命令确认分区空间已成功扩展：

```bash
df -h
```

## 
