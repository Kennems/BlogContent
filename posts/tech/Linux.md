---
title : ' Linux'
date : 2024-04-17T15:37:01+08:00
lastmod: 2024-04-17T15:37:01+08:00
description : "Linux" 
image : img/cat.jpg
draft : false    
categories : ["Linux"]
tags : ["Linux笔记"]
# password : leetcode
---


# Linux

快捷键：

ctrl + l 清空屏幕

# Linux文件系统

## **FHS3.0**（File system Hierarchy Standard）

![](https://cdn.jsdelivr.net/gh/kennems/blog-image/20230727205720.png)

- /
  - etc 配置文件
  - bin 必要命令
  - usr 二级目录
  - home 家目录
  - var 动态数据

## VFS虚拟文件系统

![](https://cdn.jsdelivr.net/gh/kennems/blog-image/20230727210231.png)

- 内核层抽象出通用的文件系统接口

- 支持文件、网络、特殊文件系统

抽象对象：

- 超级快：文件系统
- 目录项：文件路径
- 索引节点：具体文件
- 文件：进程打开的文件

属性分层结构

一切皆文件

## 数据盘挂载

```
fdisk -l
```

inode ：存储数据的元数据

Linux没有盘符的概念，只有一个根目录/，所有文件都在其下

/

- 根目录
- 层级关系

# 命令

通用格式：

```
command [-options] [parameter]
```

- command ：命令本身
- -options： [可选，非必填] 命令的一些选项，可以通过选项控制命令的行为细节
- parameter： [可选，非必填]命令的参数，多数用于命令的指向目标等

语法中[]表示可选

## ls

```bash
ls [-a -l -h] [Linux路径]
```

- -a all
  - 前面带.的文件使隐藏文件/文件夹，只有通过-a选项才能看到
- -l  以列表（竖向排列）
- -h 表示以易于阅读的形式，列出文件大小，如K，M，G

/home/用户名

组合使用

## cd

change directory

```shell
cd [Linux路径]
```

不写参数回到用户的HOME目录下

- 绝对路径
- 相对路径

. 表示当前目录

.. 表示上一级目录

~ 表示HOME目录

## mkdir

创建目录

```bash
mkdir [-p] 路径
```

-p可选，创建多级不存在的目录时使用

## touch

创建文件

touch 路径

## cat

查看内容

cat 路径

## more

查看内容，支持翻页，空格翻页，q退出

more 路径

## cp

可以用于复制文件\文件夹

```bash
cp [-r] 参数1 参数2
```

- -r选项，可选，用于**复制文件夹**使用，表示递归
- 参数1，Linux路径，表示被复制的文件或文件夹
- 参数2，Linux路径，表示要复制去的地方

## mv

```bash
mv 参数1 参数2
```

可以用于**改名**

## rm

删除文件，文件夹

```
rm [-r -f] 参数1 参数2 参数n
```

- -r， 删除文件夹
- -f，force，强制删除（不会弹出提示信息）
  - 普通用户删除内容不会弹出提示，只有**root管理员用户删除内容会有提示**
  - **所以一般普通用户用不到-f**
- 参数1，参数2， ...，参数n 表示要删除的文件或文件夹路径，按照空格隔开。

支持用通配符来模糊匹配

## 回收站式删除Trash-Cli

rm太危险了， 为了避免出错， 在`~/.bashrc`中添加

```bash
alias rm='echo "This is not the command you are looking for."; false'
```

然后 `source ~/.bashrc`

### 使用 Trash-Cli

- `trash-put`： 删除文件和目录（仅放入回收站中）
- `trash-list` ：列出被删除了的文件和目录
- `trash-restore`：从回收站中恢复文件或目录 trash.
- `trash-rm`：删除回收站中的文件
- `trash-empty`：清空回收站

## pwd

Print Work Directory

## tree

树状目录

## which

Linux命令本体就是一个个的二进制可执行文件

```bash
which 命令
```

## find

```bash
find 起始路径 -name "被查找文件名" 
```

```bash
find 起始路径 -size + | -n[kMG]
```

- +, - 表示大于和小于
- n表示大小数字
- kMG表示大小单位，k表示kb,M表示MB，G表示GB

## grep

通过grep命令，从文件中通过关键字过滤文件行

```bash
grep [-n] 关键字 文件路径
```

- 选项 -n 可选，表示在结果中显示匹配的行的行号
- 参数，**关键字**，必填，表示过滤的关键字，带有空格或其它特殊符号，建议使用“ ”将关键字包围起来
- 参数，**文件路径**，必填，表示要过滤内容的文件路径，可作为**内容输入端口**

### 搜索当年目录下所有文件是否包含某一个内容

使用 `grep` 命令来搜索当前路径下所有文件是否包含某个单词：

```bash
grep -r "your_word" .
```

解释：
- `grep`：用于搜索文件内容。
- `-r`：递归搜索，意味着它会查找当前目录及其子目录下的所有文件。
- `"your_word"`：你要搜索的单词或字符串，替换为你想查找的内容。
- `.`：表示当前目录。

如果你只想查找文件名而不显示匹配的内容，可以使用 `-l` 参数：

```bash
grep -rl "your_word" .
```

只列出包含该单词的文件路径，不显示具体匹配的内容。

如果要进行大小写不敏感的搜索，可以加上 `-i` 参数：

```bash
grep -rli "your_word" .
```

### 显示行号

通过 `-n` 选项来显示匹配行的行号

```
grep -n "pattern" filename
```

## wc

```bash
wc [-c -m -l -w] 文件路径
```

- 选项， -c， 统计bytes数量
- 选项，-m，统计字符数量
- 选项，-l，统计行数
- 选项，-w，统计单词数量
- 参数，文件路径， 被统计的文件，可作为内容输入端口

默认行数、字数、字符数

## 管道符

左 | 右。 将左边的结果作为右边的输入

## echo

命令行内输出指定内容

## 反引号`

在echo中，用``括起来表示命令信息

```bash
echo `pwd`
```

## 重定向符号

```bash
> 将左侧命令的结果，覆盖写入到符号右侧指定的文件中
```

```bash
>> 将左侧命令的结果，追加写入到符号右侧指定的文件中
```

## tail

```bash
tail [-f -num] 参数
```

- 参数，linux命令，表示被跟踪的文件路径
- 选项，-f，表示持续跟踪
- 选项，-num，表示尾部多少行，不填默认10行

## awk

编写规则，根据输入的文本数据来执行特定的操作

1. 打印整行：
   ```bash
   awk '{print}' file.txt
   ```
   这个命令将打印文件file.txt中的每一行。

2. 根据字段进行匹配和操作：
   ```bash
   awk '$1 == "pattern" {print $2}' file.txt
   ```
   这个命令将打印文件file.txt中第一个字段为"pattern"的行的第二个字段。

3. 使用正则表达式进行匹配：
   ```bash
   awk '/pattern/ {print}' file.txt
   ```
   这个命令将打印文件file.txt中包含"pattern"的每一行。

4. 计算字段的总和或平均值：
   ```bash
   awk '{sum+=$1} END {print "Sum:", sum}' file.txt
   ```
   这个命令将计算文件file.txt中第一个字段的总和，并在文件处理结束时打印出来。

5. 自定义字段分隔符：
   ```bash
   awk -F',' '{print $1}' file.csv
   ```
   这个命令将使用逗号作为字段分隔符，并打印文件file.csv中的每一行的第一个字段。

6. 使用内置函数：
   ```bash
   awk '{print toupper($1)}' file.txt
   ```
   这个命令将将文件file.txt中的第一个字段转换为大写并打印出来。

7. 过滤并处理文件内容：
   ```bash
   awk '$3 > 10 {print $1, $2 * $3}' file.txt
   ```
   这个命令将打印文件file.txt中第三个字段大于10的行的第一个和第二个字段的乘积。

8. 自定义输出格式：
   ```bash
   awk '{printf "%-10s %-5s\n", $1, $2}' file.txt
   ```
   这个命令将按照指定的格式打印文件file.txt中的每一行的前两个字段。

## Vim

### 命令模式

i : 在当前光标位置进入输入模式

a : 在当前光标位置 之后 进入输入模式

I ： 在当前行的开头，进入输入模式

A ： 在当前行的结尾，进入输入模式

o : 在当前行的下一行进入输入模式

O ： 在当前行的上一行进入输入模式

0 : 移动光标至开头

$ : 移动给光标至行结尾

pageup : 向上翻页

pagedown : 向下翻页

/ ： 进入搜索模式

n :  向下继续搜索

N : 向上继续搜索

dd ： 删除光标所在的行

ndd : n是数字，表示删除当前光标向下n行

yy : 复制当前行

nyy ： 复制当前行和下面的n行

p : 粘贴复制的内容

u : 撤销修改

ctrl + r ： 反向撤销修改

gg ： 跳到首行

G ： 跳到尾行

dG : 从当前行开始，向下全部删除

dgg ： 从当前行开始，向上全部删除

dS : 从当前光标开始，删除到本行的结尾

d0 ： 从当前光标开始，删除到本行的开头

### 底线命令模式

: wq 保存并退出

: q 仅退出

: q! 强制退出

:w 仅保存

**:set nu 显示行号**

:set paste 设置粘贴模式

## tmux

`tmux` 进入 `exit`(ctrl + d) 退出

### 创建新会话

```
tmux new -s <session-name>
```

### 分离会话

`ctrl + b` then `d`

```
tmux detach
```

```
tmux ls
```

### 接入已有对话

`ctrl + b` then `s`

```
tmux attach -t 0
```

```
tmux attch -t <session-name>
```

### 杀死对话

`ctrl + b` then `$`

```
tmux kill-session -t 0
```

```
tmux kill-session -t <session-name>
```

### 切换会话

```
tmux switch -t 0
```

### 划分窗格

`ctrl + b`  then `%` : 左右划分

`ctrl + b` then `“` : 上下划分

`ctrl + b` then `<arrow key>` : 光标

```
tmux split-window
```

```
tmux split-window -h
```

### 移动光标

`ctrl + b` then `{ ` 切换到上一个

`ctrl + b` then `} ` 切换到下一个

`ctrl + b`  then `ctrl + o ` 所有窗格向前移动一个位置，第一个窗格变成最后一个窗格

`ctrl + b` then `alt + o` 所有窗格向后移动一个位置，最后一个窗格变成第一个窗格

`ctrl + b` then `x` 关闭当前窗格

`ctrl + b` then `!` 将当前窗格拆分为一个独立窗口

`ctrl + b`  then `z` 当前窗格全屏显示，再使用一次变回原来大小

`ctrl + b`  then `q` 显示窗格编号

```
tmux select -pane -U
```

```
tmux select -pane -D
```

```
tmux select -pane -L
```

```
tmux select -pane -R
```

### 交换窗格位置

```
tmux swap-pane -U
```

```
tmux swap-pane -D
```

### 创建多个窗口

`ctrl + b` then `c ` 创建一个新窗口

`ctrl + b` then `p` 切换到上一个窗口

`ctrl + b` then `n`  切换到下一个窗口

`ctrl + b` then `<number> ` 切换到指定编号的窗口

`ctrl + b` then `w` 从列表中选择窗口

`ctrl + b` then `,` 窗口重命名

## Ctrl + R使用

**Ctrl + R** : 反向搜索（reverse-i-search) 连续按键继续搜索

**Ctrl + S** : 正向搜索 （i-search) 连续按键继续搜索

Enter输入该命令

**Ctrl + G** 或 Ctrl + C退出反向搜索模式

**Alt + .** 插入最后一个参数，插入前一个命令的最后一个参数

**Ctrl + J** 选择当前命令

**Ctrl + O** 直接执行当前命令，并选择当前命令的下一条命令

## 禁用Ctrl + S

禁用 XON/XOFF 流控制

1. 打开终端配置文件

   ```
   ~/.bashrc
   ```

    或 

   ```
   ~/.bash_profile
   ```

   然后添加以下行：

   ```bash
   stty -ixon
   ```

2. 保存文件并重新加载配置：

   ```bash
   source ~/.bashrc
   ```

命令行终端左右移动

Ctrl + A 移动光标到行首

Ctrl + E 移动光标到行尾

Ctrl + B 向左移动光标

Ctrl + F 向右移动光标

Alt + B 向左移动一个单词

Alt + F 向右移动一个单词

## WSL 与 windows剪切板通信

visual模式选中文本，之后 `:'<,'>w !clip.exe` 复制选择的文本

`:w !clip.exe` 复制整个文本

# Linux权限和用户

## su

```bash
su [-] [用户名]
```

## sudo

普通用户使用sudo使用root权限

用户与用户组

## 用户和用户组

```bash
groupadd 创建用户组

groupdel 用户组名
```

```shell
useradd [-g -d] 用户名
```

- 选项： -g指定用户的组，不指定-g，会创建同名组加入，指定-g需要组已经存在，如已存在同名组，必须使用-g。
- 选项： -d指定用户HOME路径，不指定，HOME目录默认在： /home/用户名

```bash
userdel [-r] 用户名
```

- 选项：-r，删除用户的HOME目录，不使用-r，删除目录时，HOME目录保留

```bash
id [用户名]
```

- 参数：用户名，被查看的用户，如果不提供则查看自身。

```
usermod -aG 用户组 用户名 
将指定用户加入指定用户组
```

#### 查看系统中所有用户

系统用户信息保存在 `/etc/passwd` 文件中。

```
cat /etc/passwd
```

示例输出：

```
root:x:0:0:root:/root:/bin/bash
kennem:x:1000:1000:Kennem,,,:/home/kennem:/bin/bash
```

解释：

- 每行一个用户，字段用 `:` 分隔：
  1. 用户名
  2. 密码占位符（x 表示影子密码存储在 `/etc/shadow`）
  3. 用户 ID (UID)
  4. 组 ID (GID)
  5. 用户全名或描述
  6. 家目录
  7. 默认 shell

##### 列出用户名

```
cut -d: -f1 /etc/passwd
```

只显示用户名列表。

------

#### 查看系统中所有用户组

系统用户组信息保存在 `/etc/group` 文件。

##### cat 查看

```bash
cat /etc/group
```

示例输出：

```
root:x:0:
docker:x:999:kennem
sudo:x:27:kennem
```

解释：

- 每行一个用户组，字段用 `:` 分隔：
  1. 组名
  2. 密码占位符（通常是 x）
  3. GID（组 ID）
  4. 组内用户列表（逗号分隔）

##### 列出组名

```
cut -d: -f1 /etc/group
```

只显示组名列表。

------

#### 查看当前用户所属组

```
groups
```

或

```
groups $USER
```

示例：

```
kennem : kennem sudo docker
```

表示当前用户 `kennem` 属于 `kennem`、`sudo` 和 `docker` 组。

```bash
getent passwd   # 列出所有用户（同 /etc/passwd）
getent group    # 列出所有组（同 /etc/group）
```

使用`getent`命令，可以查看当前系统内有哪些用户和组

- **查看某个用户详细信息**：

```
id kennem
```

示例输出：

```
uid=1000(kennem) gid=1000(kennem) groups=1000(kennem),27(sudo),999(docker)
```

七份信息：

用户名 ： 密码（X） ： 用户ID ： 组ID ： 描述信息（无用） ： HOME目录 ： 执行终端（默认bash）

## 查看权限管控信息

- 文件或文件夹的控制信息

- 文件或文件夹所属用户
- 文件或文件夹所属用户组

![](https://cdn.jsdelivr.net/gh/kennems/blog-image/20230802155353.png)

- 第一个d表示文件夹
- 所属用户
- 所有用户组
- 所属其他用户
  - **r代表读**
  - 文件夹表示可以查看文件夹内容
  - **w代表写**
  - 文件夹表示可以在文件夹内：创建，删除，改名等操作
  - **x代表可执行权限**，针对文件表示可以将文件作为程序执行
  - 针对文件夹，表示可以更改工作目录到此文件夹，即cd进入

## chmod

修改文件或目录的权限。

```bash
chmod u = rwx, g = rx, o = x hello.txt
```

**-R选项**可以将文件夹以及文件夹内**全部内容**权限设置为：rwxrwxrwx

```bash
chmod -R u=rwx, g=rwx, o=rwx hello.tx
```

使用数字序号

```bash
chmod 751 hello.txt
```

## chown

更改文件或目录的所有者为指定的用户或用户组。

```bash
chown [-R] [用户] [:] [用户组] 文件或文件夹
```

- **选项，-R，同chmod，对文件夹内全部内容应用相同规则**
- 选项，用户，修改所属用户
- 选项，用户组，修改所属用户组
- **：** 用于分隔用户和用户组

**普通用户无法使用，只能用root用户**

# Linux使用操作

## 快捷键：

```bash
ctrl + c 强制停止,退出当前命令输入
ctrl + d 退出账户的登录
history 查看历史输入的命令
!搜索历史命令，自动匹配，例如！py
ctrl + a，跳到命令开头
ctrl + b, backward 
ctrl + f, forward
esc + f, forward a word 
esc + b, backward a word
ctrl + e，跳到命令结尾
ctrl + <-， 向左跳一个单词
ctrl + ->， 向右跳一个单词
ctrl + l 清空终端内容
clear 清空终端内容
```

## 软件安装

yum ：  RPM软件管理器，用于自动化安装配置Linux软件，并可以自动解决依赖问题。

```bash
yum [-y] [install | remove | search] 软件名称 
```

- -y 自动确认，无需手动确认安装或卸载过程

yum命令需要root权限，可以su切换到root，或使用sudo权限，yum命令需要联网

### Ubuntu 

```bash
apt [-y] [install | remove | search] wget
```

## systemctl

```bash
systemctl start | stop | status | enable | disable 服务名
```

- NetworkManager, 主网络服务
- newwork, 副网络服务
- firewalld , 防火墙服务
- sshd, ssh服务（FinalShell远程登录Linux使用的就是此服务）

除了内置的服务以外，部分第三方软件安装后也可以用systemctl进行控制

## 软链接

在系统中创建软链接，可以将文件、文件夹链接到其他位置。类似快捷方式

```bash
ln -s 参数1 参数2
```

- -s ，**创建软链接**
- 参数1 ： 被链接的文件或文件夹
- 参数2 ： 要链接去的目的地

## 日期，时区

```bash
date [-d] [+格式化字符串]
```

- -d按照给定的字符串显示日期，一般用于日期计算
- 格式化字符串： 通过特定的字符串标记，来控制显示的日期格式
  - %Y ， 年
  - %y,， 年份后两位数字（00，99）
  - %M 月份 （01，12）
  - %d 日(01,31)
  - %H 小时（00，23）
  - %M 分钟（00，59）
  - %S 秒（00，59）
  - %s 自1970-01-01 00:00:00到现在的秒数

- 使用-d支持的时间标记：(同样支持格式化字符串)
  - year 年
  - month 月
  - day 天
  - hour 小时
  - minute 分钟
  - second 秒

## ntp

可以自动联网同步时间，也可以通过ntp -u ntp.aliyun.com 手动校准时间

## IP地址

DHCP : 动态获取IP地址，即每次重启设备后都会获取一次，可能导致IP地址频繁变更

每一台联网的电脑都会有一个地址，用于和其他计算机进行通信，IP地址主要有两个版本，V4和V6版本

IPv4的地址格式为a.b.c.d，其中abcd表示0~255的数字，如192.168.88.101

通过`ipconfig`查看本机的IP地址。

127.0.0.1表示本机

0.0.0.0

- 可以用于指代本机
- 可以在端口绑定中用来确定绑定关系
- 在一些IP地址中，表示所有IP的意思，如放行规则设置为0.0.0.0，表示允许任意IP访问

### 修改IP地址

### 1. **确定网络接口名称**

```bash
ip a
```

### 2. **修改网络配置文件**

首先，进入 `netplan` 配置目录：

```bash
cd /etc/netplan/
```

一个 `.yaml` 文件（例如 `01-netcfg.yaml` 或类似的文件），使用文本编辑器打开该文件：

```bash
sudo nano 01-netcfg.yaml
```

### 3. **编辑配置文件**

在配置文件中，找到对应的接口部分，然后修改 `dhcp4: true` 为 `dhcp4: false`，并手动配置静态 IP 地址。比如：

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp3s0:
      dhcp4: false
      addresses:
        - 192.168.1.100/24  # 修改为你需要的 IP 地址
      gateway4: 192.168.1.1  # 修改为你网关的 IP 地址
      nameservers:
        addresses:
          - 8.8.8.8  # 修改为你需要的 DNS 地址
          - 8.8.4.4
```

### 4. **应用修改**

保存文件后，应用网络配置更改：

```bash
sudo netplan apply
```

### 5. **验证修改**

检查新的 IP 地址是否已经生效：

```bash
ip a
```

### 6. **重启网络服务（可选）**

有时，可能需要重启网络服务来确保修改生效：

```bash
sudo systemctl restart systemd-networkd
```

这样，你的 IP 地址就被永久设置为静态 IP，系统重启后依然有效。如果希望使用 `dhclient` 或其他方法自动获取 IP 地址，改回 `dhcp4: true` 即可。

## 主机名

```bash
hostname
```

修改主机名

```bash
hostnamectl set-hostname name
```

## 域名解析

## ping

可以通过ping命令来检查指定的网络服务器是否是可联通的。

```bash
ping [-c num] ip或主机名
```

- 选项， -c，检查的次数，不适用-c选项，将无限次数持续检查
- 参数：ip或主机名，被检查的服务器的ip地址或主机名地址

## wget

非交互式的文件下载器，可以在命令行内下载网络文件

```bash
wget [-b] url
```

- 选项 ： -b ，后台下载，会将日志写入到当前工作目录的wget-log文件
- 参数：url，下载链接

## curl

发送http网络请求，可用于下载文件，获取信息等

```bash
curl [-O] url
```

- 选项：-O，用于下载文件，当url是下载链接时，可以使用此选项保存文件
- 参数：url，要发起请求的网络地址

## 端口 

端口，是设备与外界通讯交流的出入口，端口可以分为物理端口和虚拟端口

- 物理端口：又可称之为接口，是可见的端口，如USB接口，RJ45网口，HDMI端口等
- 虚拟端口：是指计算机内部的端口，是不可见的，是用来操作系统和外部进行交互使用的

Linux支持65535个端口，分为3类进行使用：

- 公认端口：1~1023，通常用于一些系统内置或知名程序的预留使用，如SSH服务的22端口，HTTPS服务的443端口，非特殊需要，不要占用这个范围的端口
- 注册端口：1024~49151，通常可以随意使用，用于松散的绑定一些程序/服务
- 动态端口：49152~65535，通常不会固定绑定程序，而是当程序对外进行网络链接时，用于临时使用。

## 进程管理

```bash
ps [-e -f]
```

- -e 显示出全部的进程
- -f 以完全格式化的形式展示信息（展示全部信息）
- 一般来说，固定用法就是 ps -ef 列出全部进程的全部信息

![](https://cdn.jsdelivr.net/gh/kennems/blog-image/20231201202359.png)

- UID ： 进程所属的用户ID

- PID ： 进程的进程号ID
- PPID ： 进程的父ID（启动此进程的其他进程）
- C ： 此进程的CPU占用率（百分比）
- STIME ： 进程的启动时间
- TTY ： 启动此进程的终端序号，如果显示？，表示非终端启动
- TIME ： 进程启用CPU的时间
- CMD ： 进程对应的名称或启动路径和启动命令

## 关闭进程

```bash
kill -9 进程ID
```

- -9, 表示**强制关闭进程**，不适用此选项会向进程发送信号要求其关闭，但是否关闭看进程自身的处理机制

## 主机状态

查看CPU，内存使用情况

```shell
top
```

![](https://cdn.jsdelivr.net/gh/kennems/blog-image/20231202140852.png)

**第一行 ：** 

top ： 命令名称

14：08 ：23  当前系统时间，up 6min：启动了六分钟

2 users : 2个用户登录， load ：15分钟负载

**第二行：**

Tasks : 175个进程 1 running : 1个子进程在运行

174 sleeping : 174个进程睡眠，0个停止进程， 0个僵尸进程

**第三行：**

%**Cpu(s)** : CPU使用率，**us**：用户CPU使用率，**sy** ：系统CPU使用率，**ni**：高优先级进程占用CPU时间百分比，**id**：空闲CPU率，**wa**：IO等待CPU占用率，**hi**：CPU硬件中断率，**si**：CPU软件中断率，**st**：强制等待占用CPU率

**第四、五行**

Kib Mem : 物理内存，total : 总量， free : 空闲， used ： 使用， buff/cache : buff和cache占用

KibSwap : 虚拟内存（交换空间），total : 总量，free : 空闲，used：使用，buff/cache : buff和cache占用

- PID ： 进程ID
- USER ： 进程所属用户
- PR ： 进程优先级，越小越好
- NI ： 负值表示高优先级，正表示低优先级
- VIRT ： 进程使用虚拟内存，单位KB
- RES ： 进程使用物理内存，单位KB
- SHR ： 进程使用共享内存， 单位KB
- S ： 进程状态(S休眠，R运行，Z僵死状态，N负数优先级，I空闲状态)
- %CPU ： 进程占用CPU率
- %MEM： 进程占用内存率
- TIME+ ： 进程使用CPU时间总计，单位10毫秒
- COMMAND ： 进程的命令或名称或程序文件路径

```shell
-p : 只显示某个进程信息
-d : 设置刷新时间，默认为5s
-c : 显示产生进程的完整命令，默认是进程名
-n : 制定刷新次数，比如 top -n 3 是新输出三次后退出
-b : 以非交互非全屏模式运行，以批次的方式执行top，一般配合-n制定输出几次统计信息，将输出重定向到制定文件，比如 top -b -n 3 > /tmp/top.tmp
-i : 不显示任何闲置（idle）或无用的进程
-u ： 查找特定用户启动的进程
```

**top以交互式运行：**

```shell
h ： 按下h键，会显示帮助画面
c ： 按下c键，会显示产生进程的完整命令，等同于-c参数
f ： 可以选择需要展示的项目
M ： 根据驻留内存大小（RES）排序
T ： 根据CPU使用百分比大小进行排序
T ： 根据时间/累计时间进行排序
E ： 切换顶部内存显示单位
e ： 切换进程内存显示单位
l ： 切换显示平均负载和启动时间信息
i ： 不显示闲置或无用的进程，等同于-i参数
t ： 切换显示CPU状态信息
m ： 切换显示内存信息
```

**硬盘使用情况：** 

```shell
df -h
```

- -h， 以更加人性化的单位显示

**磁盘信息监控：**

```shell
iostat [-x] [num1] [num2]
```

**网络状态监控：**

- 可以使用sar命令查看网络的相关统计

```shell
sar -n DEV num1 num2
```

- -n 查看网络，DEV表示查看网络接口
- num1 : 刷新间隔（不填就查看一次阶数）num2 : 查看次数（不填无限次数）

## 环境变量

环境变量是一组信息记录，类型是Key Value类型（名称=值），用于操作系统运行的时候记录关键信息

```shell
env 
```

查看环境变量

环境变量： PATH，通过$取出环境变量的值

环境变量PATH会记录一组目录，目录之间用：隔开。记录的是命令的搜索路径。当执行命令会从记录中记录的目录中挨个搜索要执行的命令并执行

可以通过这个项目的值，加入自定义的命令搜索路径

如 

```shell
export PATH=$PATH:目录
```

修改环境变量

- 临时生效 ： export 名称 = 值
- 永久生效
  - 针对用户 ： ~/.bashrc文件中配置
  - 针对全部用户

## 文件上传和下载

通过finalShell或者xshell 上传或下载，拖动

## 压缩和解压

- .tar 称之为tarball，简单的将文件组装到一个.tar的文件中，并没有太多文件体积的减少，仅仅是简单的封装
- .gz，也常见为.tar.gz，gzip格式压缩文件，即使用gzip压缩算法将文件压缩到一个文件内，可以极大的减少压缩后的体积

```shell
tar [-c -v -x -f -z -C] 参数1 参数2 ... 参数N
```

- -c 创建压缩文件，用于压缩模式
- -v 显示压缩，解压过程，用于查看进度
- -x 解压模式
- -f 要创建的文件，或者要解压的文件，-f选项必须在所有选项中位置处于最后一个
- -z gzip模式，不适用-z就是普通的tarball模式
- -C 选择解压的目的地，用于解压模式

### 1. 压缩并打包文件或目录

使用 `-czf` 参数将文件或目录打包并压缩成 `.tar.gz` 格式：

```bash
tar -czf 压缩包名.tar.gz 要压缩的文件或目录
```

例如，将目录 `my_folder` 压缩成 `archive.tar.gz`：

```bash
tar -czf archive.tar.gz my_folder
```

### 2. 解压 `.tar.gz` 文件

使用 `-xzf` 参数解压 `.tar.gz` 文件：

```bash
tar -xzf 压缩包名.tar.gz
```

例如，解压 `archive.tar.gz` 到当前目录：

```bash
tar -xzf archive.tar.gz
```

### 3. 列出 `.tar.gz` 文件内容

使用 `-tzf` 参数查看 `.tar.gz` 文件中的内容，而不解压：

```bash
tar -tzf 压缩包名.tar.gz
```

### 4. 打包但不压缩

如果不需要压缩，只想打包成 `.tar` 文件，可以使用 `-cf` 参数：

```bash
tar -cf 包名.tar 要打包的文件或目录
```

例如，将 `my_folder` 打包成 `archive.tar`：

```bash
tar -cf archive.tar my_folder
```

### 5. 解包 `.tar` 文件

使用 `-xf` 参数解包 `.tar` 文件：

```bash
tar -xf 包名.tar
```





```shell
zip [-r] 参数
```

- -r 压缩文件夹使用

```shell
unzip
```

- > unzip [-d] 参数
  > - -d 制定解压的目录

### 7z

#### 1. 压缩文件或文件夹

使用 `7z` 命令创建 `.7z` 格式的压缩文件：

```
7z a 压缩文件名.7z 要压缩的文件或文件夹
```

将 `my_folder` 压缩成 `archive.7z`：

```bash
7z a archive.7z my_folder/
```

#### 2. 解压缩 `.7z` 文件

要解压缩 `.7z` 文件，可以使用：

```bash
7z x 压缩文件名.7z
```

例如，解压缩 `archive.7z` 到当前目录：

```bash
7z x archive.7z
```

#### 3. 列出 `.7z` 文件内容

如果你想查看压缩文件中的内容，而不解压缩它，可以使用 `l` 参数：

```bash
7z l 压缩文件名.7z
```

#### 4. 解压缩到指定目录

你可以通过 `-o` 选项将文件解压缩到指定目录：

```bash
7z x 压缩文件名.7z -o输出目录
```

例如，将 `archive.7z` 解压缩到 `my_folder` 目录下：

```bash
7z x archive.7z -o./my_folder
```

#### 6. 其他常用选项

- `-t7z`：指定压缩类型为 `.7z`（默认）。
- `-mx=9`：最大压缩级别，9 表示最强压缩。

例如：

```bash
7z a -t7z -mx=9 压缩文件名.7z 文件或文件夹
```

## Mysql

```shell
wget --no-check-certificate https: /dlcdn.apache.org/tomcat/tomcat10/v10.0.27/bin/apache-tomcat-10.0.27.tar.gz
```

## Redis

## ElasticSearch

## Tomcat

## Nginx

## RabbitMq
