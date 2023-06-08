+++
title = "键盘输入和算术"
weight = 10
date = 2023-06-08T11:18:38+08:00
description = ""
isCJKLanguage = true
draft = false

+++

# 键盘输入和算术  Keyboard Input and Arithmetic

https://linuxcommand.org/lc3_wss0100.php

Up to now, our scripts have not been interactive. That is, they did not accept any input from the user. In this lesson, we will see how our scripts can ask questions, and get and use responses.

​	到目前为止，我们的脚本还没有交互功能。也就是说，它们没有接受用户的任何输入。在本课中，我们将看到如何让我们的脚本提出问题，并获取和使用回答。

## read

To get input from the keyboard, we use the `read` command. The `read` command takes input from the keyboard and assigns it to a variable. Here is an example:

​	要从键盘获取输入，我们使用 `read` 命令。`read` 命令从键盘获取输入并将其赋值给一个变量。以下是一个示例：

```bash
#!/bin/bash

echo -n "Enter some text > "
read text
echo "You entered: $text"
```

As we can see, we displayed a prompt on line 3. Note that "`-n`" given to the `echo` command causes it to keep the cursor on the same line; i.e., it does not output a linefeed at the end of the prompt.

​	正如我们所看到的，我们在第 3 行显示了一个提示符。注意，`echo` 命令后面的 "`-n`" 使其保持在同一行上；即不在提示符的末尾输出换行符。

Next, we invoke the `read` command with "`text`" as its argument. What this does is wait for the user to type something followed by the Enter key and then assign whatever was typed to the variable `text`.

​	接下来，我们使用 "`text`" 作为 `read` 命令的参数来调用它。这样做的作用是等待用户输入一些内容，然后按回车键，然后将输入的内容分配给变量 `text`。

Here is the script in action:

​	以下是脚本的实际运行情况：

```bash
[me@linuxbox me]$ read_demo.bash
Enter some text > this is some text
You entered: this is some text
```

If we don't give the `read` command the name of a variable to assign its input, it will use the environment variable `REPLY`.

​	如果我们没有给 `read` 命令指定要分配其输入的变量的名称，它将使用环境变量 `REPLY`。

The `read` command has several command line options. The three most interesting ones are `-p`, `-t` and `-s`.

​	`read` 命令有几个命令行选项。其中三个最有趣的选项是 `-p`、`-t` 和 `-s`。

The `-p` option allows us to specify a prompt to precede the user's input. This saves the extra step of using an `echo` to prompt the user. Here is the earlier example rewritten to use the `-p` option:

​	`-p` 选项允许我们指定一个提示符，在用户的输入之前显示。这样可以省去使用 `echo` 提示用户的额外步骤。以下是重写为使用 `-p` 选项的早期示例：

```bash
#!/bin/bash

read -p "Enter some text > " text
echo "You entered: $text"
```

The `-t` option followed by a number of seconds provides an automatic timeout for the `read` command. This means that the `read` command will give up after the specified number of seconds if no response has been received from the user. This option could be used in the case of a script that must continue (perhaps resorting to a default response) even if the user does not answer the prompts. Here is the `-t` option in action:

​	`-t` 选项后面跟一个秒数，为 `read` 命令提供了自动超时功能。这意味着如果在指定的秒数内没有从用户那里收到响应，`read` 命令将放弃等待。在脚本必须继续执行的情况下（可能会采用默认响应），即使用户没有回答提示，也可以使用此选项。以下是 `-t` 选项的示例：

```bash
#!/bin/bash

echo -n "Hurry up and type something! > "
if read -t 3 response; then
    echo "Great, you made it in time!"
else
    echo "Sorry, you are too slow!"
fi
```

The `-s` option causes the user's typing not to be displayed. This is useful when we are asking the user to type in a password or other confidential information.

​	`-s` 选项使用户的输入不显示在屏幕上。这在要求用户输入密码或其他机密信息时非常有用。

## 算术 Arithmetic

Since we are working on a computer, it is natural to expect that it can perform some simple arithmetic. The shell provides features for *integer arithmetic*.

​	由于我们在使用计算机，自然可以期望它能执行一些简单的算术运算。Shell 提供了*整数算术*的功能。

What's an integer? That means whole numbers like 1, 2, 458, -2859. It does not mean fractional numbers like 0.5, .333, or 3.1415. To deal with fractional numbers, there is a separate program called `bc` which provides an arbitrary precision calculator language. It can be used in shell scripts, but is beyond the scope of this tutorial.

​	什么是整数？那意味着像 1、2、458、-2859 这样的整数。它不包括像 0.5、.333 或 3.1415 这样的小数。为了处理小数，有一个名为 `bc` 的单独程序，它提供了一个任意精度的计算器语言。它可以在 shell 脚本中使用，但超出了本教程的范围。

Let's say we want to use the command line as a primitive calculator. We can do it like this:

​	假设我们想要将命令行用作基本计算器。我们可以这样做：

```bash
[me@linuxbox me]$ echo $((2+2))
```

When we surround an arithmetic expression with the double parentheses, the shell will perform arithmetic expansion.

​	当我们用双括号括起算术表达式时，Shell 将执行算术展开。

Notice that whitespace is ignored:

​	注意，空格被忽略：

```bash
[me@linuxbox me]$ echo $((2+2))
4
[me@linuxbox me]$ echo $(( 2+2 ))
4
[me@linuxbox me]$ echo $(( 2 + 2 ))
4
```

The shell can perform a variety of common (and not so common) arithmetic operations. Here is an example:

​	Shell 可以执行各种常见（和不常见）的算术运算。以下是一个示例：

```bash
#!/bin/bash

first_num=0
second_num=0

read -p "Enter the first number --> " first_num
read -p "Enter the second number -> " second_num

echo "first number + second number = $((first_num + second_num))"
echo "first number - second number = $((first_num - second_num))"
echo "first number * second number = $((first_num * second_num))"
echo "first number / second number = $((first_num / second_num))"
echo "first number % second number = $((first_num % second_num))"
echo "first number raised to the"
echo "power of the second number   = $((first_num ** second_num))"
```

Notice how the leading "`$`" is not needed to reference variables inside the arithmetic expression such as "`first_num + second_num`".

​	注意在算术表达式中不需要前导的 "`$`" 来引用变量，例如 "`first_num + second_num`"。

Try this program out and watch how it handles division (remember, this is integer division) and how it handles large numbers. Numbers that get too large *overflow* like the odometer in a car when it exceeds the number of miles it was designed to count. It starts over but first it goes through all the negative numbers because of how integers are represented in memory. Division by zero (which is mathematically invalid) does cause an error.

​	尝试运行此程序，观察它如何处理除法（记住，这是整数除法）以及如何处理大数。当数字变得太大时，它们会*溢出*，就像汽车上的里程表超过设计时的里程数时一样。它会重新开始，但首先会经过所有的负数，这是由于整数在内存中的表示方式。除零（在数学上是无效的）会导致错误。

The first four operations, addition, subtraction, multiplication and division, are easily recognized but the fifth one may be unfamiliar. The "`%`" symbol represents remainder (also known as *modulo*). This operation performs division but instead of returning a quotient like division, it returns the remainder. While this might not seem very useful, it does, in fact, provide great utility when writing programs. For example, when a remainder operation returns zero, it indicates that the first number is an exact multiple of the second. This can be very handy:

​	前四个操作，加法、减法、乘法和除法，很容易理解，但第五个可能不太熟悉。"`%`" 符号表示余数（也称为*模运算*）。此操作执行除法，但与除法返回商不同，它返回余数。虽然这可能看起来并不非常有用，但实际上在编写程序时提供了很大的实用性。例如，当余数操作返回零时，它表示第一个数字是第二个数字的精确倍数。这非常方便：

```bash
#!/bin/bash

number=0

read -p "Enter a number > " number

echo "Number is $number"
if [ $((number % 2)) -eq 0 ]; then
    echo "Number is even"
else
    echo "Number is odd"
fi 
```

Or, in this program that formats an arbitrary number of seconds into hours and minutes:

​	或者，在这个程序中，将任意秒数格式化为小时和分钟：

```bash
#!/bin/bash

seconds=0

read -p "Enter number of seconds > " seconds

hours=$((seconds / 3600))
seconds=$((seconds % 3600))
minutes=$((seconds / 60))
seconds=$((seconds % 60))

echo "$hours hour(s) $minutes minute(s) $seconds second(s)"
```

