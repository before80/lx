+++
title = "笔记"
date = 2024-04-23T19:48:52+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

## bash shell 基础命令

### 启动 shell

​	系统启动的shell程序取决于用户账户的配置。

​	可通过查看/etc/paswd获知某个用户默认的shell。

​	例如，获取 root用户的默认shell：

```cmd
grep -e ^root /etc/passwd
```



### 使用shell提示符

​	Ubuntu系统的默认shell提示符组成规则：

```
# 普通用户
用户名@主机名:路径$
# root用户
root@主机名:路径#
```

​	CentOS系统的默认shell提示符组成规则：

```
# 普通用户
[用户名@主机名 路径]$
# root用户
[root@主机名 路径]#
```

> 如何获取用户名？
>
> ​	方法1：
>
> ```cmd
> lx@lxm:~$ echo $USER
> lx
> ```
>
> ​	方法2：
>
> ```
> lx@lxm:~$ whoami
> lx
> ```
>
> ​	方法3：
>
> ```cmd
> lx@lxm:~$ logname
> lx
> ```
>
> 

> 如何获取主机名？
>
> ```
> lx@lxm:~$ hostname
> lxm
> ```
>
> 

​	可以根据需要修改提示符。

### 与bash手册交互

方式1：通过`man`命令

​	`man`命令可以查看`非内置命令`、函数、配置文件的手册页。

​	按`Enter`键，逐行读手册页；

​	按`箭头上下键`，上下逐行读手册页；

​	按`PgUp`或`PgDn`或空格键，逐页读手册页；

​	输入`/`加上`需要搜索的内容`接着按`Enter`键进行搜索，接着可按`n`键跳至下一匹配项，按`N(即 SHIFT + n)`键跳至前一匹配项，按`p`键跳至手册页的第一个匹配项。

​	按`Q`键退出手册页。

​	在命令行执行`man man`这个命令，可在`DESCRIPTION`这里看到手册页的节描述，以下是在Ubuntu 22.04上得到的结果：（其中的1到9被称为`节编号`）

```
man  is  the  system's manual pager.  Each page argument given to man is normally the name of a program, utility or function.  The manual page associated with each of these arguments is then found and displayed.  A section, if provided, will direct man to look only in that section of the manual.The default action is to search in all of the available sections following a pre-defined order (see DEFAULTS), and to show only the first page found, even if page exists in several sections.

	man是系统的手册浏览器。man命令通常接收的参数是程序、实用工具或函数的名称。然后找到并显示与每个参数关联的手册页。如果提供了一个节（section），man将仅在手册的该节中查找。默认操作是按照预定义的顺序（见DEFAULTS）搜索所有可用的节，并且只显示找到的第一页，即使页面存在于多个节中。


The table below shows the section numbers of the manual followed by the types of pages they contain.
	下表显示了手册的节编号，以及它们包含的页面类型。

       1   Executable programs or shell commands 可执行程序或shell命令
       2   System calls (functions provided by the kernel) 系统调用（内核提供的函数）
       3   Library calls (functions within program libraries) 库调用（程序库中的函数）
       4   Special files (usually found in /dev) 特殊文件（通常可在/dev目录中找到）
       5   File formats and conventions, e.g. /etc/passwd 文件格式和约定，例如 /etc/passwd
       6   Games 游戏
       7   Miscellaneous (including macro packages and conventions), e.g. man(7), groff(7), man-pages(7) 杂项（包括宏包和约定）
       8   System administration commands (usually only for root) 系统管理命令（通常只给root用户使用）
       9   Kernel routines [Non standard] 内核例程[非标准]

```

​	`节编号`，在手册页中的第一行所显示的命令名的右边括号中显示。例如：

```cmd
man man
```

其在手册页的第一行是

```cmd
MAN(1)                                                         Manual pager utils                                                         MAN(1)
```

而

```cmd
man wget
```

其在手册页的第一行是

```cmd
WGET(1)                                                             GNU Wget                                                             WGET(1)
```



> 注意
>
> ​	手册页可能包含一些非标准的节编号，例如，`1p`对应于可移植操作系统接口命令，`3n`对应于网络函数。

> 注意
>
> ​	有些命令名可能存在于多个节编号中，可以通过`man -f 命令名`或`whatis 命令名`来查看该命令所在的节编号。例如：
>
> ```cmd
> lx@lxm:~$ whatis passwd
> passwd (1)           - change user password
> passwd (1ssl)        - OpenSSL application commands
> passwd (5)           - the password file
> lx@lxm:~$ man -f passwd
> passwd (1)           - change user password
> passwd (1ssl)        - OpenSSL application commands
> passwd (5)           - the password file
> lx@lxm:~$ whatis hostname
> hostname (7)         - hostname resolution description
> hostname (1)         - show or set the system's host name
> hostname (5)         - Local hostname configuration file
> lx@lxm:~$ man -f hostname
> hostname (7)         - hostname resolution description
> hostname (1)         - show or set the system's host name
> hostname (5)         - Local hostname configuration file
> lx@lxm:~$ whatis wget
> wget (1)             - The non-interactive network downloader.
> lx@lxm:~$ man -f wget
> wget (1)             - The non-interactive network downloader.
> ```
>
> ​	若要查看指定节编号下的手册页，可通过`man 节编号 命令名`来查看。例如：
>
> ```cmd
> lx@lxm:~$ man 7 hostname
> lx@lxm:~$ man 1ssl passwd
> ```
>
> 

方式2：通过`info`命令，查看命令、函数、配置文件的手册信息

> `man`命令和`info`命令的异同
>
> ​	在 Linux 下，`man` 和 `info` 命令都用于查看命令、函数或配置文件等的手册信息，但它们在展示内容和使用方式上有一些不同之处：
>
> 1. **内容格式**：
>    - `man` 命令显示的是传统的 Unix 手册页，通常以简洁的文本形式呈现，内容较为紧凑，适合快速查阅。
>    - `info` 命令显示的是 GNU 项目的 info 文档，内容结构化更丰富，具有更多的超链接和交互式功能，以节点（node）的形式组织信息。
> 2. **导航方式**：
>    - `man` 命令通常使用键盘上的箭头键、Page Up、Page Down 等来上下翻页，查看手册页的不同部分。
>    - `info` 命令则提供更多的交互式功能，可以使用方向键、Tab 键等进行导航，还支持内部搜索、跳转到不同的节点等。
> 3. **内容结构**：
>    - `man` 手册页通常较为简洁，以线性方式呈现，主要包含命令的简要描述、用法、选项等信息。
>    - `info` 文档结构更为复杂，通常包含更多的章节、子章节和交叉引用，信息更为详尽，适合深入学习和了解。
> 4. **可扩展性**：
>    - `man` 手册页通常由系统安装的软件包提供，更新频率较低，信息相对固定。
>    - `info` 文档则更具有扩展性，GNU 项目的软件通常提供更多的信息和更新，因此可能更适合查阅最新的文档。
>
> 总的来说，`man` 命令更适合快速查找和浏览基本的命令信息，而 `info` 命令则更适合深入学习和了解复杂的软件或工具。

### 浏览文件系统

#### Linux文件系统

​	Windows系统中文件可存放于多个磁盘分区下，例如：`D:\testFile1.xls`、`E:\testFile2.doc`

​	而Linux系统则将文件存放在虚拟目录的单个目录下，例如：`/home/lx/testFile1.md`、`/tmp/temp.log`，且在Linux中只有一个根目录（即`/`）。

#### 遍历目录

### 列出文件和目录

#### 显示基本列表

#### 显示长列表

#### 过滤输出列表

### 处理文件

#### 创建文件

#### 复制文件

#### 使用命令行补全

#### 链接文件

#### 文件重命名

#### 删除文件

### 管理目录

#### 创建目录

#### 删除目录

### 查看文件内容

#### 查看文件类型

#### 查看整个文件

#### 查看部分文件



## 更多的bash shell 命令



## 理解shell



## Linux环境变量



## 理解Linux文件权限



## 管理文件系统



## 安装软件



## 构建基础脚本



## 结构化命令



## 更多的结构化命令



## 处理用户输入



## 呈现数据



## 脚本控制



## 创建函数



## 图形化桌面环境中的脚步



## 初识 sed 和 gawk



## 正则表达式



## sed 进阶



## gawk 进阶



## 使用其他 shell



## 编写简单的脚本实用工具



