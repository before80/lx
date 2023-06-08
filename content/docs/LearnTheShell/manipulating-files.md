+++
title = "操作文件"
date = 2023-06-08T10:55:30+08:00
weight = 5
description = ""
isCJKLanguage = true
draft = false

+++

# 操作文件 Manipulating Files

https://linuxcommand.org/lc3_lts0050.php

This lesson will introduce the following commands:

​	本课程将介绍以下命令：

- `cp` - copy files and directories
- `mv` - move or rename files and directories
- `rm` - remove files and directories
- `mkdir` - create directories
- `cp` - 复制文件和目录
- `mv` - 移动或重命名文件和目录
- `rm` - 删除文件和目录
- `mkdir` - 创建目录

These four commands are among the most frequently used Linux commands. They are the basic commands for manipulating both files and directories.

​	这四个命令是最常用的 Linux 命令之一。它们是用于操作文件和目录的基本命令。

Now, to be frank, some of the tasks performed by these commands are more easily done with a graphical file manager. With a file manager, you can drag and drop a file from one directory to another, cut and paste files, delete files, etc. So why use these old command line programs?

​	说实话，其中一些命令执行的任务可以更容易地通过图形文件管理器完成。使用文件管理器，您可以将文件从一个目录拖放到另一个目录，剪切和粘贴文件，删除文件等等。那么为什么要使用这些老旧的命令行程序呢？

The answer is power and flexibility. While it is easy to perform simple file manipulations with a graphical file manager, complicated tasks can be easier with the command line programs. For example, how would you copy all the HTML files from one directory to another, but only copy files that did not exist in the destination directory or were newer than the versions in the destination directory? Pretty hard with with a file manager. Pretty easy with the command line:

​	答案是权力和灵活性。虽然使用图形文件管理器可以执行简单的文件操作，但使用命令行程序可以更容易地处理复杂的任务。例如，如何将一个目录中的所有 HTML 文件复制到另一个目录，但只复制目标目录中不存在或比目标目录中的版本更新的文件？使用文件管理器可能会很困难，但使用命令行却很简单：

```bash
[me@linuxbox me]$ cp -u *.html destination
```



## 通配符 Wildcards

Before we begin with our commands, we'll first look at a shell feature that makes these commands so powerful. Since the shell uses filenames so much, it provides special characters to help you rapidly specify groups of filenames. These special characters are called *wildcards*. Wildcards allow you to select filenames based on patterns of characters. The table below lists the wildcards and what they select:

​	在开始使用这些命令之前，我们首先来看一下使这些命令如此强大的一个 shell 特性。由于 shell 经常使用文件名，它提供了一些特殊字符，可以帮助您快速指定文件名的组。这些特殊字符称为 *通配符*。通配符允许您根据字符模式选择文件名。下表列出了通配符及其选择的内容：



| **通配符 Wildcard** | **含义 Meaning**                                             |
| :------------------ | :----------------------------------------------------------- |
| *****               | 匹配任意字符<br />Matches any characters                     |
| **?**               | 匹配任意单个字符<br />Matches any single character           |
| **[characters]**    | 匹配字符集中的任意字符。字符集也可以表示为 *POSIX 字符类*，如下所示：POSIX 字符类<br />**[:alnum:]**字母数字字符<br />**[:alpha:]**字母字符<br />**[:digit:]**数字字符<br />**[:upper:]**大写字母字符<br />**[:lower:]**小写字母字符<br />Matches any character that is a member of the set *characters*. The set of characters may also be expressed as a *POSIX character class* such as one of the following:POSIX Character Classes**[:alnum:]**Alphanumeric characters**[:alpha:]**Alphabetic characters**[:digit:]**Numerals**[:upper:]**Uppercase alphabetic characters**[:lower:]**Lowercase alphabetic characters |
| **[!characters]**   | 匹配不属于字符集中的任意字符<br />Matches any character that is not a member of the set *characters* |

Using wildcards, it is possible to construct very sophisticated selection criteria for filenames. Here are some examples of patterns and what they match:

​	使用通配符，可以构造非常复杂的文件名选择条件。以下是一些模式示例及其匹配的内容：

| **模式 Pattern**                | **匹配 Matches**                                             |
| :------------------------------ | :----------------------------------------------------------- |
| `*`                             | 所有文件名 <br />All filenames                               |
| `g*`                            | 以字母 "g" 开头的所有文件名<br />All filenames that begin with the character "g" |
| `b*.txt`                        | 以字母 "b" 开头且以 ".txt" 结尾的所有文件名<br />All filenames that begin with the character "b" and end with the characters ".txt" |
| `Data???`                       | 以 "Data" 开头后面紧跟着恰好 3 个字符的文件名<br />Any filename that begins with the characters "Data" followed by exactly 3 more characters |
| `[abc]*`                        | 以 "a"、"b" 或 "c" 开头后面跟着任意其他字符的文件名<br />Any filename that begins with "a" or "b" or "c" followed by any other characters |
| `[[:upper:]]*`                  | 以大写字母开头的任何文件名。这是字符类的一个示例。<br />Any filename that begins with an uppercase letter. This is an example of a character class. |
| `BACKUP.[[:digit:]][[:digit:]]` | 另一个字符类的示例。此模式匹配以 "BACKUP." 开头后面紧跟着恰好两个数字的文件名。<br />Another example of character classes. This pattern matches any filename that begins with the characters "BACKUP." followed by exactly two numerals. |
| `*[![:lower:]]`                 | 不以小写字母结尾的任何文件名<br />Any filename that does not end with a lowercase letter. |

We can use wildcards with any command that accepts filename arguments.

​	我们可以在接受文件名参数的任何命令中使用通配符。

## cp

The `cp` program copies files and directories. In its simplest form, it copies a single file:

​	`cp` 程序用于复制文件和目录。在最简单的形式中，它可以复制单个文件：

```bash
[me@linuxbox me]$ cp file1 file2
```

It can also be used to copy multiple files (and/or directories) to a different directory:

​	它还可以用于将多个文件（和/或目录）复制到不同的目录：

```bash
[me@linuxbox me]$ cp file... directory
```

**A note on notation:** ... signifies that an item can be repeated one or more times.

​	**关于符号的说明：** ... 表示一个项目可以重复一次或多次。

Other useful examples of `cp` and its options include:

​	`cp` 和其选项的其他有用示例包括：

| **命令 Command**      | **结果 Results**                                             |
| :-------------------- | :----------------------------------------------------------- |
| `cp *file1 file2*`    | 将 *file1* 的内容复制到 *file2*。如果 *file2* 不存在，则创建它；**否则，file2 将被 file1 的内容静默覆盖**。<br />Copies the contents of *file1* into *file2*. If *file2* does not exist, it is created; **otherwise, \*file2\* is silently overwritten with the contents of \*file1\*.** |
| `cp -i *file1 file2*` | 与上面相同，然而，由于指定了 "-i"（交互式）选项，如果 *file2* 存在，则在覆盖它之前会提示用户。<br />Like above however, since the "-i" (interactive) option is specified, if *file2* exists, the user is prompted before it is overwritten with the contents of *file1*. |
| `cp *file1 dir1*`     | 将 *file1* 的内容（命名为 *file1*）复制到目录 *dir1* 内。<br />Copy the contents of *file1* (into a file named *file1*) inside of directory *dir1*. |
| `cp -R *dir1 dir2*`   | 复制目录 *dir1* 的内容。如果目录 *dir2* 不存在，则创建它。否则，它在目录 *dir2* 中创建一个名为 *dir1* 的目录。<br />Copy the contents of the directory *dir1*. If directory *dir2* does not exist, it is created. Otherwise, it creates a directory named *dir1* within directory *dir2*. |

## mv

The `mv` command moves or renames files and directories depending on how it is used. It will either move one or more files to a different directory, or it will rename a file or directory. To rename a file, it is used like this:

​	`mv` 命令根据使用方式的不同，可以移动或重命名文件和目录。要重命名文件，可以使用以下方式：

```bash
[me@linuxbox me]$ mv filename1 filename2
```

To move files (and/or directories) to a different directory:

​	要将文件（和/或目录）移动到另一个目录：

```bash
[me@linuxbox me]$ mv file... directory
```

Examples of `mv` and its options include:

​	`mv` 和其选项的示例包括：

| **命令 Command**        | **结果 Results**                                             |
| :---------------------- | :----------------------------------------------------------- |
| `mv *file1 file2*`      | 如果 *file2* 不存在，则将 *file1* 重命名为 *file2*。**如果 file2存在，则它的内容将被 file1 的内容静默替换**。<br />If *file2* does not exist, then *file1* is renamed *file2*. **If file2 exists, its contents are silently replaced with the contents of file1.** |
| `mv -i *file1 file2*`   | 与上面相同，然而，由于指定了 "-i"（交互式）选项，如果 *file2* 存在，则在覆盖它之前会提示用户。<br />Like above however, since the "-i" (interactive) option is specified, if *file2* exists, the user is prompted before it is overwritten with the contents of *file1*. |
| `mv *file1 file2 dir1*` | 将文件 *file1* 和 *file2* 移动到目录 *dir1*。如果 *dir1* 不存在，`mv` 将报错退出。<br />The files *file1* and *file2* are moved to directory *dir1*. If *dir1* does not exist, `mv` will exit with an error. |
| `mv *dir1 dir2*`        | 如果 *dir2* 不存在，则将 *dir1* 重命名为 *dir2*。如果 *dir2* 存在，则将目录 *dir1* 移动到目录 *dir2* 中。<br />If *dir2* does not exist, then *dir1* is renamed *dir2*. If *dir2* exists, the directory *dir1* is moved within directory *dir2*. |

## rm

The `rm` command removes (deletes) files and directories.

​	`rm` 命令用于删除文件和目录。

```bash
[me@linuxbox me]$ rm file...
```

Using the recursive option (`-r`), `rm` can also be used to delete directories:

​	使用递归选项（`-r`），`rm` 还可以用于删除目录：

```bash
[me@linuxbox me]$ rm -r directory...
```

Examples of `rm` and its options include:

​	`rm` 和其选项的示例包括：

| **命令 Command**      | **结果 Results**                                             |
| :-------------------- | :----------------------------------------------------------- |
| `rm *file1 file2*`    | 删除 *file1* 和 *file2*。<br />Delete *file1* and *file2*.   |
| `rm -i *file1 file2*` | 与上面相同，然而，由于指定了 "-i"（交互式）选项，每个文件被删除之前会提示用户。<br />Like above however, since the "-i" (interactive) option is specified, the user is prompted before each file is deleted. |
| `rm -r *dir1 dir2*`   | 删除目录 *dir1* 和 *dir2* 及其所有内容。<br />Directories *dir1* and *dir2* are deleted along with all of their contents. |

## 在使用 rm 时要小心！ Be careful with rm!

Linux does not have an undelete command. Once you delete something with `rm`, it's gone. You can inflict terrific damage on your system with `rm` if you are not careful, particularly with wildcards.

​	Linux 没有恢复命令。一旦使用 `rm` 删除了某个东西，它就消失了。如果不小心使用 `rm`，特别是使用通配符，可能会对系统造成严重的破坏。

***Before you use `rm` with wildcards, try this helpful trick:\*** construct your command using `ls` instead. By doing this, you can see the effect of your wildcards before you delete files. After you have tested your command with `ls`, recall the command with the up-arrow key and then substitute `rm` for `ls` in the command.

​	***在使用带有通配符的 `rm` 之前，试试这个有用的技巧：*** 使用 `ls` 构建命令。这样，您可以在删除文件之前查看通配符的效果。在使用 `ls` 测试过命令后，可以使用上箭头键召回命令，然后在命令中将 `ls` 替换为 `rm`。

## mkdir

The `mkdir` command is used to create directories. To use it, you simply type:

​	`mkdir` 命令用于创建目录。只需键入：

```bash
[me@linuxbox me]$ mkdir directory...
```

## 使用通配符的命令 Using Commands with Wildcards

Since the commands we have covered here accept multiple file and directories names as arguments, you can use wildcards to specify them. Here are a few examples:

​	由于我们在这里介绍的命令接受多个文件和目录名称作为参数，您可以使用通配符来指定它们。以下是一些示例：

| **命令 Command**        | **结果 Results**                                             |
| :---------------------- | :----------------------------------------------------------- |
| `cp *.txt text_files`   | 将当前工作目录中以 ".txt" 结尾的所有文件复制到名为 "text_files" 的现有目录中。<br />Copy all files in the current working directory with names ending with the characters ".txt" to an existing directory named *text_files*. |
| `mv dir1 ../*.bak dir2` | 将子目录 "dir1" 和当前工作目录的父目录中以 ".bak" 结尾的所有文件移动到名为 "dir2" 的现有目录中。<br />Move the subdirectory *dir1* and all the files ending in ".bak" in the current working directory's parent directory to an existing directory named *dir2*. |
| `rm *~`                 | 删除当前工作目录中以字符 "~" 结尾的所有文件。某些应用程序使用此命名方案创建备份文件。使用此命令将清理目录中的备份文件。<br />Delete all files in the current working directory that end with the character "~". Some applications create backup files using this naming scheme. Using this command will clean them out of a directory. |

## 进一步阅读 Further Reading

- Chapter 4 of [*The Linux Command Line*](https://linuxcommand.org/tlcl.php) covers this topic in more detail
- [Linux 命令行](https://linuxcommand.org/tlcl.php)第 4 章对这个主题进行了更详细的讨论。