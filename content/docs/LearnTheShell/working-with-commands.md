+++
title = "使用命令"
date = 2023-06-08T10:55:44+08:00
weight = 6
description = ""
isCJKLanguage = true
draft = false

+++

# 使用命令 - Working with Commands

https://linuxcommand.org/lc3_lts0060.php

Up until now, we have seen a number of commands and their mysterious options and arguments. In this lesson, we will try to remove some of that mystery. We will introduce the following commands.

​	到目前为止，我们已经看到了许多命令及其神秘的选项和参数。在本课程中，我们将尝试消除其中的一些神秘感。我们将介绍以下命令。 

- `type` - Display information about command type
- `which` - Locate a command
- `help` - Display reference page for shell builtin
- `man` - Display an on-line command reference
- `type` - 显示命令类型的信息
- `which` - 定位一个命令
- `help` - 显示内置shell命令的参考页面
- `man` - 显示在线命令参考

## 什么是"命令"? What are "Commands?"

Commands can be one of 4 different kinds:

​	命令可以是以下4种不同类型之一：

1. **An executable program** like all those files we saw in /usr/bin. Within this category, programs can be *compiled binaries* such as programs written in C and C++, or programs written in *scripting languages* such as the shell, Perl, Python, Ruby, etc.
2. **可执行程序**，如我们在 /usr/bin 中看到的所有文件。在此类别中，程序可以是*编译的二进制文件*，例如用 C 和 C++ 编写的程序，或者是用*脚本语言*（如Shell、Perl、Python、Ruby等）编写的程序。
3. **A command built into the shell itself.** bash provides a number of commands internally called *shell builtins*. The `cd` command, for example, is a shell builtin.
4. **内置于shell本身的命令**。bash 提供了许多称为*shell内置命令*的内部命令。例如，`cd` 命令就是一个内置命令。
5. **A shell function.** These are miniature shell scripts incorporated into the *environment*. We will cover configuring the environment and writing shell functions in later lessons, but for now, just be aware that they exist.
6. **shell函数**。这些是嵌入到*环境*中的小型shell脚本。我们将在后面的课程中介绍配置环境和编写shell函数，但现在只需要知道它们的存在即可。
7. **An alias.** Commands that we can define ourselves, built from other commands. This will be covered in a later lesson.
8. **别名**。我们可以自定义的由其他命令构建的命令。这将在后面的课程中介绍。

## 识别命令 Identifying Commands

It is often useful to know exactly which of the four kinds of commands is being used and Linux provides a couple of ways to find out.

​	通常，了解正在使用的四种命令类型中的哪一种非常有用，Linux 提供了几种方法来找到答案。

### type

The `type` command is a shell builtin that displays the kind of command the shell will execute, given a particular command name. It works like this:

​	`type` 命令是一个内置于shell的命令，它显示给定命令名称的命令类型。它的使用方法如下：

```bash
type command
```

where “command” is the name of the command we want to examine. Here are some examples:

​	其中 "command" 是我们想要检查的命令的名称。下面是一些示例：

```bash
[me@linuxbox me]$ type type
type is a shell builtin
[me@linuxbox me]$ type ls
s is aliased to `ls --color=auto'
[me@linuxbox me]$ type cp
cp is /bin/cp
```

Here we see the results for three different commands. Notice that the one for `ls` and how the ls command is actually an alias for the `ls` command with the “-- color=auto” option added. Now we know why the output from `ls` is displayed in color!

​	这里我们看到了三个不同命令的结果。注意 `ls` 命令的结果，ls 实际上是 `ls` 命令的一个别名，并添加了 `--color=auto` 选项。现在我们知道为什么 `ls` 命令的输出显示为彩色了！

### which

Sometimes there is more than one version of an executable program installed on a system. While this is not very common on desktop systems, it's not unusual on large servers. To determine the exact location of a given executable, the `which` command is used:

​	有时在系统上安装了一个可执行程序的多个版本。尽管在桌面系统上这种情况并不常见，但在大型服务器上并不罕见。为了确定给定可执行程序的确切位置，可以使用 `which` 命令：

```bash
[me@linuxbox me]$ which ls
/bin/ls
```

`which` only works for executable programs, not builtins nor aliases that are substitutes for actual executable programs.

​	`which` 仅适用于可执行程序，而不适用于内置命令或替代实际可执行程序的别名。

## 获取命令文档 Getting Command Documentation

With this knowledge of what a command is, we can now search for the documentation available for each kind of command.

​	有了对命令的了解，我们现在可以搜索每种类型命令可用的文档。

### help

`bash` has a built-in help facility available for each of the shell builtins. To use it, type “help” followed by the name of the shell builtin. Optionally, we can add the -m option to change the format of the output. For example:

​	`bash` 为每个shell内置命令提供了一个内置的帮助功能。要使用它，键入“help”，然后是shell内置命令的名称。我们还可以添加 `-m` 选项以更改输出的格式。例如：

```bash
[me@linuxbox me]$ help -m cd
NAME
    cd - Change the shell working directory.

SYNOPSIS
    cd [-L|-P] [dir]

DESCRIPTION
    Change the shell working directory.
    
    Change the current directory to DIR.  The default DIR is the value of the
    HOME shell variable.
    
    The variable CDPATH defines the search path for the directory containing
    DIR.  Alternative directory names in CDPATH are separated by a colon (:).
    A null directory name is the same as the current directory.  If DIR begins
    with a slash (/), then CDPATH is not used.
    
    If the directory is not found, and the shell option `cdable_vars' is set,
    the word is assumed to be  a variable name.  If that variable has a value,
    its value is used for DIR.
    
    Options:
        -L  force symbolic links to be followed
        -P  use the physical directory structure without following symbolic
      links
    
    The default is to follow symbolic links, as if `-L' were specified.
    
    Exit Status:
    Returns 0 if the directory is changed; non-zero otherwise.

SEE ALSO
    bash(1)

IMPLEMENTATION
    GNU bash, version 4.1.5(1)-release (i486-pc-linux-gnu)
    Copyright (C) 2009 Free Software Foundation, Inc.
```

**A note on notation:** When square brackets appear in the description of a command's syntax, they indicate optional items. A vertical bar character indicates mutually exclusive items. In the case of the `cd` command above:

​	**关于符号的说明：** 当命令语法的描述中出现方括号时，表示这些是可选项。竖线字符表示互斥的选项。对于上面的 `cd` 命令：

```bash
cd [-L|-P] [dir]
```

This notation says that the command `cd` may be followed optionally by either a “-L” or a “-P” and further, optionally followed by the argument “dir”.

​	这个符号说明表示 `cd` 命令后面可以选择跟着“-L”或者“-P”，然后再可选地跟着参数“dir”。

### `--help`

Many executable programs support a “`--help`” option that displays a description of the command's supported syntax and options. For example:

​	许多可执行程序支持“`--help`”选项，它会显示命令的支持语法和选项的描述。例如：

```bash
[me@linuxbox me]$ mkdir --help
Usage: mkdir [OPTION] DIRECTORY...
Create the DIRECTORY(ies), if they do not already exist.
Mandatory arguments to long options are mandatory for short options
too.

   -Z, --context=CONTEXT (SELinux) set security context to CONTEXT
   -m, --mode=MODE   set file mode (as in chmod), not a=rwx – umask
   -p, --parents     no error if existing, make parent directories as
                     needed
   -v, --verbose     print a message for each created directory
   --help            display this help and exit
   --version         output version information and exit
```

Some programs don't support the “--help” option, but try it anyway. Often it results in an error message that will reveal similar usage information.

​	有些程序不支持“`--help`”选项，但还是可以尝试一下。通常会显示出一个错误消息，其中包含类似的使用信息。

### man

Most executable programs intended for command line use provide a formal piece of documentation called a *manual* or *man page*. A special paging program called `man` is used to view them. It is used like this:

​	大多数用于命令行的可执行程序都提供了一份正式的文档，称为*手册*或*man页*。用于查看它们的特殊分页程序称为 `man`。使用方法如下：

```bash
man program
```

where “program” is the name of the command to view. Man pages vary somewhat in format but generally contain a title, a synopsis of the command's syntax, a description of the command's purpose, and a listing and description of each of the command's options. Man pages, however, do not usually include examples, and are intended as a reference, not a tutorial. Let's try viewing the man page for the `ls` command:

​	其中“program”是要查看的命令的名称。Man页的格式有所不同，但通常包含标题、命令语法的概述、命令目的的描述以及每个命令选项的列表和描述。不过，Man页通常不包含示例，并且旨在作为参考而不是教程。让我们尝试查看 `ls` 命令的 man 页：

```bash
[me@linuxbox me]$ man ls
```

On most Linux systems, `man` uses `less` to display the manual page, so all of the familiar `less` commands work while displaying the page.

​	在大多数Linux系统上，`man` 使用 `less` 来显示手册页，因此在显示页面时可以使用所有熟悉的 `less` 命令。

### README和其他文档文件 - README and Other Documentation Files

Many software packages installed on your system have documentation files residing in the `/usr/share/doc` directory. Most of these are stored in plain text format and can be viewed with `less`. Some of the files are in HTML format and can be viewed with a web browser. We may encounter some files ending with a “.gz” extension. This indicates that they have been compressed with the `gzip` compression program. The gzip package includes a special version of `less` called `zless` that will display the contents of gzip-compressed text files.

​	安装在系统上的许多软件包在 `/usr/share/doc` 目录中有文档文件。其中大多数以纯文本格式存储，并可以使用 `less` 查看。其中一些文件是HTML格式的，可以使用Web浏览器查看。可能会遇到一些以“.gz”扩展名结尾的文件。这表示它们已使用gzip压缩程序进行了压缩。gzip软件包包含一个名为`zless`的特殊版本的`less`，可以显示gzip压缩的文本文件的内容。