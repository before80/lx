+++
title = "shell函数"
weight = 6
date = 2023-06-08T11:17:04+08:00
description = ""
isCJKLanguage = true
draft = false

+++

# shell函数 Shell Functions

https://linuxcommand.org/lc3_wss0060.php

As programs get longer and more complex, they become more difficult to design, code, and maintain. As with any large endeavor, it is often useful to break a single, large task into a series of smaller tasks.

​	随着程序变得越来越长和复杂，设计、编码和维护就变得更加困难。与任何大型任务一样，将一个单一的、庞大的任务拆分为一系列较小的任务通常是有用的。

In this lesson, we will begin to break our single monolithic script into a number of separate functions.

​	在本课中，我们将开始将我们的单一的庞大脚本拆分为多个独立的函数。

To get familiar with this idea, let's consider the description of an everyday task -- going to the market to buy food. Imagine that we were going to describe the task to a man from Mars.

​	为了熟悉这个概念，让我们考虑一个日常任务的描述 - 去市场买食物。想象一下，我们要将这个任务描述给来自火星的人。

Our first top-level description might look like this:

​	我们的第一个顶层描述可能是这样的：

1. Leave house
2. Drive to market
3. Park car
4. Enter market
5. Purchase food
6. Drive home
7. Park car
8. Enter house
9. 离开房子
10. 开车去市场
11. 停车
12. 进入市场
13. 购买食物
14. 开车回家
15. 停车
16. 进入房子

This description covers the overall process of going to the market; however a man from Mars will probably require additional detail. For example, the "Park car" sub task could be described as follows:

​	这个描述涵盖了去市场的整个过程；然而，来自火星的人可能需要额外的细节。例如，“停车”子任务可以描述如下：

1. Find parking space
2. Drive car into space
3. Turn off motor
4. Set parking brake
5. Exit car
6. Lock car
7. 寻找停车位
8. 将车开入停车位
9. 关闭发动机
10. 设置驻车制动器
11. 离开车辆
12. 锁车

Of course the task "Turn off motor" has a number of steps such as "turn off ignition" and "remove key from ignition switch," and so on.

​	当然，“关闭发动机”这个任务还有许多步骤，比如“关闭点火开关”和“取下点火开关上的钥匙”，等等。

This process of identifying the top-level steps and developing increasingly detailed views of those steps is called *top-down design*. This technique allows us to break large complex tasks into many small, simple tasks.

​	这个确定顶层步骤并开发逐渐详细的视图的过程被称为*自顶向下设计*。这种技术使我们能够将大型复杂任务拆分为许多小而简单的任务。

As our script continues to grow, we will use top down design to help us plan and code our script.

​	随着我们的脚本不断增长，我们将使用自顶向下设计来帮助我们规划和编码脚本。

If we look at our script's top-level tasks, we find the following list:

​	如果我们查看脚本的顶层任务，我们会发现以下列表：

1. Open page
2. Open head section
3. Write title
4. Close head section
5. Open body section
6. Write title
7. Write time stamp
8. Close body section
9. Close page
10. 打开页面
11. 打开头部部分
12. 写标题
13. 关闭头部部分
14. 打开正文部分
15. 写标题
16. 写时间戳
17. 关闭正文部分
18. 关闭页面

All of these tasks are implemented, but we want to add more. Let's insert some additional tasks after task 7:

​	所有这些任务都已经实现，但我们想要添加更多。让我们在任务 7 之后插入一些额外的任务：

1. Write time stamp
2. Write system release info
3. Write up-time
4. Write drive space
5. Write home space
6. Close body section
7. Close page
8. 写时间戳
9. 写系统发布信息
10. 写运行时间
11. 写驱动器空间
12. 写主目录空间
13. 关闭正文部分
14. 关闭页面

It would be great if there were commands that performed these additional tasks. If there were, we could use command substitution to place them in our script like so:

​	如果有能够执行这些额外任务的命令就好了。如果有的话，我们可以使用命令替换将它们放在我们的脚本中，像这样：

```bash
#!/bin/bash

# sysinfo_page - A script to produce a system information HTML file

##### Constants

TITLE="System Information for $HOSTNAME"
RIGHT_NOW="$(date +"%x %r %Z")"
TIME_STAMP="Updated on $RIGHT_NOW by $USER"

##### Main

cat <<- _EOF_
  <html>
  <head>
      <title>$TITLE</title>
  </head>

  <body>
      <h1>$TITLE</h1>
      <p>$TIME_STAMP</p>
      $(system_info)
      $(show_uptime)
      $(drive_space)
      $(home_space)
  </body>
  </html>
_EOF_
```

While there are no commands that do exactly what we need, we can create them using *shell functions*.

​	虽然没有确切满足我们需求的命令，但我们可以使用*shell 函数*来创建它们。

As we learned in lesson 2, shell functions act as "little programs within programs" and allow us to follow top-down design principles. To add the shell functions to our script, we'll change it so:

​	正如我们在第二课中学到的那样，shell 函数充当“程序中的小程序”，使我们能够遵循自顶向下的设计原则。为了将 shell 函数添加到我们的脚本中，我们将对它进行如下更改：

```bash
#!/bin/bash

# sysinfo_page - A script to produce an system information HTML file

##### Constants

TITLE="System Information for $HOSTNAME"
RIGHT_NOW="$(date +"%x %r %Z")"
TIME_STAMP="Updated on $RIGHT_NOW by $USER"

##### Functions

system_info()
{

}


show_uptime()
{

}


drive_space()
{

}


home_space()
{

}

##### Main

cat <<- _EOF_
  <html>
  <head>
      <title>$TITLE</title>
  </head>

  <body>
      <h1>$TITLE</h1>
      <p>$TIME_STAMP</p>
      $(system_info)
      $(show_uptime)
      $(drive_space)
      $(home_space)
  </body>
  </html>
_EOF_
```

A couple of important points about functions: First, they must appear before we attempt to use them. Second, the function body (the portions of the function between the { and } characters) must contain at least one valid command. As written, the script will not execute without error, because the function bodies are empty. The simple way to fix this is to place a `return` statement in each function body. After we do this, our script will execute successfully again.

​	关于函数有几点重要的要点：首先，它们必须出现在我们尝试使用它们之前。其次，函数体（花括号 { 和 } 之间的部分）必须包含至少一条有效命令。按照当前的编写方式，脚本将无法执行而出现错误，因为函数体是空的。修复这个问题的简单方法是在每个函数体中放置一个 `return` 语句。在我们这样做之后，脚本将再次成功执行。

## 保持脚本工作正常 Keep Your Scripts Working

When we develop a program, it is is often a good practice to add a small amount of code, run the script, add some more code, run the script, and so on. This way, if we introduce a mistake into the code, it will be easier to find and correct.

​	当我们开发一个程序时，通常最好的做法是添加一小段代码，运行脚本，再添加一些代码，运行脚本，依此类推。这样，如果我们在代码中引入了错误，就会更容易找到和纠正它。

As we add functions to your script, we can also use a technique called *stubbing* to help watch the logic of our script develop. Stubbing works like this: imagine that we are going to create a function called "system_info" but we haven't figured out all of the details of its code yet. Rather than hold up the development of the script until we are finished with `system_info`, we just add an `echo` command like this:

​	当我们向脚本添加函数时，我们还可以使用一种叫做*存根*的技术来帮助观察脚本逻辑的发展。存根的工作方式如下：假设我们要创建一个名为 "system_info" 的函数，但我们还没有弄清楚它的所有代码细节。与其等到我们完成 `system_info` 后再继续脚本的开发，我们只需添加一个 `echo` 命令，像这样：

```bash
system_info()
{
    # Temporary function stub
    echo "function system_info"
}
```

This way, our script will still execute successfully, even though we do not yet have a finished system_info function. We will later replace the temporary stubbing code with the complete working version.

​	这样，即使我们还没有一个完整的 `system_info` 函数，我们的脚本仍然可以成功执行。稍后，我们将用完整的工作版本替换临时的存根代码。

The reason we use an `echo` command is so we get some feedback from the script to indicate that the functions are being executed.

​	我们使用 `echo` 命令的原因是为了从脚本中获得一些反馈，以指示函数正在被执行。

Let's go ahead and write stubs for our new functions and keep the script working.

​	让我们继续为我们的新函数编写存根，保持脚本的正常工作。

```bash
#!/bin/bash

# sysinfo_page - A script to produce an system information HTML file

##### Constants

TITLE="System Information for $HOSTNAME"
RIGHT_NOW="$(date +"%x %r %Z")"
TIME_STAMP="Updated on $RIGHT_NOW by $USER"

##### Functions

system_info()
{
    # Temporary function stub
    echo "function system_info"
}


show_uptime()
{
    # Temporary function stub
    echo "function show_uptime"
}


drive_space()
{
    # Temporary function stub
    echo "function drive_space"
}


home_space()
{
    # Temporary function stub
    echo "function home_space"
}


##### Main

cat <<- _EOF_
  <html>
  <head>
      <title>$TITLE</title>
  </head>

  <body>
      <h1>$TITLE</h1>
      <p>$TIME_STAMP</p>
      $(system_info)
      $(show_uptime)
      $(drive_space)
      $(home_space)
  </body>
  </html>
_EOF_
```


