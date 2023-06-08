+++
title = "四处浏览"
date = 2023-06-08T10:54:56+08:00
weight = 3
description = ""
isCJKLanguage = true
draft = false

+++

# 四处浏览 Looking Around

https://linuxcommand.org/lc3_lts0030.php

Now that we know how to move from working directory to working directory, we're going to take a tour of our Linux system and, along the way, learn some things about what makes it tick. But before we begin, we have to learn about some tools that will come in handy during our journey. These are:

​	现在我们知道如何从一个工作目录移动到另一个工作目录了，我们将在 Linux 系统中进行一次浏览，并在此过程中了解一些关于 Linux 运行的原理的知识。但在开始之前，我们必须学习一些在我们的旅程中会派上用场的工具。它们是：

 

- `ls` (list files and directories)
- `less` (view text files)
- `file` (classify a file's contents)
- `ls`（列出文件和目录）
- `less`（查看文本文件）
- `file`（对文件进行分类）

## ls

The `ls` command is used to list the contents of a directory. It is probably the most commonly used Linux command. It can be used in a number of different ways. Here are some examples:

​	`ls` 命令用于列出目录的内容。它可能是最常用的 Linux 命令之一。它可以以多种不同的方式使用。以下是一些示例：



| **命令 Command**  | **结果 Result**                                              |
| :---------------- | :----------------------------------------------------------- |
| `ls`              | 列出当前工作目录中的文件<br />List the files in the working directory |
| `ls /bin`         | 列出 `/bin` 目录中的文件（或其他任何我们关心的目录）<br />List the files in the `/bin` directory (or any other directory we care to specify) |
| `ls -l`           | 以长格式列出当前工作目录中的文件<br />List the files in the working directory in long format |
| `ls -l /etc /bin` | 以长格式列出 `/bin` 目录和 `/etc` 目录中的文件<br />List the files in the `/bin` directory and the `/etc` directory in long format |
| `ls -la ..`       | 以长格式列出父目录中的所有文件（甚至是以句点字符开头的文件，通常是隐藏的）<br />List all files (even ones with names beginning with a period character, which are normally hidden) in the parent of the working directory in long format |

These examples also point out an important concept about commands. Most commands operate like this:

​	这些示例还指出了关于命令的一个重要概念。大多数命令的操作方式类似于：

```
command -options arguments
```

where *command* is the name of the command, *-options* is one or more adjustments to the command's behavior, and *arguments* is one or more "things" upon which the command operates.

​	其中 *command* 是命令的名称，*-options* 是对命令行为进行的一个或多个调整，*arguments* 是命令操作的一个或多个"东西"。

In the case of `ls`, we see that `ls` is the name of the command, and that it can have one or more options, such as `-a` and `-l`, and it can operate on one or more files or directories.

​	在 `ls` 的情况下，我们可以看到 `ls` 是命令的名称，它可以有一个或多个选项，比如 `-a` 和 `-l`，并且可以操作一个或多个文件或目录。

### 更详细的长格式 A Closer Look at Long Format

If we use the `-l` option with `ls`, you will get a file listing that contains a wealth of information about the files being listed. Here's an example:

​	如果我们在 `ls` 命令中使用 `-l` 选项，将会得到一个包含有关所列出文件的丰富信息的文件列表。这里有一个示例：

------

```
-rw-------   1 me       me            576 Apr 17  2019 weather.txt
drwxr-xr-x   6 me       me           1024 Oct  9  2019 web_page
-rw-rw-r--   1 me       me         276480 Feb 11 20:41 web_site.tar
-rw-------   1 me       me           5743 Dec 16  2018 xmas_file.txt

----------     -------  -------  -------- ------------ -------------
    |             |        |         |         |             |
    |             |        |         |         |         File Name
    |             |        |         |         |
    |             |        |         |         +---  Modification Time
    |             |        |         |
    |             |        |         +-------------   Size (in bytes)
    |             |        |
    |             |        +-----------------------        Group
    |             |
    |             +--------------------------------        Owner
    |
    +----------------------------------------------   File Permissions
```

------

- **文件名 File Name**

  The name of the file or directory.

  文件或目录的名称。

- **修改时间 Modification Time**

  The last time the file was modified. If the last modification occurred more than six months in the past, the date and year are displayed. Otherwise, the time of day is shown.

  文件最后一次修改的时间。如果最后一次修改是在六个月以上之前，将显示日期和年份。否则，显示具体时间。

- **大小 Size**

  The size of the file in bytes.

  文件的大小（以字节为单位）。

- **组 Group**

  The name of the group that has file permissions in addition to the file's owner.

  除了文件所有者外，具有文件权限的组的名称。

- **所有者 Owner**

  The name of the user who owns the file.

  文件的所有者的用户名。

- **文件权限 File Permissions**

  A representation of the file's access permissions. The first character is the type of file. A "-" indicates a regular (ordinary) file. A "d" indicates a directory. The second set of three characters represent the read, write, and execution rights of the file's owner. The next three represent the rights of the file's group, and the final three represent the rights granted to everybody else. We'll discuss this in more detail in a later lesson.
  
  文件访问权限的表示形式。第一个字符表示文件的类型。"-" 表示普通文件，"d" 表示目录。接下来的三个字符表示文件所有者的读取、写入和执行权限。接下来的三个字符表示文件所属组的权限，最后的三个字符表示授予其他人的权限。我们将在以后的课程中详细讨论这个。

## less

`less` is a program that lets us view text files. This is very handy since many of the files used to control and configure Linux are human readable.

​	`less` 是一个用于查看文本文件的程序。这非常方便，因为用于控制和配置 Linux 的许多文件都是可读的。

## 什么是"文本"？ What is "text"?

There are many ways to represent information on a computer. All methods involve defining a relationship between the information and some numbers that will be used to represent it. Computers, after all, only understand numbers and all data is converted to numeric representation.

​	在计算机上表示信息有许多方式。所有的方法都涉及将信息与一些数字之间的关系定义起来，这些数字将用于表示它。毕竟，计算机只能理解数字，所有的数据都被转换为数字表示。

Some of these representation systems are very complex (such as compressed multimedia files), while others are rather simple. One of the earliest and simplest is called *ASCII text*. [ASCII](https://linuxcommand.org/lc3_man_pages/ascii7.html) (pronounced "As-Key") is short for American Standard Code for Information Interchange. This is a simple encoding scheme that was first used on Teletype machines to map keyboard characters to numbers.

​	其中一些表示系统非常复杂（例如压缩的多媒体文件），而其他一些则相当简单。其中一种最早和最简单的表示系统被称为 *ASCII 文本*。[ASCII](https://linuxcommand.org/lc3_man_pages/ascii7.html)（发音为"As-Key"）是美国信息交换标准代码的缩写。这是一种简单的编码方案，最初在电传打字机上用于将键盘字符映射到数字。

Text is a simple one-to-one mapping of characters to numbers. It is very compact. Fifty characters of text translates to fifty bytes of data. Throughout a Linux system, many files are stored in text format and there are many Linux tools that work with text files. Even Windows systems recognize the importance of this format. The well-known NOTEPAD.EXE program is an editor for plain ASCII text files.

​	文本是字符与数字之间的简单一对一映射。它非常紧凑。五十个字符的文本转换为五十个字节的数据。在 Linux 系统中，许多文件都以文本格式存储，并且有许多 Linux 工具可以处理文本文件。即使在 Windows 系统中，也认识到这种格式的重要性。著名的 NOTEPAD.EXE 程序就是用于编辑纯 ASCII 文本文件的编辑器。

The `less` program is invoked by simply typing:

​	通过简单地键入以下命令即可调用 `less` 程序：

```
less text_file
```

This will display the file.

​	这将显示文件内容。

### 控制 less - Controlling less

Once started, `less` will display the text file one page at a time. We can use the Page Up and Page Down keys to move through the text file. To exit `less`, we type "q". Here are some commands that `less` will accept:

​	启动后，`less` 将逐页显示文本文件。我们可以使用 Page Up 和 Page Down 键在文本文件中进行导航。要退出 `less`，我们输入 "q"。以下

| **命令 Command**   | **操作 Action**                                              |
| :----------------- | :----------------------------------------------------------- |
| Page Up or b       | 向上滚动一页<br />Scroll back one page                       |
| Page Down or space | 向下滚动一页<br />Scroll forward one page                    |
| G                  | 转到文本文件的末尾<br />Go to the end of the text file       |
| 1G                 | 转到文本文件的开头<br />Go to the beginning of the text file |
| /*characters*      | 在文本文件中向前搜索指定的 *characters*<br />Search forward in the text file for an occurrence of the specified *characters* |
| n                  | 重复上一次搜索<br />Repeat the previous search               |
| h                  | 显示完整的 `less` 命令和选项列表<br />Display a complete list less commands and options |
| q                  | 退出 `less` <br />Quit                                       |

## file

As we wander around our Linux system, it is helpful to determine what kind of data a file contains before we try to view it. This is where the `file` command comes in. `file` will examine a file and tell us what kind of file it is.

​	在我们浏览 Linux 系统时，在尝试查看文件之前，确定文件包含的数据类型很有帮助。这就是 `file` 命令的用处。`file` 命令将检查一个文件并告诉我们它是什么类型的文件。

To use the `file` program, we just type:

​	要使用 `file` 程序，只需键入：

```
file name_of_file
```

The `file` program can recognize most types of files, such as:

​	`file` 程序可以识别大多数类型的文件，例如：

| **文件类型 File Type**         | **描述 Description**                                         | **可作为文本查看？Viewable as text?** |
| :----------------------------- | :----------------------------------------------------------- | :------------------------------------ |
| ASCII text                     | 名称已经说得很清楚了<br />The name says it all               | yes                                   |
| Bourne-Again shell script text | 一个 `bash` 脚本<br />A `bash` script                        | yes                                   |
| ELF 64-bit LSB executable      | 可执行二进制程序<br />An executable binary program           | no                                    |
| ELF 64-bit LSB shared object   | 共享库<br />A shared library                                 | no                                    |
| GNU tar archive                | 录音带存档文件。一种常见的存储文件组的方式<br />A tape archive file. A common way of storing groups of files. | no, use `tar tvf` to view listing.    |
| gzip compressed data           | 用 `gzip` 压缩的归档<br />An archive compressed with `gzip`  | no                                    |
| HTML document text             | 网页<br />A web page                                         | yes                                   |
| JPEG image data                | 压缩的 JPEG 图像<br />A compressed JPEG image                | no                                    |
| PostScript document text       | 一个 PostScript 文件<br />A PostScript file                  | yes                                   |
| Zip archive data               | 用 `zip` 压缩的归档<br />An archive compressed with `zip`    | no                                    |

While it may seem that most files cannot be viewed as text, a surprising number can be. This is especially true of the important configuration files. During our adventure we will see that many features of the operating system are controlled by text configuration files and shell scripts. In Linux, there are no secrets!

​	虽然看起来大多数文件都不能作为文本查看，但实际上有相当多的文件可以。这在重要的配置文件中尤其如此。在我们的探险中，我们将看到操作系统的许多功能都由文本配置文件和 shell 脚本控制。在 Linux 中，没有秘密！