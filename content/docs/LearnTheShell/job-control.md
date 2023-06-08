+++
title = "作业控制"
date = 2023-06-08T10:56:29+08:00
weight = 10
description = ""
isCJKLanguage = true
draft = false

+++

# 作业控制 - Job Control

https://linuxcommand.org/lc3_lts0100.php

In the previous lesson, we looked at some of the implications of Linux being a multi-user operating system. In this lesson, we will examine the multitasking nature of Linux, and how it is controlled with the command line interface.

​	在前面的课程中，我们了解了Linux作为多用户操作系统的一些含义。在本课程中，我们将探讨Linux的多任务性质，以及如何使用命令行界面来控制它。

As with any multitasking operating system, Linux executes multiple, simultaneous processes. Well, they appear simultaneous, anyway. Actually, a single processor core can only execute one process at a time but the Linux kernel manages to give each process its turn at the processor and each appears to be running at the same time.

​	与任何多任务操作系统一样，Linux可以执行多个并发进程。虽然它们看起来是同时执行的，但实际上，单个处理器核心一次只能执行一个进程，但Linux内核设法让每个进程轮流使用处理器，每个进程看起来都在同时运行。

There are several commands that are used to control processes. They are:

​	有几个命令用于控制进程。它们是： 

- `ps` - list the processes running on the system
- `kill` - send a signal to one or more processes (usually to "kill" a process)
- `jobs` - an alternate way of listing your own processes
- `bg` - put a process in the background
- `fg` - put a process in the foreground
- `ps` - 列出系统上正在运行的进程
- `kill` - 向一个或多个进程发送信号（通常用于"终止"一个进程）
- `jobs` - 列出您自己的进程的另一种方法
- `bg` - 将一个进程放入后台运行
- `fg` - 将一个进程放到前台运行

## 一个实际的例子 A Practical Example

While it may seem that this subject is rather obscure, it can be very practical for the average user who mostly works with the graphical user interface. Though it might not be apparent, most (if not all) graphical programs can be launched from the command line. Here's an example: there is a small program supplied with the X Window system called `xload` which displays a graph representing system load. We can execute this program by typing the following:

​	虽然这个主题可能看起来相当晦涩，但对于大多数主要使用图形用户界面的普通用户来说，它可以非常实用。虽然可能不明显，但大多数（如果不是全部）图形程序都可以从命令行启动。这里有一个例子：X Window系统中提供了一个名为`xload`的小程序，它显示表示系统负载的图形。我们可以通过键入以下命令来执行此程序：

```bash
[me@linuxbox me]$ xload
```

Notice that the small `xload` window appears and begins to display the system load graph. On systems where `xload` is not available, try `gedit` instead. Notice also that our prompt did not reappear after the program launched. The shell is waiting for the program to finish before control returns. If we close the `xload` window, the `xload` program terminates and the prompt returns.

​	请注意，会出现一个小的`xload`窗口，并开始显示系统负载图。在不可用`xload`的系统上，可以尝试使用`gedit`代替。还要注意，程序启动后我们的提示符没有重新出现。Shell在控制权返回之前等待程序完成。如果关闭`xload`窗口，`xload`程序将终止并返回提示符。

## 将程序放入后台 Putting a Program into the Background

Now, in order to make life a little easier, we are going to launch the `xload` program again, but this time we will put it in the background so that the prompt will return. To do this, we execute xload like this:

​	现在，为了让生活变得更轻松，我们将再次启动`xload`程序，但这次我们将把它放在后台，以便提示符会返回。为此，我们执行以下命令来启动`xload`：

```bash
[me@linuxbox me]$ xload &
[1] 1223
[me@linuxbox me]$
```

In this case, the prompt returned because the process was put in the background.

​	在这种情况下，提示符返回，因为进程被放入了后台。

Now imagine that we forgot to use the "&" symbol to put the program into the background. There is still hope. We can type `Ctrl-z` and the process will be suspended. We can verify this by seeing that the program's window is frozen. The process still exists, but is idle. To resume the process in the background, type the `bg` command (short for background). Here is an example:

​	现在想象一下，我们忘记使用"&"符号将程序放入后台。还有希望。我们可以输入`Ctrl-z`，进程将被挂起。我们可以通过查看程序的窗口是否被冻结来验证这一点。进程仍然存在，但是处于空闲状态。要在后台恢复该进程，请键入`bg`命令（缩写为background）。以下是一个例子：

```bash
[me@linuxbox me]$ xload
[2]+ Stopped xload

[me@linuxbox me]$ bg
[2]+ xload &
```



## 列出正在运行的进程 Listing Running Processes

Now that we have a process in the background, it would be helpful to display a list of the processes we have launched. To do this, we can use either the `jobs` command or the more powerful `ps` command.

​	现在，我们有一个后台进程，显示正在运行的进程列表将会很有帮助。为此，我们可以使用`jobs`命令或更强大的`ps`命令。

```bash
[me@linuxbox me]$ jobs
[1]+ Running xload&

[me@linuxbox me]$ ps
PID  TTY   TIME     CMD
1211 pts/4 00:00:00 bash
1246 pts/4 00:00:00 xload
1247 pts/4 00:00:00 ps

[me@linuxbox me]$
```



## 终止进程 Killing a Process

Suppose that we have a program that becomes unresponsive; how do we get rid of it? We use the `kill` command, of course. Let's try this out on `xload`. First, we need to identify the process we want to kill. We can use either `jobs` or `ps`, to do this. If we use `jobs` we will get back a job number. With `ps`, we are given a *process id* (PID). We will do it both ways:

​	假设我们有一个程序变得无响应，我们该如何摆脱它呢？我们当然可以使用`kill`命令。让我们在`xload`上试一试。首先，我们需要确定要终止的进程。我们可以使用`jobs`或`ps`来完成。如果我们使用`jobs`，我们将得到一个作业号。使用`ps`，我们会得到一个*进程ID*（PID）。我们将两种方式都演示一下：

```bash
[me@linuxbox me]$ xload &
[1] 1292

[me@linuxbox me]$ jobs
[1]+ Running xload&

[me@linuxbox me]$ kill %1

[me@linuxbox me]$ xload &
[2] 1293
[1] Terminated xload

[me@linuxbox me]$ ps
PID  TTY   TIME     CMD
1280 pts/5 00:00:00 bash
1293 pts/5 00:00:00 xload
1294 pts/5 00:00:00 ps

[me@linuxbox me]$ kill 1293
[2]+ Terminated xload

[me@linuxbox me]$
```



## 关于kill的更多信息 A Little More About kill

While the `kill` command is used to "kill" processes, its real purpose is to send *signals* to processes. Most of the time the signal is intended to tell the process to go away, but there is more to it than that. Programs (if they are properly written) listen for signals from the operating system and respond to them, most often to allow some graceful method of terminating. For example, a text editor might listen for any signal that indicates that the user is logging off, or that the computer is shutting down. When it receives this signal, it could save the work in progress before it exits. The `kill` command can send a variety of signals to processes. Typing:

​	尽管`kill`命令用于"终止"进程，但它的真正目的是向进程发送*信号*。大多数时候，这个信号是告诉进程离开，但其中还有更多内容。程序（如果它们编写正确）会监听来自操作系统的信号并对其作出响应，通常是为了允许一些优雅的终止方法。例如，文本编辑器可能会监听任何指示用户注销或计算机关闭的信号。当它收到这个信号时，它可以在退出之前保存正在进行的工作。`kill`命令可以向进程发送各种信号。键入：

```bash
kill -l
```

will print a list of the signals it supports. Many are rather obscure, but several are handy to know:

​	将打印出它支持的信号列表。其中许多信号相当晦涩，但有几个很方便：

| **信号 Signal #** | **名称 Name** | **描述 Description**                                         |
| ----------------- | ------------- | ------------------------------------------------------------ |
| **1**             | **SIGHUP**    | 挂断信号。程序可以监听此信号并对其作出响应。在关闭终端时，该信号会发送给在终端上运行的进程。<br />Hang up signal. Programs can listen for this signal and act upon it. This signal is sent to processes running in a terminal when you close the terminal. |
| **2**             | **SIGINT**    | 中断信号。该信号用于中断进程。程序可以处理此信号并对其作出响应。我们还可以通过在运行程序的终端窗口中键入`Ctrl-c`来直接发送此信号。<br />Interrupt signal. This signal is given to processes to interrupt them. Programs can process this signal and act upon it. We can also issue this signal directly by typing `Ctrl-c` in the terminal window where the program is running. |
| **15**            | **SIGTERM**   | 终止信号。该信号用于终止进程。同样，程序可以处理此信号并对其作出响应。这是如果没有指定信号，默认由`kill`命令发送的信号。<br />Termination signal. This signal is given to processes to terminate them. Again, programs can process this signal and act upon it. This is the default signal sent by the `kill` command if no signal is specified. |
| **9**             | **SIGKILL**   | 杀死信号。该信号导致Linux内核立即终止进程。程序无法监听此信号。<br />Kill signal. This signal causes the immediate termination of the process by the Linux kernel. Programs cannot listen for this signal. |

Now let's suppose that we have a program that is hopelessly hung and we want to get rid of it. Here's what we do:

​	现在假设我们有一个无望地挂起的程序，我们想摆脱它。下面是我们的做法： 

1. Use the `ps` command to get the process id (PID) of the process we want to terminate.
2. 使用`ps`命令获取我们要终止的进程的进程ID（PID）。
3. Issue a `kill` command for that PID.
4. 发出针对该PID的`kill`命令。
5. If the process refuses to terminate (i.e., it is ignoring the signal), send increasingly harsh signals until it does terminate.
6. 如果进程拒绝终止（即忽略了信号），则发送越来越严厉的信号，直到它终止为止。

```bash
[me@linuxbox me]$ ps x | grep bad_program
PID  TTY   STAT TIME COMMAND
2931 pts/5 SN   0:00 bad_program

[me@linuxbox me]$ kill -SIGTERM 2931

[me@linuxbox me]$ kill -SIGKILL 2931
```

In the example above we used the `ps` command with the x option to list all of our processes (even those not launched from the current terminal). In addition, we piped the output of the `ps` command into `grep` to list only list the program we are interested in. Next, we used `kill` to issue a SIGTERM signal to the troublesome program. In actual practice, it is more common to do it in the following way since the default signal sent by `kill` is SIGTERM and `kill` can also use the signal number instead of the signal name:

​	上面的示例中，我们使用了带有x选项的`ps`命令列出了我们的所有进程（即使它们不是从当前终端启动的）。此外，我们将`ps`命令的输出导入到`grep`中，以仅列出我们感兴趣的程序。接下来，我们使用`kill`发出SIGTERM信号给有问题的程序。在实际操作中，更常见的做法是以以下方式进行，因为`kill`发送的默认信号是SIGTERM，`kill`还可以使用信号编号而不是信号名称：

```bash
[me@linuxbox me]$ kill 2931
```

Then, if the process does not terminate, force it with the SIGKILL signal:

​	然后，如果进程不终止，可以使用SIGKILL信号强制终止：

```bash
[me@linuxbox me]$ kill -9 2931
```



## 到此为止！ That's It!

This concludes the "Learning the Shell" series of lessons. In the next series, "Writing Shell Scripts," we will look at how to automate tasks with the shell.

​	这就结束了“学习Shell”的一系列课程。在下一系列“编写Shell脚本”中，我们将讨论如何使用Shell自动化任务。

## 进一步阅读 Further Reading

- For a more in-depth treatment of the topic, see Chapter 10 in [*The Linux Command Line*](https://linuxcommand.org/tlcl.php).
- 要对该主题进行更深入的处理，请参阅[*The Linux Command Line*](https://linuxcommand.org/tlcl.php)一书中的第10章。
- [*1963 Timesharing: A Solution to Computer Bottlenecks*](https://youtu.be/Q07PhW5sCEk), a fascinating YouTube video from the Computer History Museum describing the first timesharing operating system and how it works. It's basically the same method used by all modern computers.
- [*1963年分时共享：解决计算机瓶颈问题*](https://youtu.be/Q07PhW5sCEk)，这是一段引人入胜的YouTube视频，来自计算机历史博物馆，介绍了第一个分时共享操作系统及其工作原理。它基本上是所有现代计算机使用的相同方法。