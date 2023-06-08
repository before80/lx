+++
title = "一些真正的工作"
weight = 7
date = 2023-06-08T11:17:18+08:00
description = ""
isCJKLanguage = true
draft = false

+++

# 一些真正的工作 Some Real Work

https://linuxcommand.org/lc3_wss0070.php

In this lesson, we will develop some of our shell functions and get our script to produce some useful information.

​	在本课中，我们将开发一些 shell 函数，并使我们的脚本生成一些有用的信息。

## show_uptime

The `show_uptime` function will display the output of the [`uptime`](https://linuxcommand.org/lc3_man_pages/uptime1.html) command. The uptime command outputs several interesting facts about the system, including the length of time the system has been "up" (running) since its last re-boot, the number of users and recent system load.

​	`show_uptime` 函数将显示 [`uptime`](https://linuxcommand.org/lc3_man_pages/uptime1.html) 命令的输出结果。`uptime` 命令输出了系统的一些有趣信息，包括系统自上次重新启动以来的运行时间，用户数量以及最近的系统负载。

```bash
[me@linuxbox me]$ uptime
9:15pm up 2 days, 2:32, 2 users, load average: 0.00, 0.00, 0.00
```

To get the output of the uptime command into our HTML page, we will code our shell function like this, replacing our temporary stubbing code with the finished version:

​	为了将 `uptime` 命令的输出添加到我们的 HTML 页面中，我们将编写如下的 shell 函数，将临时的存根代码替换为最终版本：

```bash
show_uptime()
{
    echo "<h2>System uptime</h2>"
    echo "<pre>"
    uptime
    echo "</pre>"
}
```

As we can see, this function outputs a stream of text containing a mixture of HTML tags and command output. When the command substitution takes place in the main body of the our program, the output from our function becomes part of the here script.

​	正如我们所看到的，该函数输出了一个包含 HTML 标记和命令输出混合的文本流。当命令替换发生在我们程序的主体部分时，函数的输出将成为 here 脚本的一部分。

## drive_space

The drive_space function will use the [`df`](https://linuxcommand.org/lc3_man_pages/df1.html) command to provide a summary of the space used by all of the mounted file systems.

​	`drive_space` 函数将使用 [`df`](https://linuxcommand.org/lc3_man_pages/df1.html) 命令提供所有已挂载文件系统使用空间的摘要信息。

```bash
[me@linuxbox me]$ df
Filesystem   1k-blocks      Used Available Use% Mounted on
/dev/hda2       509992    225772    279080  45% /
/dev/hda1        23324      1796     21288   8% /boot
/dev/hda3     15739176   1748176  13832360  12% /home
/dev/hda5      3123888   3039584     52820  99% /usr
```

In terms of structure, the `drive_space` function is very similar to the show_uptime function:

​	在结构上，`drive_space` 函数与 `show_uptime` 函数非常相似：

```bash
drive_space()
{
    echo "<h2>Filesystem space</h2>"
    echo "<pre>"
    df
    echo "</pre>"
}
```



## home_space

The `home_space` function will display the amount of space each user is using in his/her home directory. It will display this as a list, sorted in descending order by the amount of space used.

​	`home_space` 函数将显示每个用户在他/她的主目录中使用的空间量。它将按照使用空间的大小降序排列，以列表形式显示。

```bash
home_space()
{
    echo "<h2>Home directory space by user</h2>"
    echo "<pre>"
    echo "Bytes Directory"
    du -s /home/* | sort -nr
    echo "</pre>"
}
```

Note that in order for this function to successfully execute, the script must be run by the superuser, since the [`du`](https://linuxcommand.org/lc3_man_pages/du1.html) command requires superuser privileges to examine the contents of the /home directory.

​	请注意，为了使该函数成功执行，脚本必须以超级用户身份运行，因为 [`du`](https://linuxcommand.org/lc3_man_pages/du1.html) 命令需要超级用户权限来检查 /home 目录的内容。

## system_info

We're not ready to finish the `system_info` function yet. In the meantime, we will improve the stubbing code so it produces valid HTML:

​	我们还没有准备好完成 `system_info` 函数。与此同时，我们将改进存根代码，以生成有效的 HTML：

```bash
system_info()
{
    echo "<h2>System release info</h2>"
    echo "<p>Function not yet implemented</p>"
}
```

