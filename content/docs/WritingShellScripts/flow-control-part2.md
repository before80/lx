+++
title = "流程控制 - 第二部分"
weight = 11
date = 2023-06-08T11:17:43+08:00
description = ""
isCJKLanguage = true
draft = false

+++

# 流程控制 - 第二部分 Flow Control - Part 2

https://linuxcommand.org/lc3_wss0110.php

Hold on to your hats. This lesson is going to be a big one!

​	准备好了吗？这一课将是一大篇章！

## 更多的分支 More Branching

In the [previous lesson on flow control](https://linuxcommand.org/lc3_wss0080.php) we learned about the `if` command and how it is used to alter program flow based on a command's exit status. In programming terms, this type of program flow is called *branching* because it is like traversing a tree. We come to a fork in the tree and the evaluation of a condition determines which branch we take.

​	在[前一节的流程控制课程](https://linuxcommand.org/lc3_wss0080.php)中，我们学习了 `if` 命令以及如何根据命令的退出状态来改变程序流程。从编程的角度来说，这种类型的程序流程被称为*分支*，因为它类似于遍历树。我们来到树中的一个分叉，条件的评估决定了我们走哪条分支。

There is a second and more complex kind of branching called a *case*. A case is multiple-choice branch. Unlike the simple branch, where we take one of two possible paths, a case supports several possible outcomes based on the evaluation of a value.

​	还有一种更复杂的分支叫做*case*。Case 是多选分支。与简单的分支不同，我们可以基于某个值的评估来支持多种可能的结果。

We can construct this type of branch with multiple `if` statements. In the example below, we evaluate some input from the user:

​	我们可以使用多个 `if` 语句构建这种类型的分支。在下面的示例中，我们评估用户输入的内容：

```bash
#!/bin/bash

read -p "Enter a number between 1 and 3 inclusive > " character
if [ "$character" = "1" ]; then
    echo "You entered one."
elif [ "$character" = "2" ]; then
    echo "You entered two."
elif [ "$character" = "3" ]; then
    echo "You entered three."
else
    echo "You did not enter a number between 1 and 3."
fi
```

Not very pretty.

​	不是很漂亮。

Fortunately, the shell provides a more elegant solution to this problem. It provides a built-in command called `case`, which can be used to construct an equivalent program:

​	幸运的是，Shell 提供了一个更优雅的解决方案。它提供了一个内置命令叫做 `case`，可以用来构建等效的程序：

```bash
#!/bin/bash

read -p "Enter a number between 1 and 3 inclusive > " character
case $character in
    1 ) echo "You entered one."
        ;;
    2 ) echo "You entered two."
        ;;
    3 ) echo "You entered three."
        ;;
    * ) echo "You did not enter a number between 1 and 3."
esac
```

The `case` command has the following form:

​	`case` 命令的形式如下：

```bash
case word in
    patterns ) commands ;;
esac
```

`case` selectively executes statements if `word` matches a pattern. We can have any number of patterns and statements. Patterns can be literal text or wildcards. We can have multiple patterns separated by the "`|`" character. Here is a more advanced example to show how this works:

​	`case` 命令根据 `word` 是否匹配某个模式来选择性地执行语句。我们可以有任意数量的模式和语句。模式可以是文字字面值或通配符。我们可以用 "`|`" 字符分隔多个模式。下面是一个更高级的示例，展示了它是如何工作的：

```bash
#!/bin/bash

read -p "Type a digit or a letter > " character
case $character in
                                # Check for letters
    [[:lower:]] | [[:upper:]] ) echo "You typed the letter $character"
                                ;;

                                # Check for digits
    [0-9] )                     echo "You typed the digit $character"
                                ;;

                                # Check for anything else
    * )                         echo "You did not type a letter or a digit"
esac
```

Notice the special pattern "`*`". This pattern will match anything, so it is used to catch cases that did not match previous patterns. Inclusion of this pattern at the end is wise, as it can be used to detect invalid input.

​	注意特殊的模式 "`*`"。这个模式将匹配任何内容，因此用于捕捉未匹配前面模式的情况。在最后加入这个模式是明智的，因为它可以用来检测无效的输入。

## 循环 Loops

The final type of program flow control we will discuss is called *looping*. Looping is repeatedly executing a section of a program based on the exit status of a command. The shell provides three commands for looping: `while`, `until` and `for`. We are going to cover `while` and `until` in this lesson and `for` in a upcoming lesson.

​	我们将讨论的最后一种程序流程控制类型称为*循环*。循环是根据命令的退出状态重复执行程序的一部分。Shell 提供了三个用于循环的命令：`while`、`until` 和 `for`。我们将在本课程中讨论 `while` 和 `until`，而将 `for` 留在即将到来的课程中讲解。

The `while` command causes a block of code to be executed over and over, as long as the exit status of a specified command is true. Here is a simple example of a program that counts from zero to nine:

​	`while` 命令使得一段代码块根据指定命令的退出状态反复执行。以下是一个简单的示例，该程序从零计数到九：

```bash
#!/bin/bash

number=0
while [ "$number" -lt 10 ]; do
    echo "Number = $number"
    number=$((number + 1))
done
```

On line 3, we create a variable called `number` and initialize its value to 0. Next, we start the `while` loop. As we can see, we have specified a command that tests the value of `number`. In our example, we test to see if `number` has a value less than 10.

​	在第3行，我们创建一个名为 `number` 的变量，并将其初始值设置为0。接下来，我们开始 `while` 循环。正如我们所见，我们指定了一个命令来测试 `number` 的值。在我们的示例中，我们测试 `number` 是否小于10。

Notice the word `do` on line 4 and the word `done` on line 7. These enclose the block of code that will be repeated as long as the exit status remains zero.

​	注意第4行的 `do` 和第7行的 `done`。它们包围了将重复的代码块，只要退出状态保持为零，就会重复执行。

In most cases, the block of code that repeats must do something that will eventually change the exit status, otherwise we will have what is called an *endless loop*; that is, a loop that never ends.

​	在大多数情况下，重复执行的代码块必须执行一些最终会改变退出状态的操作，否则我们将会得到所谓的*无限循环*；也就是说，一个永远不会结束的循环。

In the example, the repeating block of code outputs the value of `number` (the `echo` command on line 5) and increments `number` by one on line 6. Each time the block of code is completed, the test command's exit status is evaluated again. After the tenth iteration of the loop, `number` has been incremented ten times and the `test` command will terminate with a non-zero exit status. At that point, the program flow resumes with the statement following the word `done`. Since `done` is the last line of our example, the program ends.

​	在这个示例中，重复执行的代码块输出 `number` 的值（第5行的 `echo` 命令），并在第6行将 `number` 增加一。每次完成代码块后，测试命令的退出状态会再次评估。在循环的第十次迭代之后，`number` 已经增加了十次，`test` 命令将以非零退出状态终止。此时，程序流程将恢复到单词 `done` 后面的语句。由于 `done` 是我们示例的最后一行，程序结束了。

The `until` command works exactly the same way, except the block of code is repeated as long as the specified command's exit status is false. In the example below, notice how the expression given to the `test` command has been changed from the `while` example to achieve the same result:

​	`until` 命令的工作方式完全相同，只是重复执行的代码块是在指定命令的退出状态为假时重复执行。在下面的示例中，注意与 `while` 示例相比，给 `test` 命令指定的表达式已经改变，但结果是相同的：

```bash
#!/bin/bash

number=0
until [ "$number" -ge 10 ]; do
    echo "Number = $number"
    number=$((number + 1))
done
```



## 构建菜单 Building a Menu

A common user interface for text-based programs is a *menu*. A menu is a list of choices from which the user can pick.

​	文本界面程序中常见的用户界面是*菜单*。菜单是一个列表，用户可以从中选择。

In the example below, we use our new knowledge of loops and cases to build a simple menu driven application:

​	在下面的示例中，我们利用循环和 `case` 的新知识构建一个简单的菜单驱动应用程序：

```bash
#!/bin/bash

selection=
until [ "$selection" = "0" ]; do
    echo "
    PROGRAM MENU
    1 - Display free disk space
    2 - Display free memory

    0 - exit program
"
    echo -n "Enter selection: "
    read selection
    echo ""
    case $selection in
        1 ) df ;;
        2 ) free ;;
        0 ) exit ;;
        * ) echo "Please enter 1, 2, or 0"
    esac
done
```

The purpose of the `until` loop in this program is to re-display the menu each time a selection has been completed. The loop will continue until selection is equal to 0, the "exit" choice. Notice how we defend against entries from the user that are not valid choices.

​	这个程序中的 `until` 循环的目的是在完成选择后重新显示菜单。循环将继续，直到选择等于0，即"退出"选项。注意我们如何防止用户输入无效的选择。

To make this program better looking when it runs, we can enhance it by adding a function that asks the user to press the Enter key after each selection has been completed, and clears the screen before the menu is displayed again. Here is the enhanced example:

​	为了使程序在运行时更美观，我们可以增强它，添加一个函数，在每次选择完成后要求用户按下回车键，并在显示菜单之前清屏。以下是增强后的示例：

```bash
#!/bin/bash

press_enter()
{
    echo -en "\nPress Enter to continue"
    read
    clear
}

selection=
until [ "$selection" = "0" ]; do
    echo "
    PROGRAM MENU
    1 - display free disk space
    2 - display free memory

    0 - exit program
"
    echo -n "Enter selection: "
    read selection
    echo ""
    case $selection in
        1 ) df ; press_enter ;;
        2 ) free ; press_enter ;;
        0 ) exit ;;
        * ) echo "Please enter 1, 2, or 0"; press_enter
    esac
done
```



## 当你的计算机卡住时... When your computer hangs...

We have all had the experience of an application *hanging*. Hanging is when a program suddenly seems to stop and become unresponsive. While you might think that the program has stopped, in most cases, the program is still running but its program logic is stuck in an endless loop.	

​	我们都曾经有过应用程序*卡住*的经历。卡住是指一个程序突然停止并变得无响应。虽然你可能认为程序已经停止了，但在大多数情况下，程序仍在运行，只是它的程序逻辑陷入了一个无限循环中。

Imagine this situation: you have an external device attached to your computer, such as a USB disk drive but you forgot to turn it on. You try and use the device but the application hangs instead. When this happens, you could picture the following dialog going on between the application and the interface for the device:	

​	想象一下这种情况：你的计算机连接着一个外部设备，比如一个USB磁盘驱动器，但你忘了将其打开。你尝试使用设备，但应用程序却卡住了。当这种情况发生时，你可以想象应用程序和设备接口之间发生了以下对话：

```
Application:    Are you ready?
Interface:  Device not ready.

Application:    Are you ready?
Interface:  Device not ready.

Application:    Are you ready?
Interface:  Device not ready.
```

and so on, forever.

以此类推，一直无限循环。

Well-written software tries to avoid this situation by instituting a *timeout*. This means that the loop is also counting the number of attempts or calculating the amount of time it has waited for something to happen. If the number of tries or the amount of time allowed is exceeded, the loop exits and the program generates an error and exits.

​	良好编写的软件会通过引入*超时*机制来避免这种情况。这意味着循环同时计算尝试次数或等待发生某事的时间。如果超过了允许的尝试次数或时间限制，循环将退出，程序生成一个错误并退出。