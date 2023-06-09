+++
title = "学习shell"
date = 2023-06-08T10:46:51+08:00
weight = 2
description = ""
isCJKLanguage = true
draft = false
+++

# 为什么要费心？Why Bother?

https://linuxcommand.org/lc3_learning_the_shell.php

Why do you need to learn the command line anyway? Well, let me tell you a story. Many years ago we had a problem where I worked. There was a shared drive on one of our file servers that kept getting full. I won't mention that this legacy operating system did not support user quotas; that's another story. But the server kept getting full and it stopped people from working. One of our software engineers spent a couple of hours writing a C++ program that would look through all the user's directories and add up the space they were using and make a listing of the results. Since I was forced to use the legacy OS while I was on the job, I installed [a Linux-like command line environment for it.](http://www.cygwin.com/) When I heard about the problem, I realized I could perform this task with this single line:

​	你为什么需要学习命令行？好吧，让我给你讲个故事。很多年前，我工作的地方遇到一个问题。我们的一个文件服务器上有一个共享驱动器，空间总是被占满。我不会提及这个遗留操作系统不支持用户配额的事实，那是另一个故事了。但服务器一直被占满，导致人们无法正常工作。我们的一位软件工程师花了几个小时编写了一个C++程序，用于查找所有用户目录并计算它们使用的空间，并生成一个结果列表。由于我在工作期间被迫使用这个遗留操作系统，我为其安装了一个[类似Linux的命令行环境](http://www.cygwin.com/)。当我听说这个问题时，我意识到我可以用一行命令来完成这个任务：

```bash
du -s * | sort -nr > $HOME/user_space_report.txt
```

Graphical user interfaces (GUIs) are helpful for many tasks, but they are not good for all tasks. I have long felt that most computers today are not powered by electricity. They instead seem to be powered by the "pumping" motion of the mouse. Computers were supposed to free us from manual labor, but how many times have you performed some task you felt sure the computer should be able to do but you ended up doing the work yourself by tediously working the mouse? Pointing and clicking, pointing and clicking.

​	图形用户界面（GUI）对许多任务很有帮助，但并非对所有任务都适用。我一直觉得现在的大多数计算机不是靠电力驱动，而是靠鼠标的“点击”运动驱动。计算机本应该使我们从体力劳动中解放出来，但你有多少次尝试让计算机完成某项任务，但最终却发现自己不得不费力地操作鼠标来亲自完成工作？指指点点，指指点点。

I once heard an author say that when you are a child you use a computer by looking at the pictures. When you grow up, you learn to read and write. Welcome to Computer Literacy 101. Now let's get to work.

​	我曾经听过一位作者说，当你还是个孩子时，你通过看图来使用计算机。当你长大后，你学会了阅读和写作。欢迎来到计算机素养101课程。现在让我们开始工作吧。