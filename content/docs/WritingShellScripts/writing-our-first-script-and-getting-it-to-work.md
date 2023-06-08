+++
title = "编写我们的第一个脚本并使其正常工作"
date = 2023-06-08T11:15:23+08:00
weight = 1
description = ""
isCJKLanguage = true
draft = false

+++

# 编写我们的第一个脚本并使其正常工作 Writing Our First Script and Getting It to Work

https://linuxcommand.org/lc3_wss0010.php

To successfully write a shell script, we have to do three things:

​	要成功编写一个 shell 脚本，我们需要完成三件事情：

1. Write a script
2. Give the shell permission to execute it
3. Put it somewhere the shell can find it
4. 编写一个脚本
5. 给 shell 赋予执行权限
6. 将脚本放在 shell 能够找到的地方

## 编写脚本 Writing a Script

A shell script is a file that contains ASCII text. To create a shell script, we use a *text editor*. A text editor is a program, like a word processor, that reads and writes ASCII text files. There are many, many text editors available for Linux systems, both for the command line and GUI environments. Here is a list of some common ones:

​	Shell 脚本是一个包含 ASCII 文本的文件。为了创建一个 shell 脚本，我们使用一个*文本编辑器*。文本编辑器是一个程序，类似于文字处理器，用于读写 ASCII 文本文件。对于 Linux 系统，有许多文本编辑器可供选择，包括命令行和图形界面环境。以下是一些常见的文本编辑器列表：

| 名称 Name | 描述 Description                                             | 界面 Interface           |
| :-------- | :----------------------------------------------------------- | :----------------------- |
| `vi, vim` | nix 文本编辑器的鼻祖 `vi` 以其晦涩难懂的用户界面而闻名。好处是，`vi` 强大、轻巧且快速。学习 `vi` 是 Unix 系统使用的基本技能，因为它在类 Unix 系统上通用。在大多数 Linux 发行版中，`vi` 的增强版本称为 `vim`，取代了 vi。`vim` 是一款出色的编辑器，值得花时间学习它。<br />The granddaddy of Unix text editors, `vi`, is infamous for its obtuse user interface. On the bright side, `vi` is powerful, lightweight, and fast. Learning `vi` is a Unix rite of passage, since it is universally available on Unix-like systems. On most Linux distributions, an enhanced version of `vi` called `vim` is provided in place of vi. `vim` is a remarkable editor and well worth taking the time to learn it. | 命令行<br />command line |
| `Emacs`   | 文本编辑器领域的真正巨头是 Emacs，最初由[Richard Stallman](https://en.wikipedia.org/wiki/Richard_Stallman)编写。Emacs 包含（或可以包含）为文本编辑器构想的所有功能。值得注意的是，`vi` 和 Emacs 的拥护者之间存在激烈的宗教战争，争论哪个更好。<br />The true giant in the world of text editors is Emacs originally written by [Richard Stallman](https://en.wikipedia.org/wiki/Richard_Stallman). Emacs contains (or can be made to contain) every feature ever conceived of for a text editor. It should be noted that `vi` and Emacs fans fight bitter religious wars over which is better. | 命令行<br />command line |
| `nano`    | `nano` 是 `pine` 邮件程序提供的文本编辑器的免费克隆版本。`nano` 非常易于使用，但与 `vim` 和 `emacs` 相比，功能较少。`nano` 推荐给需要命令行编辑器的初学者。<br />`nano` is a free clone of the text editor supplied with the `pine` email program. `nano` is very easy to use but is very short on features compared to `vim` and `emacs`. `nano` is recommended for first-time users who need a command line editor. | 命令行<br />command line |
| `gedit`   | `gedit` 是 GNOME 桌面环境附带的编辑器。gedit 易于使用，并包含足够的功能作为初学者级别的编辑器。<br />`gedit` is the editor supplied with the GNOME desktop environment. gedit is easy to use and contains enough features to be a good beginners-level editor. | 图形界面<br />graphical  |
| `kwrite`  | `kwrite` 是 KDE 附带的“高级编辑器”。它具有语法高亮功能，对于程序员和脚本编写者来说非常有用。<br />`kwrite` is the "advanced editor" supplied with KDE. It has syntax highlighting, a helpful feature for programmers and script writers. | 图形界面<br />graphical  |

Let's fire up our text editor and type in our first script as follows:

​	让我们启动我们的文本编辑器，并按照以下方式输入我们的第一个脚本：

```bash
#!/bin/bash

# My first script

echo "Hello World!"
```

Clever readers will have figured out how to copy and paste the text into the text editor ;-)

​	聪明的读者已经知道如何将文本复制并粘贴到文本编辑器中 ;-)

This is a traditional "Hello World" program. Forms of this program appear in almost every introductory programming book. We'll save the file with some descriptive name. How about `hello_world`?

​	这是一个传统的“Hello World”程序。这种程序的形式几乎出现在每本入门编程书籍中。我们将文件保存为一些描述性的名称。比如 `hello_world`？

The first line of the script is important. It is a special construct, called a *shebang*, given to the system indicating what program is to be used to interpret the script. In this case, `/bin/bash`. Other scripting languages such as `Perl, awk, tcl, Tk,` and `python` also use this mechanism.

​	脚本的第一行很重要。它是一个特殊的结构，称为*shebang*，告诉系统要使用哪个程序来解释脚本。在本例中是 `/bin/bash`。其他脚本语言如 `Perl, awk, tcl, Tk` 和 `python` 也使用这种机制。

The second line is a *comment*. Everything that appears after a "#" symbol is ignored by `bash`. As our scripts become bigger and more complicated, comments become vital. They are used by programmers to explain what is going on so that others can figure it out. The last line is the `echo` command. This command simply prints its arguments on the display.

​	第二行是一个*注释*。`bash` 会忽略 "#" 符号后面的所有内容。随着我们的脚本变得越来越大和复杂，注释变得至关重要。程序员使用注释来解释正在发生的事情，以便其他人可以理解。最后一行是 `echo` 命令。该命令简单地将其参数打印到显示器上。

## 设置权限 Setting Permissions

The next thing we have to do is give the shell permission to execute our script. This is done with the `chmod` command as follows:

​	接下来，我们需要给 shell 赋予执行脚本的权限。可以使用 `chmod` 命令执行以下操作：

```bash
[me@linuxbox me]$ chmod 755 hello_world
```

The "755" will give us read, write, and execute permission. Everybody else will get only read and execute permission. To make the script private, (i.e., only we can read and execute), use "700" instead.

​	"755" 将赋予我们读取、写入和执行权限，其他用户只能获取读取和执行权限。如果要使脚本私有（即只有我们自己能读取和执行），可以使用 "700"。

## 将其放在路径中 Putting It in Our Path

At this point, our script will run. Try this:

​	此时，我们的脚本将可以运行。尝试运行以下命令：

```bash
[me@linuxbox me]$ ./hello_world
```

We should see "Hello World!" displayed.

​	我们应该会看到 "Hello World!" 显示出来。

Before we go any further, we need to talk about paths. When we type the name of a command, the system does not search the entire computer to find where the program is located. That would take a long time. We see that we don't usually have to specify a complete path name to the program we want to run, the shell just seems to know.

​	在继续之前，我们需要讨论一下路径。当我们键入一个命令的名称时，系统不会搜索整个计算机来找到程序所在的位置。那样会花费很长时间。我们注意到，通常我们不需要指定要运行的程序的完整路径名称，shell 似乎自己就知道。

Well, that's correct. The shell does know. Here's how: the shell maintains a list of directories where executable files (programs) are kept, and only searches the directories on that list. If it does not find the program after searching each directory on the list, it will issue the famous `command not found` error message.

​	是的，这是正确的。shell 确实知道。原因如下：shell 维护着一个包含可执行文件（程序）所在目录的列表，并且只搜索该列表中的目录。如果在列表中的每个目录中都找不到该程序，它将显示著名的`command not found`错误消息。

This list of directories is called our *path*. We can view the list of directories with the following command:

​	这个目录列表被称为我们的*路径*。我们可以使用以下命令查看目录列表，其中 `$PATH` 是指定命令输入时将搜索的以冒号分隔的目录列表：

```bash
[me@linuxbox me]$ echo $PATH
```

This will return a colon separated list of directories that will be searched if a specific path name is not given when a command is entered. In our first attempt to execute our new script, we specified a pathname ("./") to the file.

​	这将返回一个以冒号分隔的目录列表，如果在输入命令时没有指定特定路径名，系统将在这些目录中进行搜索。在我们尝试执行新脚本时，我们指定了一个路径名（"./"）。

We can add directories to our path with the following command, where *directory* is the name of the directory we want to add:

​	我们可以使用以下命令将目录添加到我们的路径中，其中 *directory* 是要添加的目录名称：

```bash
[me@linuxbox me]$ export PATH=$PATH:directory
```

A better way would be to edit our `.bash_profile` file to include the above command. That way, it would be done automatically every time we log in.

​	更好的方法是编辑我们的 `.bash_profile` 文件，将上述命令包含其中。这样，每次登录时都会自动执行该命令。

Most Linux distributions encourage a practice in which each user has a specific directory for the programs they personally use. This directory is called `bin` and is a subdirectory of our home directory. If we do not already have one, we can create it with the following command:

​	大多数 Linux 发行版都鼓励每个用户为他们个人使用的程序创建一个特定的目录。这个目录称为 `bin`，是我们家目录的子目录。如果我们还没有这个目录，可以使用以下命令创建它：

```bash
[me@linuxbox me]$ mkdir ~/bin
```

If we move or copy our script into our new `bin` directory we'll be all set. Now we just have to type:

​	如果我们将脚本移动或复制到新的 `bin` 目录中，我们就准备好了。现在我们只需要输入：

```bash
[me@linuxbox me]$ hello_world
```

and our script will run. Most distributions will have the `~/bin` directory already in the PATH, but on some distributions, most notably Ubuntu (and other Debian-based distributions), we may need to restart our terminal session before our newly created `bin` directory is added to our PATH.

​	我们的脚本将运行。大多数发行版都已经将 `~/bin` 目录添加到 PATH 中，但在某些发行版中，特别是 Ubuntu（和其他基于 Debian 的发行版），我们可能需要重新启动终端会话，以便我们新创建的 `bin` 目录被添加到 PATH 中。