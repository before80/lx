+++
title = "流程控制 - 第一部分"
weight = 8
date = 2023-06-08T11:17:36+08:00
description = ""
isCJKLanguage = true
draft = false

+++

# 流程控制 - 第一部分 Flow Control - Part 1

https://linuxcommand.org/lc3_wss0080.php

In this lesson, we will look at how to add intelligence to our scripts. So far, our project script has only consisted of a sequence of commands that starts at the first line and continues line by line until it reaches the end. Most programs do much more than this. They make decisions and perform different actions depending on conditions.

​	在这节课中，我们将学习如何在脚本中添加智能功能。到目前为止，我们的项目脚本只是由一系列命令组成，从第一行开始顺序执行，直到结束。大多数程序要做的事情比这要复杂得多。它们根据条件进行决策并执行不同的操作。

The shell provides several commands that we can use to control the flow of execution in our program. In this lesson, we will look at the following:

​	Shell 提供了几个命令，我们可以用它们来控制程序的执行流程。在本课中，我们将学习以下内容： 

- `if`
- `test`
- `exit`

## if

The first command we will look at is `if`. The `if` command is fairly simple on the surface; it makes a decision based on the *exit status* of a command. The `if` command's syntax looks like this:

​	我们首先学习的是 `if` 命令。`if` 命令在表面上很简单，它根据一个命令的 *退出状态* 做出决策。`if` 命令的语法如下：

```bash
if commands; then
    commands
[elif commands; then
    commands...]
[else
    commands]
fi
```

where *commands* is a list of commands. This is a little confusing at first glance. But before we can clear this up, we have to look at how the shell evaluates the success or failure of a command.

​	其中 *commands* 是一系列命令。乍一看可能有点困惑。但在我们澄清这一点之前，我们必须了解 Shell 如何评估命令的成功或失败。

## 退出状态 Exit Status

Commands (including the scripts and shell functions we write) issue a value to the system when they terminate, called an exit status. This value, which is an integer in the range of 0 to 255, indicates the success or failure of the command’s execution. By convention, a value of zero indicates success and any other value indicates failure. The shell provides a parameter that we can use to examine the exit status. Here we see it in action:

​	命令（包括我们编写的脚本和 Shell 函数）在终止时向系统发出一个值，称为退出状态。这个值是一个在 0 到 255 范围内的整数，表示命令执行的成功或失败。按照惯例，零表示成功，任何其他值表示失败。Shell 提供了一个参数，我们可以使用它来检查退出状态。下面是一个示例：

```bash
[me@linuxbox ~]$ ls -d /usr/bin
/usr/bin
[me@linuxbox ~]$ echo $?
0
[me@linuxbox ~]$ ls -d /bin/usr
ls: cannot access /bin/usr: No such file or directory
[me@linuxbox ~]$ echo $?
2
```

In this example, we execute the `ls` command twice. The first time, the command executes successfully. If we display the value of the parameter `$?`, we see that it is zero. We execute the ls command a second time, producing an error and examine the parameter `$?` again. This time it contains a 2, indicating that the command encountered an error. Some commands use different exit status values to provide diagnostics for errors, while many commands simply exit with a value of one when they fail. Man pages often include a section entitled “Exit Status,” describing what codes are used. However, a zero always indicates success.

​	在这个示例中，我们执行了两次 `ls` 命令。第一次，命令成功执行。如果我们显示参数 `$?` 的值，会发现它是零。我们第二次执行 `ls` 命令，产生一个错误，并再次检查参数 `$?`。这次它包含了 2，表示命令遇到了一个错误。有些命令使用不同的退出状态值来提供错误的诊断信息，而许多命令在失败时只是退出状态为 1。手册页面通常包含一个名为“Exit Status”的部分，描述了使用了哪些代码。然而，零总是表示成功。

The shell provides two extremely simple builtin commands that do nothing except terminate with either a zero or one exit status. The `true` command always executes successfully and the `false` command always executes unsuccessfully:

​	Shell 提供了两个非常简单的内置命令，它们除了以零或一的退出状态终止外什么都不做。`true` 命令始终成功执行，而 `false` 命令始终执行失败：

```bash
[me@linuxbox~]$ true
[me@linuxbox~]$ echo $?
0
[me@linuxbox~]$ false
[me@linuxbox~]$ echo $?
1
```

We can use these commands to see how the `if` statement works. What the `if` statement really does is evaluate the success or failure of commands:

​	我们可以使用这些命令来了解 `if` 语句的工作原理。`if` 语句的真正作用是评估命令的成功或失败：

```bash
[me@linuxbox ~]$ if true; then echo "It's true."; fi
It's true.
[me@linuxbox ~]$ if false; then echo "It's true."; fi
[me@linuxbox ~]$
```

The command `echo "It's true."` is executed when the command following `if` executes successfully, and is not executed when the command following `if` does not execute successfully.

​	当 `if` 后面的命令成功执行时，会执行 `echo "It's true."` 命令，当 `if` 后面的命令执行失败时，不会执行 `echo "It's true."` 命令。

## test

The `test` command is used most often with the `if` command to perform true/false decisions. The command is unusual in that it has two different syntactic forms:

​	`test` 命令最常与 `if` 命令一起使用以进行真/假判断。该命令有两种不同的语法形式：

```bash
# First form

test expression

# Second form

[ expression ]
```

The `test` command works simply. If the given expression is true, `test` exits with a status of zero; otherwise it exits with a status of 1.

​	`test` 命令的工作很简单。如果给定的表达式为真，则 `test` 以零状态退出；否则，它以 1 的状态退出。

The neat feature of `test` is the variety of expressions we can create. Here is an example:

​	`test` 的好处是我们可以创建多种表达式。以下是一个示例：

```bash
if [ -f .bash_profile ]; then
    echo "You have a .bash_profile. Things are fine."
else
    echo "Yikes! You have no .bash_profile!"
fi
```

In this example, we use the expression "`-f .bash_profile` ". This expression asks, "Is .bash_profile a file?" If the expression is true, then `test` exits with a zero (indicating true) and the `if` command executes the command(s) following the word `then`. If the expression is false, then `test` exits with a status of one and the `if` command executes the command(s) following the word `else`.

​	在这个示例中，我们使用表达式 "`-f .bash_profile`"。该表达式询问：“`.bash_profile` 是否是一个文件？”如果表达式为真，则 `test` 以零状态退出（表示为真），并且 `if` 命令执行紧随 `then` 之后的命令。如果表达式为假，则 `test` 以状态 1 退出，并且 `if` 命令执行紧随 `else` 之后的命令。

Here is a partial list of the conditions that `test` can evaluate. Since `test` is a shell builtin, use "`help test`" to see a complete list.

​	以下是 `test` 命令可以评估的一部分条件列表。由于 `test` 是一个内置命令，使用 "`help test`" 可以查看完整列表。

| **表达式 Expression**  | **描述 Description**                                         |
| :--------------------- | :----------------------------------------------------------- |
| -d *file*              | 如果 *file* 是一个目录，则为真。<br />True if *file* is a directory. |
| -e *file*              | 如果 *file* 存在，则为真。<br />True if *file* exists.       |
| -f *file*              | 如果 *file* 存在且是一个普通文件，则为真。<br />True if *file* exists and is a regular file. |
| -L *file*              | 如果 *file* 是一个符号链接，则为真。<br />True if *file* is a symbolic link. |
| -r *file*              | 如果 *file* 是一个你可读取的文件，则为真。<br />True if *file* is a file readable by you. |
| -w *file*              | 如果 *file* 是一个你可写入的文件，则为真。<br />True if *file* is a file writable by you. |
| -x *file*              | 如果 *file* 是一个你可执行的文件，则为真。<br />True if *file* is a file executable by you. |
| *file1* -nt *file2*    | 如果 *file1* 比（根据修改时间）*file2* 新，则为真。<br />True if *file1* is newer than (according to modification time) *file2*. |
| *file1* -ot *file2*    | 如果 *file1* 比 *file2* 旧，则为真。<br />True if *file1* is older than *file2*. |
| -z *string*            | 如果 *string* 为空，则为真。<br />True if *string* is empty. |
| -n *string*            | 如果 *string* 不为空，则为真。<br />True if *string* is not empty. |
| *string1* = *string2*  | 如果 *string1* 等于 *string2*，则为真。<br />True if *string1* equals *string2.* |
| *string1* != *string2* | 如果 *string1* 不等于 *string2*，则为真。<br />True if *string1* does not equal *string2.* |

Before we go on, We need to explain the rest of the example above, since it also reveals more important ideas.

​	在我们继续之前，我们需要解释上面示例的剩余部分，因为它还揭示了更重要的思想。

In the first line of the script, we see the `if` command followed by the `test` command, followed by a semicolon, and finally the word `then`. Most people choose to use the `[ *expression* ]` form of the `test` command since it's easier to read. Notice that the spaces required between the "`[`" and the beginning of the expression are required. Likewise, the space between the end of the expression and the trailing "`]`".

​	在脚本的第一行中，我们看到 `if` 命令后面跟着 `test` 命令，后面是一个分号，最后是单词 `then`。大多数人选择使用 `[ *expression* ]` 形式的 `test` 命令，因为它更容易阅读。请注意，在 "`[`" 与表达式开头之间需要的空格。同样，表达式的结尾和尾随的 "`]`" 之间的空格也是必需的。

The semicolon is a command separator. Using it allows us to put more than one command on a line. For example:

​	分号是一个命令分隔符。使用分号允许我们在一行上放置多个命令。例如：

```bash
[me@linuxbox me]$ clear; ls
```

will clear the screen and execute the ls command.

将清除屏幕并执行 ls 命令。

We use the semicolon as we did to allow us to put the word `then` on the same line as the `if` command, because it's easier to read that way.

​	我们像这样使用分号是为了让 `then` 这个词与 `if` 命令在同一行上，因为这样更容易阅读。

On the second line, there is our old friend `echo`. The only thing of note on this line is the indentation. Again for the benefit of readability, it is traditional to indent all blocks of conditional code; that is, any code that will only be executed if certain conditions are met. The shell does not require this; it is done to make the code easier to read.

​	在第二行中，我们看到了我们的老朋友 `echo`。这行中值得注意的是缩进。为了可读性，传统上对所有的条件代码块进行缩进，也就是只有在满足特定条件时才会执行的代码块。Shell 并不要求这样做，但这样做是为了让代码更容易阅读。

In other words, we could write the following and get the same results:

​	换句话说，我们可以编写以下代码并获得相同的结果：

```bash
# Preferred form

if [ -f .bash_profile ]; then
    echo "You have a .bash_profile. Things are fine."
else
    echo "Yikes! You have no .bash_profile!"
fi

# Another alternate form

if [ -f .bash_profile ]
then echo "You have a .bash_profile. Things are fine."
else echo "Yikes! You have no .bash_profile!"
fi
```



## exit

In order to be good script writers, we must set the exit status when our scripts finish. To do this, use the `exit` command. The `exit` command causes the script to terminate immediately and set the exit status to whatever value is given as an argument. For example:

​	为了成为良好的脚本编写者，我们必须在脚本完成时设置退出状态。要做到这一点，请使用 `exit` 命令。`exit` 命令会立即终止脚本，并将退出状态设置为作为参数给出的值。例如：

```bash
exit 0
```

exits our script and sets the exit status to 0 (success), whereas

退出我们的脚本，并将退出状态设置为 0（成功），而

```bash
exit 1
```

exits your script and sets the exit status to 1 (failure).

退出你的脚本，并将退出状态设置为 1（失败）。

## 测试是否为 Root 用户  Testing for Root

When we last left our script, we required that it be run with superuser privileges. This is because the `home_space` function needs to examine the size of each user's home directory, and only the superuser is allowed to do that.

​	当我们上次离开我们的脚本时，我们要求它在超级用户权限下运行。这是因为 `home_space` 函数需要检查每个用户的主目录大小，而只有超级用户才允许这样做。

But what happens if a regular user runs our script? It produces a lot of ugly error messages. What if we could put something in the script to stop it if a regular user attempts to run it?

​	但是如果普通用户运行我们的脚本会发生什么？它会产生很多丑陋的错误消息。如果我们能在脚本中添加一些内容，以便在普通用户尝试运行它时停止它会怎么样？

The `id` command can tell us who the current user is. When executed with the "-u" option, it prints the numeric user id of the current user.

​	`id` 命令可以告诉我们当前用户是谁。当使用 "-u" 选项执行时，它会打印当前用户的数字用户 ID。

```bash
[me@linuxbox me]$ id -u
501
[me@linuxbox me]$ sudo -i
Password for me:
[root@linuxbox ~]# id -u
0
[root@linuxbox ~]# exit
```

If the superuser executes `id -u`, the command will output "0." This fact can be the basis of our test:

​	如果超级用户执行 `id -u`，该命令将输出 "0"。这个事实可以成为我们的测试基础：

```bash
if [ "$(id -u)" = "0" ]; then
    echo "superuser"
fi
```

In this example, if the output of the command `id -u` is equal to the string "0", then print the string "superuser."

​	在这个示例中，如果 `id -u` 命令的输出等于字符串 "0"，那么打印字符串 "superuser"。

While this code will detect if the user is the superuser, it does not really solve the problem yet. We want to stop the script if the user is not the superuser, so we will code it like so:

​	虽然这段代码会检测用户是否是超级用户，但它还没有真正解决问题。我们想要在用户不是超级用户时停止脚本，所以我们将对其进行编码：

```bash
if [ "$(id -u)" != "0" ]; then
    echo "You must be the superuser to run this script" >&2
    exit 1
fi
```

With this code, if the output of the `id -u` command is not equal to "0", then the script prints a descriptive error message, exits, and sets the exit status to 1, indicating to the operating system that the script executed unsuccessfully.

​	使用这段代码，如果 `id -u` 命令的输出不等于 "0"，则脚本会打印一个描述性的错误消息，退出并将退出状态设置为 1，表示脚本执行失败，以通知操作系统。

Notice the "`>&2`" at the end of the `echo` command. This is another form of I/O direction. We will often see this in routines that display error messages. If this redirection were not done, the error message would go to standard output. With this redirection, the message is sent to *standard error*. Since we are executing our script and redirecting its standard output to a file, we want the error messages separated from the normal output.

​	请注意 `echo` 命令末尾的 "`>&2`"。这是另一种 I/O 重定向形式。我们经常在显示错误消息的程序中看到这种形式。如果没有进行此重定向，错误消息将被发送到标准输出。通过进行此重定向，消息将被发送到*标准错误*。由于我们正在执行脚本并将其标准输出重定向到文件，我们希望错误消息与正常输出分开。

We could put this routine near the beginning of our script so it has a chance to detect a possible error before things get under way, but in order to run this script as an ordinary user, we will use the same idea and modify the `home_space` function to test for proper privileges instead, like so:

​	我们可以将此代码放在脚本的开头附近，以便在开始之前有机会检测可能的错误，但为了以普通用户身份运行此脚本，我们将使用相同的思路，并修改 `home_space` 函数以测试适当的权限，如下所示：

```bash
function home_space {
    # Only the superuser can get this information

    if [ "$(id -u)" = "0" ]; then
        echo "<h2>Home directory space by user</h2>"
        echo "<pre>"
        echo "Bytes Directory"
            du -s /home/* | sort -nr
        echo "</pre>"
    fi

}   # end of home_space
```

This way, if an ordinary user runs the script, the troublesome code will be passed over, rather than executed and the problem will be solved.

​	这样，如果普通用户运行脚本，有问题的代码将被跳过，而不是执行，问题就得到解决了。

## 进一步阅读 Further Reading

- Chapter 27 in [*The Linux Command Line*](https://linuxcommand.org/tlcl.php) covers this topic in more detail including the `[[ ]]` construct, the modern replacement for the `test` command, and the `(( ))` construct for performing arithmetic truth tests.
- 《Linux 命令行》的第 27 章详细介绍了这个主题，包括 `[[ ]]` 结构，作为 `test` 命令的现代替代品，以及用于执行算术真值测试的 `(( ))` 结构。
- To learn more about stylistic conventions and best coding practices for bash scripts, see the [*Coding Standards*](https://linuxcommand.org/lc3_adv_standards.php) adventure.
- 要了解有关 bash 脚本的样式约定和最佳编码实践的更多信息，请参阅 [*Coding Standards*](https://linuxcommand.org/lc3_adv_standards.php) 探险。