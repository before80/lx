+++
title = "命令替换和常量"
weight = 5
date = 2023-06-08T11:16:52+08:00
description = ""
isCJKLanguage = true
draft = false

+++

# 命令替换和常量 Command Substitution and Constants

https://linuxcommand.org/lc3_wss0050.php

In the previous lesson, we learned how to create variables and perform parameter expansions with them. In this lesson, we will extend this idea to show how we can substitute the results from commands.

​	在上一课中，我们学习了如何创建变量并对其进行参数展开。在本课中，我们将扩展这个概念，展示如何替换命令的结果。

When we last left our script, it could create an HTML page that contained a few simple lines of text, including the host name of the machine which we obtained from the environment variable HOSTNAME. Now, we will add a time stamp to the page to indicate when it was last updated, along with the user that did it.

​	上次我们离开时，我们的脚本可以创建一个包含一些简单文本的 HTML 页面，其中包括从环境变量 HOSTNAME 获取的计算机的主机名。现在，我们将在页面上添加一个时间戳，以指示上次更新的时间，以及执行更新的用户。

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
    <p>Updated on $(date +"%x %r %Z") by $USER</p>
    </body>
    </html>
_EOF_
```

As we can see, another environment variable, `USER`, is used to get the user name. In addition, we used this strange looking thing:

​	正如我们所看到的，另一个环境变量 `USER` 用于获取用户名。此外，我们使用了这样一个看起来奇怪的东西：

```bash
$(date +"%x %r %Z")
```

The characters "$( )" tell the shell, "substitute the results of the enclosed command," a technique known as *command substitution*. In our script, we want the shell to insert the results of the command `date +"%x %r %Z"` which outputs the current date and time. The `date` command has many features and formatting options. To look at them all, try this:

​	字符 "$( )" 告诉 shell，“替换封闭命令的结果”，这是一种称为*命令替换*的技术。在我们的脚本中，我们希望 shell 插入命令 `date +"%x %r %Z"` 的结果，该命令输出当前日期和时间。`date` 命令有许多功能和格式选项。要查看所有选项，请尝试执行以下命令：

```bash
[me@linuxbox me]$ date --help | less
```

Be aware that there is an older, alternate syntax for "$(command)" that uses the backtick character " ` ". This older form is compatible with the original Bourne shell (`sh`) but its use is discouraged in favor of the modern syntax. The bash shell fully supports scripts written for `sh`, so the following forms are equivalent:

​	请注意，有一种较旧的，替代 "$(command)" 语法，它使用反引号字符 " `"。这种较旧的形式与原始 Bourne shell (`sh`) 兼容，但建议使用现代语法而不是它。Bash shell 完全支持为 `sh` 编写的脚本，因此以下形式是等价的：

```bash
$(command)
`command`
```



## 将命令的结果赋给变量 Assigning a Command's Result to a Variable

We can also assign the results of a command to a variable:

​	我们还可以将命令的结果赋给变量：

```bash
right_now="$(date +"%x %r %Z")"
```

We can even nest the variables (place one inside another), like this:

​	我们甚至可以嵌套变量（将一个变量放在另一个变量内），如下所示：

```bash
right_now="$(date +"%x %r %Z")"
time_stamp="Updated on $right_now by $USER"
```

**An important safety tip:** when performing parameter expansions or command substitutions, it is good practice to surround them in double quotes to prevent unwanted word splitting in case the result of the expansion contains whitespace characters.

​	**一个重要的安全提示：**在进行参数展开或命令替换时，最好将其用双引号括起来，以防止展开的结果包含空格字符时发生意外的单词分割。

## 常量 Constants

As the name variable suggests, the content of a variable is subject to change. This means that it is expected that during the execution of our script, a variable may have its content modified by something the script does.

​	正如变量的名称所暗示的，变量的内容是可变的。这意味着在脚本执行期间，变量的内容可能会被脚本执行的某些操作修改。

On the other hand, there may be values that, once set, should never be changed. These are called *constants*. This is a common idea in programming. Most programming languages have special facilities to support values that are not allowed to change. Bash also has this facility but it is rarely used. Instead, if a value is intended to be a constant, it is given an uppercase name to remind the programmer that it should be considered a constant even if it's not being enforced.

​	另一方面，可能存在一些一旦设置就不应该更改的值。这些被称为*常量*。这是编程中的一个常见概念。大多数编程语言都有特殊的功能来支持不允许更改的值。Bash 也有这样的功能，但很少被使用。相反，如果一个值被认为是常量，它会被赋予一个大写的名称，以提醒程序员它应该被视为常量，即使它没有被强制执行。

Environment variables are usually considered constants since they are rarely changed. Like constants, environment variables are given uppercase names by convention. In the scripts that follow, we will use this convention - uppercase names for constants and lowercase names for variables.

​	环境变量通常被认为是常量，因为它们很少改变。与常量一样，按照约定，环境变量使用大写名称。在接下来的脚本中，我们将使用这个约定 - 常量使用大写名称，变量使用小写名称。

So, applying everything we know, our program looks like this:

​	因此，根据我们所知，我们的程序如下所示：

```bash
#!/bin/bash

# sysinfo_page - A script to produce an HTML file

title="System Information for $HOSTNAME"
RIGHT_NOW="$(date +"%x %r %Z")"
TIME_STAMP="Updated on $RIGHT_NOW by $USER"

cat <<- _EOF_
    <html>
    <head>
        <title>
        $title
        </title>
    </head>

    <body>
    <h1>$title</h1>
    <p>$TIME_STAMP</p>
    </body>
    </html>
_EOF_
```

