+++
title = "变量"
weight = 4
date = 2023-06-08T11:16:26+08:00
description = ""
isCJKLanguage = true
draft = false

+++

# 变量 Variables

https://linuxcommand.org/lc3_wss0040.php

```bash
#!/bin/bash

# sysinfo_page - A script to produce an HTML file

cat <<- _EOF_
    <html>
    <head>
        <title>
        My System Information
        </title>
    </head>

    <body>
    <h1>My System Information</h1>
    </body>
    </html>
_EOF_
```

Now that we have our script working, let's improve it. First off, we'll make some changes because we want to be lazy. In the script above, we see that the phrase "My System Information" is repeated. This is wasted typing (and extra work!) so we'll improve it like this:

​	现在我们的脚本可以工作了，让我们对它进行改进。首先，我们想偷点懒。在上面的脚本中，我们发现短语 "我的系统信息" 是重复的。这是多余的键入（和额外的工作！），所以我们将对其进行改进：

```bash
#!/bin/bash

# sysinfo_page - A script to produce an HTML file

title="My System Information"

cat <<- _EOF_
    <html>
    <head>
        <title>
        $title
        </title>
    </head>

    <body>
    <h1>$title</h1>
    </body>
    </html>
_EOF_
```

We added a line to the beginning of the script and replaced the two occurrences of the phrase "My System Information" with `$title`.

​	我们在脚本的开头添加了一行，并将短语 "我的系统信息" 的两个出现替换为 `$title`。

## 变量 Variables

What we have done is to introduce a fundamental concept that appears in every programming language, *variables*. Variables are areas of memory that can be used to store information and are referred to by a name. In the case of our script, we created a variable called "title" and placed the phrase "My System Information" into memory. Inside the here script that contains our HTML, we use "$title" to tell the shell to perform *parameter expansion* and replace the name of the variable with the variable's contents.

​	我们所做的是引入了一种在每种编程语言中都出现的基本概念，*变量*。变量是用于存储信息的内存区域，并通过名称进行引用。在我们的脚本中，我们创建了一个名为 "title" 的变量，并将短语 "我的系统信息" 存储到内存中。在包含我们的 HTML 的 here 脚本中，我们使用 "$title" 来告诉 shell 执行*参数展开*，将变量的名称替换为变量的内容。

Whenever the shell sees a word that begins with a "$", it tries to find out what was assigned to the variable and substitutes it.

​	当 shell 遇到以 "$" 开头的单词时，它会尝试查找所分配给变量的内容并进行替换。

## 如何创建变量 How to Create a Variable

To create a variable, put a line in the script that contains the name of the variable followed immediately by an equal sign ("="). No spaces are allowed. After the equal sign, assign the information to store.

​	要创建一个变量，在脚本中放置一行，其中包含变量名，紧跟着一个等号（"="）。不允许有空格。在等号后面，分配要存储的信息。

## 变量名从何而来？ Where Do Variable Names Come From?

We just make them up. That's right; we get to choose the names for our variables. There are a few rules.

​	我们只是自己编写。没错，我们可以选择变量的名称。有一些规则。

1. Names must start with a letter.
2. A name must not contain embedded spaces. Use underscores instead.
3. Punctuation marks are not permitted.
4. 名称必须以字母开头。
5. 名称不能包含空格。使用下划线代替。
6. 不允许使用标点符号。

## 这如何增加我们的懒惰程度？  How Does This Increase Our Laziness?

The addition of the `title` variable made our life easier in two ways. First, it reduced the amount of typing we had to do. Second and more importantly, it made our script easier to maintain.

​	添加 `title` 变量以两种方式简化了我们的生活。首先，它减少了我们需要键入的量。更重要的是，它使我们的脚本更易于维护。

As we write more and more scripts (or do any other kind of programming), we will see that programs are rarely ever finished. They are modified and improved by their creators and others. After all, that's what open source development is all about. Let's say that we wanted to change the phrase "My System Information" to "Linuxbox System Information." In the previous version of the script, we would have had to change this in two locations. In the new version with the `title` variable, we only have to change it in one place. Since our script is so small, this might seem like a trivial matter, but as scripts get larger and more complicated, it becomes very important.

​	当我们编写越来越多的脚本（或进行任何其他类型的编程）时，我们会发现程序很少是完成的。它们会被其创建者和其他人进行修改和改进。毕竟，这就是开源开发的目的。假设我们想要将短语 "我的系统信息" 更改为 "Linuxbox 系统信息"。在以前版本的脚本中，我们需要在两个位置进行更改。而在具有 `title` 变量的新版本中，我们只需要在一个位置进行更改。由于我们的脚本非常小，这可能看起来像是一个琐碎的事情，但是随着脚本变得越来越大和更复杂，这变得非常重要。

## 环境变量 Environment Variables

When we start our shell session, some variables are already set by the startup files we looked at earlier. To see all the variables that are in the environment, use the `printenv` command. One variable in our environment contains the host name for the system. We will add this variable to our script like so:

​	当我们启动 shell 会话时，一些变量已经由我们之前查看过的启动文件设置好了。要查看环境中的所有变量，请使用 `printenv` 命令。我们的环境中的一个变量包含系统的主机名。我们将像这样将此变量添加到我们的脚本中：

```bash
#!/bin/bash

# sysinfo_page - A script to produce an HTML file

title="System Information for"

cat <<- _EOF_
    <html>
    <head>
        <title>
        $title $HOSTNAME
        </title>
    </head>

    <body>
    <h1>$title $HOSTNAME</h1>
    </body>
    </html>
_EOF_
```

Now our script will always include the name of the machine on which we are running. Note that, by convention, environment variables names are uppercase.

​	现在我们的脚本将始终包含正在运行的计算机的名称。请注意，按照约定，环境变量名称是大写的。