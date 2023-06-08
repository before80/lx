+++
title = "错误、信号和陷阱（噢，我的天！）- 第一部分"
weight = 14
date = 2023-06-08T11:19:49+08:00
description = ""
isCJKLanguage = true
draft = false

+++

# 错误、信号和陷阱（噢，我的天！）- 第一部分 Errors and Signals and Traps (Oh My!) - Part 1

https://linuxcommand.org/lc3_wss0140.php

In this lesson, we're going to look at handling errors during script execution.

​	在本课程中，我们将讨论处理脚本执行过程中的错误。

The difference between a poor program and a good one is often measured in terms of the program's *robustness*. That is, the program's ability to handle situations in which something goes wrong.

​	一个糟糕的程序和一个好的程序之间的区别通常是以程序的*鲁棒性*来衡量的。也就是说，程序处理出现问题的情况的能力。

## 退出状态 Exit Status

As we recall from previous lessons, every well-written program returns an exit status when it finishes. If a program finishes successfully, the exit status will be zero. If the exit status is anything other than zero, then the program failed in some way.

​	回顾一下之前的课程，每个编写良好的程序在完成时都会返回一个退出状态。如果程序成功完成，退出状态将为零。如果退出状态不是零，那么程序以某种方式失败了。

It is very important to check the exit status of programs we call in our scripts. It is also important that our scripts return a meaningful exit status when they finish. There was once a Unix system administrator who wrote a script for a production system containing the following 2 lines of code:

​	检查我们在脚本中调用的程序的退出状态非常重要。同样重要的是，我们的脚本在完成时返回一个有意义的退出状态。曾经有一个 Unix 系统管理员为一个生产系统编写了一个包含以下两行代码的脚本：

```bash
# Example of a really bad idea

cd "$some_directory"
rm *
```

Why is this such a bad way of doing it? It's not, if nothing goes wrong. The two lines change the working directory to the name contained in `$some_directory` and delete the files in that directory. That's the intended behavior. But what happens if the directory named in `$some_directory` doesn't exist? In that case, the `cd` command will fail and the script executes the `rm` command on the current working directory. Not the intended behavior!

​	为什么这样做是错误的？如果一切顺利的话，就不是错误。这两行代码将工作目录更改为`$some_directory`中包含的名称，并删除该目录中的文件。这是预期的行为。但是，如果`$some_directory`中指定的目录不存在会发生什么？在这种情况下，`cd`命令将失败，脚本会在当前工作目录上执行`rm`命令。这不是预期的行为！

By the way, the hapless system administrator's script suffered this very failure and it destroyed a large portion of an important production system. Don't let this happen to you!

​	顺便说一下，这个倒霉的系统管理员的脚本遭遇了这个错误，并摧毁了一个重要的生产系统的大部分内容。不要让这种情况发生在你身上！

The problem with the script was that it did not check the exit status of the `cd` command before proceeding with the `rm` command.

​	该脚本的问题在于它在继续执行`rm`命令之前没有检查`cd`命令的退出状态。

## 检查退出状态 Checking the Exit Status

There are several ways we can get and respond to the exit status of a program. First, we can examine the contents of the `$?` environment variable. `$?` will contain the exit status of the last command executed. We can see this work with the following:

​	我们有几种方法可以获取和响应程序的退出状态。首先，我们可以检查`$?`环境变量的内容。`$?`将包含上一个执行的命令的退出状态。我们可以通过以下方式查看它的工作原理：

```bash
[me@linuxbox]$  true; echo $?
0
[me@linuxbox]$ false; echo $?
1
```

The `true` and `false` commands are programs that do nothing except return an exit status of zero and one, respectively. Using them, we can see how the `$?` environment variable contains the exit status of the previous program.

​	`true`和`false`命令是什么都不做，只返回零和非零退出状态的程序。通过使用它们，我们可以看到`$?`环境变量包含了先前程序的退出状态。

So to check the exit status, we could write the script this way:

​	因此，要检查退出状态，我们可以这样编写脚本：

```bash
# Check the exit status

cd "$some_directory"
if [ "$?" = "0" ]; then
  rm *
else
  echo "Cannot change directory!" 1>&2
  exit 1
fi
```

In this version, we examine the exit status of the `cd` command and if it's not zero, we print an error message on standard error and terminate the script with an exit status of 1.

​	在这个版本中，我们检查`cd`命令的退出状态，如果不是零，我们在标准错误上打印出一个错误消息，并以退出状态1终止脚本。

While this is a working solution to the problem, there are more clever methods that will save us some typing. The next approach we can try is to use the `if` statement directly, since it evaluates the exit status of commands it is given.

​	虽然这是解决问题的一种方法，但还有更聪明的方法可以节省我们的打字。下一个我们可以尝试的方法是直接使用`if`语句，因为它评估给定的命令的退出状态。

Using `if`, we could write it this way:

​	使用`if`，我们可以这样编写脚本：

```bash
# A better way

if cd "$some_directory"; then
  rm ./*
else
  echo "Could not change directory! Aborting." 1>&2
  exit 1
fi
```

Here we check to see if the `cd` command is successful. Only then does `rm` get executed; otherwise an error message is output and the program exits with a code of 1, indicating that an error has occurred.

​	在这里，我们检查`cd`命令是否成功。只有在成功的情况下才会执行`rm`命令；否则，输出一个错误消息，并以代码1退出，表示发生了错误。

Notice too how we changed the target of the `rm` command from "*" to "./*". This is a safety precaution. The reason is a little subtle and has to do with the lax way Unix-like systems name files. Since it is possible to include almost any character in a file name, we must card against file names that begin with hyphens as thy might be interpreted as command options after the wildcard is expanded. For example, if there was a file named `-rf` in the directory, it might cause `rm` to do unpleasant things. It's a good idea to always include "./" ahead of leading asterisks in scripts.

​	还要注意，我们将`rm`命令的目标从`*`更改为`./`。这是一种安全措施。原因有点微妙，与类 Unix 系统命名文件的方式有关。由于文件名几乎可以包含任何字符，我们必须对以连字符开头的文件名进行保护，因为在通配符展开后，它们可能被解释为命令选项。例如，如果目录中有一个名为`-rf`的文件，它可能导致`rm`执行一些不好的操作。在脚本中，始终在前导星号之前包含`./`是一个好习惯。

## 错误退出函数 An Error Exit Function

Since we will be checking for errors often in our programs, it makes sense to write a function that will display error messages. This will save more typing and promote laziness.

​	由于我们经常会在程序中检查错误，因此编写一个显示错误消息的函数是有意义的。这样可以节省更多的打字并鼓励懒惰。

```bash
# An error exit function

error_exit()
{
  echo "$1" 1>&2
  exit 1
}

# Using error_exit

if cd "$some_directory"; then
  rm ./*
else
  error_exit "Cannot change directory! Aborting."
fi
```



## AND 和 OR 列表 AND and OR Lists

Finally, we can further simplify our script by using the AND and OR control operators. To explain how they work, here is a quote from the `bash` man page:

​	最后，我们可以通过使用 AND 和 OR 控制运算符来进一步简化脚本。为了解释它们的工作原理，这里是来自`bash`手册页的引用：

"The control operators && and || denote AND lists and OR lists, respectively. An AND list has the form

​	"控制运算符`&&`和`||`分别表示 AND 列表和 OR 列表。AND 列表的形式为

```bash
command1 && command2
```

`command2` is executed *if, and only if*, `command1` returns an exit status of zero.

当且仅当`command1`返回零的退出状态时，执行`command2`。

An OR list has the form

​	OR 列表的形式为

```bash
command1 || command2
```

`command2` is executed *if, and only if*, `command1` returns a non-zero exit status. The exit status of AND and OR lists is the exit status of the last command executed in the list."

当且仅当`command1`返回非零的退出状态时，执行`command2`。AND 列表和 OR 列表的退出状态是列表中最后一个执行的命令的退出状态。"

Again, we can use the `true` and `false` commands to see this work:

​	同样，我们可以使用`true`和`false`命令来查看这个工作方式：

```bash
[me@linuxbox]$ true || echo "echo executed"
[me@linuxbox]$ false || echo "echo executed"
echo executed
[me@linuxbox]$ true && echo "echo executed"
echo executed
[me@linuxbox]$ false && echo "echo executed"
[me@linuxbox]$
```

Using this technique, we can write an even simpler version:

​	使用这种技术，我们可以编写一个更简单的版本：

```bash
# Simplest of all

cd "$some_directory" || error_exit "Cannot change directory! Aborting"
rm *
```

If an exit is not required in case of error, then we can even do this:

​	如果在出现错误的情况下不需要退出，那么我们甚至可以这样做：

```bash
# Another way to do it if exiting is not desired

cd "$some_directory" && rm ./*
```

We need to point out that even with the defense against errors we have introduced in our example for the use of `cd`, this code is still vulnerable to a common programming error, namely, what happens if the name of the variable containing the name of the directory is misspelled? In that case, the shell will interpret the variable as empty and the `cd` succeed, but it will change directories to the user's home directory, so beware!

​	我们需要指出的是，即使在我们的示例中为了防止错误而引入了对`cd`的防御，该代码仍然容易受到一种常见的编程错误的攻击，即如果包含目录名称的变量的名称拼写错误会发生什么？在这种情况下，shell 将将变量解释为空，并且`cd`成功，但它会更改到用户的主目录，所以要小心！

## 改进错误退出函数 Improving the Error Exit Function

There are a number of improvements that we can make to the `error_exit` function. It is useful to include the name of the program in the error message to make clear where the error is coming from. This becomes more important as our programs get more complex and we start having scripts launching other scripts, etc. Also, note the inclusion of the `LINENO` environment variable which will help identify the exact line within a script where the error occurred.

​	对于`error_exit`函数，我们可以进行一些改进。在错误消息中包含程序的名称是很有用的，以清楚地表明错误的来源。随着我们的程序变得更复杂，我们开始启动其他脚本等等，这一点变得更加重要。还要注意包含`LINENO`环境变量，它将帮助识别发生错误的脚本中的确切行。

```bash
#!/bin/bash

# A slicker error handling routine

# I put a variable in my scripts named PROGNAME which
# holds the name of the program being run.  You can get this
# value from the first item on the command line ($0).

PROGNAME="$(basename $0)"

error_exit()
{

# ----------------------------------------------------------------
# Function for exit due to fatal program error
#   Accepts 1 argument:
#     string containing descriptive error message
# ----------------------------------------------------------------


  echo "${PROGNAME}: ${1:-"Unknown Error"}" 1>&2
  exit 1
}

# Example call of the error_exit function.  Note the inclusion
# of the LINENO environment variable.  It contains the current
# line number.

echo "Example of error with line number and message"
error_exit "$LINENO: An error has occurred."
```

The use of the curly braces within the `error_exit` function is an example of *parameter expansion*. We can surround a variable name with curly braces (as with `${PROGNAME}`) if we need to be sure it is separated from surrounding text. Some people just put them around every variable out of habit. That usage is simply a style thing. The second use, `${1:-"Unknown Error"}` means that if parameter 1 (`$1`) is undefined, substitute the string "Unknown Error" in its place. Using parameter expansion, it is possible to perform a number of useful string manipulations. More information about parameter expansion can be found in the `bash` man page under the topic "EXPANSIONS".

​	在`error_exit`函数中使用大括号是参数扩展的一个示例。如果我们需要确保变量与周围的文本分开，可以使用大括号将变量名称括起来（例如`${PROGNAME}`）。有些人习惯于在每个变量周围都加上大括号。这种用法只是一种风格问题。第二个用法`${1:-"Unknown Error"}`的意思是，如果参数1（`$1`）未定义，则用字符串"Unknown Error"替代它。使用参数扩展，可以进行许多有用的字符串操作。有关参数扩展的更多信息，请参阅`bash`手册页中的"EXPANSIONS"主题。