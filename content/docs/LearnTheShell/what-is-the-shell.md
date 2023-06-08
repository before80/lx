+++
title = "什么是 \"Shell\"?"
date = 2023-06-08T10:54:30+08:00
weight = 1
description = ""
isCJKLanguage = true
draft = false

+++

# 什么是"Shell"？ What is "the Shell"?

https://linuxcommand.org/lc3_lts0010.php

Simply put, the shell is a program that takes commands from the keyboard and gives them to the operating system to perform. In the old days, it was the only user interface available on a Unix-like system such as Linux. Nowadays, we have *graphical user interfaces (GUIs)* in addition to *command line interfaces (CLIs)* such as the shell.

​	简而言之，Shell是一个程序，它从键盘接收命令并将其传递给操作系统执行。在早期，它是类Unix系统（如Linux）上唯一可用的用户界面。如今，除了Shell，我们还有*图形用户界面（GUI）*，例如命令行界面（CLI）。

On most Linux systems a program called `bash` (which stands for Bourne Again SHell, an enhanced version of the original Unix shell program, `sh`, written by Steve Bourne) acts as the shell program. Besides `bash`, there are other shell programs available for Linux systems. These include: `ksh`, `tcsh` and `zsh`.

​	在大多数Linux系统中，一个名为`bash`的程序（代表Bourne Again SHell，是原始Unix shell程序`sh`的增强版本，由Steve Bourne编写）充当Shell程序。除了`bash`，Linux系统还有其他可用的Shell程序，包括：`ksh`、`tcsh`和`zsh`。

## 什么是"终端"？What's a "Terminal?"

It's a program called a *terminal emulator*. This is a program that opens a window and lets you interact with the shell. There are a bunch of different terminal emulators we can use. Some Linux distributions install several. These might include `gnome-terminal`, `konsole`, `xterm`, `rxvt`, `kvt`, `nxterm`, and `eterm`.

​	它是一个被称为*终端仿真器*的程序。它打开一个窗口，让你与Shell进行交互。我们可以使用许多不同的终端仿真器。一些Linux发行版会安装多个终端仿真器。它们可能包括`gnome-terminal`、`konsole`、`xterm`、`rxvt`、`kvt`、`nxterm`和`eterm`。

## 启动终端 Starting a Terminal

Window managers usually have a way to launch a terminal from the menu. Look through the list of programs to see if anything looks like a terminal emulator. While there are a number of different terminal emulators, they all do the same thing. They give us access to a shell session. You will probably develop a preference for one, based on the different bells and whistles it provides.

​	窗口管理器通常有一种从菜单启动终端的方法。浏览程序列表，看看是否有类似终端仿真器的选项。虽然有许多不同的终端仿真器，但它们都具有相同的功能。它们给我们提供了访问Shell会话的途径。你可能会根据它们提供的不同特性和功能，对其中的某个终端仿真器有所偏好。

## 测试键盘输入 Testing the Keyboard

OK, let's try some typing. Bring up a terminal window. The first thing we should see is a *shell prompt* that contains our user name and the name of the machine followed by a dollar sign. Something like this:

​	好的，让我们试着输入一些内容。打开一个终端窗口。我们应该首先看到一个包含我们的用户名和计算机名称后面跟着一个美元符号的*Shell提示符*。类似这样：

```bash
[me@linuxbox me]$
```

Excellent! Now type some nonsense characters and press the enter key.

​	太棒了！现在输入一些无意义的字符，然后按回车键。

```bash
[me@linuxbox me]$ kdkjflajfks
```

If all went well, we should have gotten an error message complaining that it cannot understand the command:

​	如果一切正常，我们应该会收到一个错误消息，说明它无法理解该命令：

```bash
[me@linuxbox me]$ kdkjflajfks
bash: kdkjflajfks: command not found
```

Wonderful! Now press the up-arrow key. Watch how our previous command "kdkjflajfks" returns. Yes, we have *command history*. Press the down-arrow and we get the blank line again.

​	太好了！现在按向上箭头键。注意我们之前的命令"kdkjflajfks"会重新出现。是的，我们有*命令历史记录*。按向下箭头键，我们又回到了空白行。

Recall the "kdkjflajfks" command using the up-arrow key if needed. Now, try the left and right-arrow keys. We can position the text cursor anywhere in the command line. This allows us to easily correct mistakes.

​	如果需要，可以使用向上箭头键来重新调用"kdkjflajfks"命令。现在，试试左右箭头键。我们可以将文本光标定位在命令行的任何位置。这使我们可以轻松纠正错误。

> 你没有以root用户身份操作，对吗？ You're not operating as root, are you?
>
> If the last character of your shell prompt is # rather than $, you are operating as the *superuser*. This means that you have administrative privileges. This can be dangerous, since you are able to delete or overwrite any file on the system. Unless you absolutely need administrative privileges, do not operate as the superuser.
>
> ​	如果你的Shell提示符的最后一个字符是`#`而不是`$`，那么你正在以*超级用户*的身份操作。这意味着你拥有管理员特权。这可能是危险的，因为你可以删除或覆盖系统上的任何文件。除非你绝对需要管理员特权，否则不要以超级用户身份操作。

## 使用鼠标 Using the Mouse

Even though the shell is a command line interface, the mouse is still handy.

​	虽然Shell是一个命令行界面，但鼠标仍然很方便。

Besides using the mouse to scroll the contents of the terminal window, we can can use it to copy text. Drag the mouse over some text (for example, "kdkjflajfks" right here on the browser window) while holding down the left button. The text should highlight. Release the left button and move the mouse pointer to the terminal window and press the middle mouse button (alternately, press both the left and right buttons at the same time when working on a touch pad). The text we highlighted in the browser window should be copied into the command line.

​	除了使用鼠标滚动终端窗口的内容，我们还可以使用它来复制文本。按住左键，拖动鼠标在一些文本上（例如，在浏览器窗口中的"kdkjflajfks"），文本将被突出显示。释放左键，将鼠标指针移动到终端窗口上，并按下中间鼠标按钮（或者在触摸板上同时按下左键和右键）。我们在浏览器窗口中突出显示的文本将被复制到命令行中。

## 关于焦点的一些说明... A few words about focus...

When you installed your Linux system and its window manager (most likely Gnome or KDE), it was configured to behave in some ways like that legacy operating system.

​	当你安装Linux系统及其窗口管理器（很可能是Gnome或KDE）时，它会配置成某种程度上与那个遗留操作系统类似的行为。

In particular, it probably has its *focus policy* set to "click to focus." This means that in order for a window to gain focus (become active) you have to click in the window. This is contrary to traditional X Window behavior. You should consider setting the focus policy to "focus follows mouse". You may find it strange at first that windows don't raise to the front when they get focus (you have to click on the window to do that), but you will enjoy being able to work on more than one window at once without having the active window obscuring the other. Try it and give it a fair trial; I think you will like it. You can find this setting in the configuration tools for your window manager.

​	特别是，它可能将其*焦点策略*设置为"点击获取焦点"。这意味着为了使窗口获得焦点（变为活动状态），你必须在窗口中点击。这与传统的X Window行为相反。你应该考虑将焦点策略设置为"焦点跟随鼠标"。起初，窗口不会在获得焦点时自动提到前台可能会让你感到奇怪（你必须点击窗口才能做到），但你将享受到能够同时在多个窗口上工作而不会被活动窗口遮挡的便利。尝试一下并进行充分试用；我相信你会喜欢它的。你可以在窗口管理器的配置工具中找到此设置。

## 进一步阅读 Further Reading

- The [Wikipedia entry for Steve Bourne](https://en.wikipedia.org/wiki/Stephen_R._Bourne), developer of the original Bourne shell
- The [Wikipedia article on the Unix shell](https://en.wikipedia.org/wiki/Unix_shell), the place where all this fun got started
- The ["Power Terminals"](https://linuxcommand.org/lc3_adv_powerterm.php) Adventure
- [Steve Bourne的维基百科页面](https://en.wikipedia.org/wiki/Stephen_R._Bourne)，原始Bourne shell的开发者
- [Unix Shell的维基百科文章](https://en.wikipedia.org/wiki/Unix_shell)，这就是所有这些有趣的东西开始的地方
- ["Power Terminals"](https://linuxcommand.org/lc3_adv_powerterm.php) 探险