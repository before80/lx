+++
title = "远离麻烦"
weight = 9
date = 2023-06-08T11:18:02+08:00
description = ""
isCJKLanguage = true
draft = false

+++

# 远离麻烦 - Stay Out of Trouble

https://linuxcommand.org/lc3_wss0090.php

Now that our scripts are getting a little more complicated, Let's look at some common mistakes that we might run into. To do this, we'll create the following script called `trouble.bash`. Be sure to enter it exactly as written.

现在我们的脚本变得有点复杂了，让我们看看可能遇到的一些常见错误。为此，我们将创建以下名为 `trouble.bash` 的脚本。请确保按照原样输入。

```bash
#!/bin/bash

number=1

if [ $number = "1" ]; then
    echo "Number equals 1"
else
    echo "Number does not equal 1"
fi
```

When we run this script, it should output the line "Number equals 1" because, well, `number` equals 1. If we don't get the expected output, we need to check our typing; we've made a mistake.

​	当我们运行这个脚本时，它应该输出 "Number equals 1"，因为，嗯，`number` 等于 1。如果我们没有得到预期的输出，我们需要检查我们的输入，我们肯定犯了个错误。

## 空变量 Empty Variables

Let's edit the script to change line 3 from:

​	让我们编辑脚本，将第 3 行从：

```bash
number=1
```

to:

改为：

```bash
number=
```

and run the script again. This time we should get the following:

​	然后再次运行脚本。这次我们应该得到以下结果：

```bash
[me@linuxbox me]$ ./trouble.bash
/trouble.bash: [: =: unary operator expected.
Number does not equal 1
```

As we can see, `bash` displayed an error message when we ran the script. We might think that by removing the "1" on line 3 it created a syntax error on line 3, but it didn't. Let's look at the error message again:

​	如我们所见，当我们运行脚本时，`bash` 显示了一个错误消息。我们可能认为，通过在第 3 行删除 "1"，它在第 3 行创建了一个语法错误，但实际上并非如此。让我们再次查看错误消息：

```bash
./trouble.bash: [: =: unary operator expected
```

We can see that `./trouble.bash` is reporting the error and the error has to do with "`[`". Remember that "`[`" is an abbreviation for the `test` shell builtin. From this we can determine that the error is occurring on line 5 not line 3.

​	我们可以看到 `./trouble.bash` 报告了错误，并且该错误与 "`[`" 有关。请记住，"`[`" 是 `test` 内置命令的缩写。从这里我们可以确定错误发生在第 5 行而不是第 3 行。

First, to be clear, there is nothing wrong with line 3. `number=` is perfectly good syntax. We sometimes want to set a variable's value to nothing. We can confirm the validity of this by trying it on the command line:

​	首先，为了明确，第 3 行没有问题。`number=` 是完全正确的语法。有时我们想将变量的值设置为空。我们可以通过在命令行上尝试来确认其有效性：

```bash
[me@linuxbox me]$ number=
[me@linuxbox me]$
```

See, no error message. So what's wrong with line 5? It worked before.

​	看，没有错误消息。那么第 5 行有什么问题？它之前是有效的。

To understand this error, we have to see what the shell sees. Remember that the shell spends a lot of its life expanding text. In line 5, the shell expands the value of `number` where it sees `$number`. In our first try (when `number=1`), the shell substituted 1 for `$number` like so:

​	要理解这个错误，我们必须看到 shell 看到的内容。请记住，shell 在其生命周期中花费了很多时间来展开文本。在第 5 行，shell 在 `$number` 处展开了 `number` 的值。在我们第一次尝试（`number=1`）中，shell 将 1 替换为 `$number`，如下所示：

```bash
if [ 1 = "1" ]; then
```

However, when we set number to nothing (`number=`), the shell saw this after the expansion:

​	然而，当我们将 number 设置为空（`number=`）时，shell 在展开之后看到了这个：

```bash
if [ = "1" ]; then
```

which is an error. It also explains the rest of the error message we received. The "`=`" is a binary operator; that is, it expects two items to operate upon - one on each side. What the shell is trying to tell us is that there is only one item and there should be a unary operator (like "`!`") that only operates on a single item.

这是一个错误。这也解释了我们收到的错误消息的其他部分。"`=`" 是一个二元运算符；也就是说，它期望两个操作数 - 每个操作数在一边。Shell 试图告诉我们的是，只有一个操作数，并且应该有一个仅对单个操作数操作的一元运算符（如 "`!`"）。

To fix this problem, change line 5 to read:

​	要解决这个问题，将第 5 行改为以下内容：

```bash
if [ "$number" = "1" ]; then
```

Now when the shell performs the expansion it will see:

​	现在当 shell 执行展开时，将看到以下内容：

```bash
if [ "" = "1" ]; then
```

which correctly expresses our intent.

这样正确地表达了我们的意图。

This brings up two important things to remember when we are writing scripts. We need to consider what happens if a variable is set to equal nothing and we should always put double quotes around parameters that undergo expansion.

​	这带来了两个重要的事情，我们在编写脚本时需要记住。我们需要考虑如果一个变量被设置为空会发生什么，而且我们应该始终在经过展开的参数周围加上双引号。

## 缺少引号 Missing Quotes

Edit line 6 to remove the trailing quote from the end of the line:

​	编辑第 6 行，将行末的引号删除：

```bash
 echo "Number equals 1
```

and run the script again. We should get this:

然后再次运行脚本。我们应该得到以下结果：

```bash
[me@linuxbox me]$ ./trouble.bash
./trouble.bash: line 8:
unexpected EOF while looking for matching "
./trouble.bash: line 10 syntax error: unexpected end of file
```

Here we have another instance of a mistake in one line causing a problem later in the script. What happened in this case was that the shell kept looking for the closing quotation mark to determine where the end of the string is, but ran off the end of the file before it found it.

​	这里我们又遇到了一行中的错误导致脚本后面出现问题的情况。在这种情况下，发生的情况是 shell 一直在寻找结束引号以确定字符串的结尾位置，但在找到之前已经到达了文件末尾。

These errors can be a real pain to track down in a long script. This is one reason we should test our scripts frequently while we are writing so there is less new code to test. Also, using a text editor with syntax highlighting makes these bugs easier to find.

​	在一个很长的脚本中追踪这些错误可能非常困难和令人沮丧。这就是为什么在编写过程中我们应该经常测试我们的脚本，这样就会有较少的新代码需要测试。此外，使用带有语法高亮的文本编辑器可以更容易地找到这些错误。

## 隔离问题 Isolating Problems

Finding bugs in scripts can sometimes be very difficult and frustrating. Here are a couple of techniques that are useful:

​	在脚本中找到错误有时可能非常困难和令人沮丧。以下是两种有用的技巧：

**Isolate blocks of code by "commenting them out."** This trick involves putting comment characters at the beginning of lines of code to stop the shell from reading them. We can do this to a block of code to see if a particular problem goes away. By doing this, we can isolate which part of a program is causing (or not causing) a problem.

​	**通过"注释掉"来隔离代码块。** 这个技巧涉及将注释字符放在代码行的开头，以阻止 shell 读取它们。我们可以这样做来对一块代码进行注释，以查看特定的问题是否消失。通过这样做，我们可以确定程序的哪一部分导致（或不导致）问题。

For example, when we were looking for our missing quotation we could have done this:

​	例如，在查找缺少引号时，我们可以这样做：

```bash
#!/bin/bash

number=1

if [ $number = "1" ]; then
    echo "Number equals 1
#else
#   echo "Number does not equal 1"
fi
```

By commenting out the `else` clause and running the script, we could show that the problem was not in the `else` clause even though the error message suggested that it was.

​	通过注释掉 `else` 语句并运行脚本，我们可以证明问题不在 `else` 语句中，即使错误消息暗示问题在其中。

**Use echo commands to verify assumptions.** As we gain experience tracking down bugs, we will discover that bugs are often not where we first expect to find them. A common problem will be that we will make a false assumption about the performance of our program. A problem will develop at a certain point in the program and we assume the problem is there. This is often incorrect. To combat this, we can place `echo` commands in the code while we are debugging, to produce messages that confirm the program is doing what is expected. There are two kinds of messages that we can insert.

​	**使用 echo 命令验证假设。** 随着我们在调试中积累经验，我们会发现错误通常不在我们最初期望的地方。一个常见的问题是我们对程序的性能做出了错误的假设。问题将在程序的某个特定点出现，并且我们会认为问题就在那里。这通常是不正确的。为了解决这个问题，我们可以在调试过程中在代码中插入 `echo` 命令，以生成确认程序按预期运行的消息。我们可以插入两种类型的消息。

The first type simply announces that we have reached a certain point in the program. We saw this in our earlier discussion on stubbing. It is useful to know that program flow is happening the way we expect.

​	第一种类型只是宣布我们已经到达程序的某个特定点。我们在之前讨论的占位符中看到过这一点。了解程序流程是否按我们的预期进行非常有用。

The second type displays the value of a variable (or variables) used in a calculation or test. We will often find that a portion of a program will fail because something that we assumed was correct earlier in the program is, in fact, incorrect and is causing our program to fail later on.

​	第二种类型显示在计算或测试中使用的变量（或变量）的值。我们经常发现，程序的一部分将因为我们在程序早期假设正确的东西实际上是不正确的而失败，并且导致我们的程序在后面失败。

## 观察脚本运行 Watching Our Script Run

It is possible to have `bash` show us what it is doing when we run our script. To do this, add a "`-x`" to the first line of the script, like this:

​	在运行脚本时，可以让 `bash` 显示它在做什么。要做到这一点，将 "`-x`" 添加到脚本的第一行，如下所示：

```bash
#!/bin/bash -x
```

Now, when we run the script, bash will display each line (with expansions performed) as it executes it. This technique is called *tracing*. Here is what it looks like:

​	现在，当我们运行脚本时，bash 将显示每行执行时的文本（包括展开）。这个技术被称为*跟踪*。下面是它的样子：

```bash
[me@linuxbox me]$ ./trouble.bash
+ number=1
+ '[' 1 = 1 ']'
+ echo 'Number equals 1'
Number equals 1
```

Alternately, we can use the `set` command within the script to turn tracing on and off. Use `set -x` to turn tracing on and `set +x` to turn tracing off. For example.:

​	或者，我们可以在脚本中使用 `set` 命令来打开和关闭跟踪。使用 `set -x` 打开跟踪，使用 `set +x` 关闭跟踪。例如：

```bash
#!/bin/bash

number=1

set -x
if [ $number = "1" ]; then
    echo "Number equals 1"
else
    echo "Number does not equal 1"
fi
set +x
```

