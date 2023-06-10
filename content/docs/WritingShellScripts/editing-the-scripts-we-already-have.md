+++
title = "编辑我们已有的脚本"
weight = 2
date = 2023-06-08T11:15:56+08:00
description = ""
isCJKLanguage = true
draft = false

+++

# 编辑我们已有的脚本 Editing the Scripts We Already Have

https://linuxcommand.org/lc3_wss0020.php

Before we start writing new scripts, We'll take a look at some scripts we already have. These scripts were put into our home directory when our account was created, and are used to configure the behavior of our sessions on the computer. We can edit these scripts to change things.

​	在我们开始编写新脚本之前，我们先来看一下我们已经有的一些脚本。这些脚本在我们的账户创建时被放置在我们的主目录中，并用于配置我们在计算机上的会话行为。我们可以编辑这些脚本来进行更改。

In this lesson, we will look at a couple of these scripts and learn a few important new concepts about the shell.

​	在本课程中，我们将查看其中一些脚本，并学习关于 shell 的一些重要的新概念。

During our shell session, the system is holding a number of facts about the world in its memory. This information is called the *environment*. The environment contains such things as our path, our user name, and much more. We can examine a complete list of what is in the environment with the `set` command.

​	在我们的 shell 会话期间，系统会在内存中保存关于该世界的一些事实。这些信息被称为*环境*。环境包含诸如路径、用户名等等。我们可以使用 `set` 命令查看环境中的完整列表。

Two types of commands are often contained in the environment. They are *aliases* and *shell functions*.

​	环境中经常包含两种类型的命令：*别名*和*shell 函数*。

## 环境是如何建立的？ How is the Environment Established?

When we log on to the system, the bash program starts, and reads a series of configuration scripts called *startup files*. These define the default environment shared by all users. This is followed by more startup files in our home directory that define our personal environment. The exact sequence depends on the type of shell session being started. There are two kinds: a *login shell session* and a *non-login shell session*. A login shell session is one in which we are prompted for our user name and password; when we start a virtual console session, for example. A non-login shell session typically occurs when we launch a terminal session in the GUI.

​	当我们登录到系统时，bash 程序启动，并读取一系列的配置脚本，称为*启动文件*。这些启动文件定义了所有用户共享的默认环境。接下来是我们主目录中的更多启动文件，定义了我们个人的环境。确切的顺序取决于所启动的 shell 会话类型。有两种类型：*登录 shell 会话*和*非登录 shell 会话*。登录 shell 会话是指我们被提示输入用户名和密码的会话；例如，当我们启动一个虚拟控制台会话时。非登录 shell 会话通常发生在图形界面中启动终端会话时。

Login shells read one or more startup files as shown below:

​	登录 shell 会读取一个或多个启动文件，如下所示：

| **文件 File**     | **内容 Contents**                                            |
| ----------------- | ------------------------------------------------------------ |
| `/etc/profile`    | 适用于所有用户的全局配置脚本。<br />A global configuration script that applies to all users. |
| `~/.bash_profile` | 用户个人的启动文件。可以用来扩展或覆盖全局配置脚本中的设置。<br />A user's personal startup file. Can be used to extend or override settings in the global configuration script. |
| `~/.bash_login`   | 如果找不到 `~/.bash_profile`，bash 尝试读取此脚本。<br />If `~/.bash_profile` is not found, bash attempts to read this script. |
| `~/.profile`      | 如果既找不到 `~/.bash_profile` 也找不到 `~/.bash_login`，bash 尝试读取此文件。这是基于 Debian 的发行版（如 Ubuntu）的默认设置。<br />If neither `~/.bash_profile` nor `~/.bash_login` is found, bash attempts to read this file. This is the default in Debian-based distributions, such as Ubuntu. |

Non-login shell sessions read the following startup files:

​	非登录 shell 会话读取以下启动文件：

| **文件 File**      | **内容 Contents**                                            |
| ------------------ | ------------------------------------------------------------ |
| `/etc/bash.bashrc` | 适用于所有用户的全局配置脚本。<br />A global configuration script that applies to all users. |
| `~/.bashrc`        | 用户个人的启动文件。可以用来扩展或覆盖全局配置脚本中的设置。<br />A user's personal startup file. Can be used to extend or override settings in the global configuration script. |

In addition to reading the startup files above, non-login shells also inherit the environment from their parent process, usually a login shell.

​	除了读取上述启动文件，非登录 shell 会话还会从其父进程继承环境，通常是登录 shell。

Take a look at your system and see which of these startup files you have. Remember— since most of the file names listed above start with a period (meaning that they are hidden), you will need to use the “-a” option when using ls.

​	查看系统，看看你有哪些启动文件。记住，由于上面列出的大多数文件名以句点开头（表示它们是隐藏文件），使用 `ls` 命令时需要使用 `-a` 选项。

The `~/.bashrc` file is probably the most important startup file from the ordinary user’s point of view, since it is almost always read. Non-login shells read it by default and most startup files for login shells are written in such a way as to read the `~/.bashrc` file as well.

​	`~/.bashrc` 文件可能是普通用户来说最重要的启动文件，因为它几乎总是会被读取。非登录 shell 会话默认读取它，而大多数登录 shell 的启动文件也以这种方式编写，以便读取 `~/.bashrc` 文件。

If we take a look inside a typical `.bash_profile` (this one taken from a CentOS system), it looks something like this:

​	如果我们查看一个典型的 `.bash_profile`（这是从 CentOS 系统中获取的一个示例），它的内容如下所示：

```bash
# .bash_profile
# Get the aliases and functions 获取别名和函数 
if [ -f ~/.bashrc ]; then
  . ~/.bashrc
fi

# User specific environment and startup programs
# 用户特定的环境和启动程序
PATH=$PATH:$HOME/bin
export PATH
```

Lines that begin with a “`#`” are comments and are not read by the shell. These are there for human readability. The first interesting thing occurs on the fourth line, with the following code:

​	以“`#`”开头的行是注释，不会被 shell 读取。这些注释是为了人类可读性。有趣的事情发生在第四行，具有以下代码：

```bash
if [ -f ~/.bashrc ]; then
  . ~/.bashrc
fi
```

This is called an *if compound command*, which we will cover fully in a later lesson, but for now we will translate:

​	这被称为*if 复合命令*，我们将在后面的课程中详细介绍，但现在我们先翻译一下：

If the file `"~/.bashrc"` exists, then read the `"~/.bashrc"` file.

​	如果文件 `"~/.bashrc"` 存在，则读取 `"~/.bashrc"` 文件。

We can see that this bit of code is how a login shell gets the contents of `.bashrc`. The next thing in our startup file does is set the PATH variable to add the `~/bin` directory to the path.

​	我们可以看到，这段代码是登录 shell 获取 `.bashrc` 的内容的方式。我们的启动文件中的下一个部分是将 PATH 变量设置为将 `~/bin` 目录添加到路径中。

Lastly, we have:

​	最后，我们有：

```bash
export PATH
```

The `export` command tells the shell to make the contents of the PATH variable available to child processes of this shell.

​	`export` 命令告诉 shell 将 PATH 变量的内容提供给该 shell 的子进程。

## 别名 Aliases

An alias is an easy way to create a new command which acts as an abbreviation for a longer one. It has the following syntax:

​	别名是创建一个新命令的简单方式，该命令作为一个较长命令的缩写。它具有以下语法：

```bash
alias name=value
```

where *name* is the name of the new command and *value* is the text to be executed whenever *name* is entered on the command line.

​	其中 *name* 是新命令的名称，*value* 是在命令行输入 *name* 时要执行的文本。

Let's create an alias called "l" and make it an abbreviation for the command "ls -l". We'll move to our home directory and using our favorite text editor, open the file `.bashrc` and add this line to the end of the file:

​	让我们创建一个名为 "l" 的别名，将其作为命令 "ls -l" 的缩写。我们切换到我们的主目录，并使用我们喜欢的文本编辑器打开 `.bashrc` 文件，并在文件末尾添加以下行：

```bash
alias l='ls -l'
```

By adding the `alias` command to the file, we have created a new command called "l" which will perform "ls -l". To try out our new command, close the terminal session and start a new one. This will reload the `.bashrc` file. Using this technique, we can create any number of custom commands for ourselves. Here is another one to try:

​	通过将 `alias` 命令添加到文件中，我们创建了一个名为 "l" 的新命令，该命令将执行 "ls -l"。要尝试我们的新命令，关闭终端会话并启动一个新的会话。这将重新加载 `.bashrc` 文件。使用这种技术，我们可以为自己创建任意数量的自定义命令。下面是另一个要尝试的示例：

```bash
alias today='date +"%A, %B %-d, %Y"'
```

This alias creates a new command called "today" that will display today's date with nice formatting.

​	该别名创建了一个名为 "today" 的新命令，它将以漂亮的格式显示今天的日期。

By the way, the `alias` command is just another shell builtin. We can create our aliases directly at the command prompt; however they will only remain in effect during the current shell session. For example:

​	顺便说一下，`alias` 命令只是另一个 shell 内置命令。我们可以直接在命令提示符下创建别名；但是，它们只在当前的 shell 会话中有效。例如：

```bash
[me@linuxbox me]$ alias l='ls -l'
```



## Shell 函数 Shell Functions

Aliases are good for very simple commands, but to create something more complex, we need *shell functions*. Shell functions can be thought of as "scripts within scripts" or little sub-scripts. Let's try one. Open `.bashrc` with our text editor again and replace the alias for "today" with the following:

​	别名适用于非常简单的命令，但是要创建更复杂的内容，我们需要使用*shell 函数*。可以将 shell 函数视为“脚本中的脚本”或小的子脚本。让我们尝试一个例子。再次用我们的文本编辑器打开 `.bashrc`，并用以下内容替换 "today" 的别名：

```bash
today() {
    echo -n "Today's date is: "
    date +"%A, %B %-d, %Y"
}
```

Believe it or not, `()` is a shell builtin too, and as with `alias`, we can enter shell functions directly at the command prompt.

​	你可能不会相信，`()` 也是一个 shell 内置命令，就像 `alias` 一样，我们可以直接在命令提示符下输入 shell 函数。

```bash
[me@linuxbox me]$ today() {
> echo -n "Today's date is: "
> date +"%A, %B %-d, %Y"
> }
[me@linuxbox me]$
```

However, like `alias`, shell functions defined directly on the command line only last as long as the current shell session.

​	然而，与 `alias` 类似，直接在命令行定义的 shell 函数只在当前 shell 会话中有效，持续的时间与当前 shell 会话相同。