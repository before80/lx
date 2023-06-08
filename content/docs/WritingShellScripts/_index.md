+++
title = "编写shell脚本"
date = 2023-06-08T10:47:23+08:00
weight = 3
description = ""
isCJKLanguage = true
draft = false
+++

# 这里是乐趣开始的地方 Here is Where the Fun Begins

https://linuxcommand.org/lc3_writing_shell_scripts.php

With the thousands of commands available to the command line user, how can we remember them all? The answer is, we don't. The real power of the computer is its ability to do the work for us. To get it to do that, we use the power of the shell to automate things. We write *shell scripts*.

​	对于命令行用户来说，有成千上万个可用命令，我们如何记住它们呢？答案是，我们不需要记住它们全部。计算机的真正力量在于它能够替我们完成工作。为了实现这一点，我们利用 shell 的力量来自动化任务。我们编写*shell 脚本*。

## 什么是 Shell 脚本？ What are Shell Scripts?

In the simplest terms, a shell script is a file containing a series of commands. The shell reads this file and carries out the commands as though they have been entered directly on the command line.

​	简而言之，shell 脚本是包含一系列命令的文件。Shell 会读取该文件，并像直接在命令行上输入这些命令一样执行它们。

The shell is somewhat unique, in that it is both a powerful command line interface to the system and a scripting language interpreter. As we will see, most of the things that can be done on the command line can be done in scripts, and most of the things that can be done in scripts can be done on the command line.

​	Shell 在某种程度上是独特的，它既是一个强大的系统命令行接口，又是一种脚本语言解释器。正如我们将看到的，大多数可以在命令行上完成的事情也可以在脚本中完成，而大多数可以在脚本中完成的事情也可以在命令行上完成。

We have already covered many shell features, but we have focused on those features most often used directly on the command line. The shell also provides a set of features usually (but not always) used when writing programs.

​	我们已经介绍了许多 shell 特性，但我们侧重于那些在命令行上直接使用的特性。Shell 还提供了一套通常（但并不总是）在编写程序时使用的特性。

Scripts unlock the power of our Linux machine. So let's have some fun!

​	脚本释放了我们 Linux 机器的力量。所以让我们来玩一玩吧！