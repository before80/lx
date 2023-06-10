+++
title = "扩展"
date = 2023-06-08T10:56:08+08:00
weight = 8
description = ""
isCJKLanguage = true
draft = false

+++

# 扩展 Expansion 

https://linuxcommand.org/lc3_lts0080.php

Each time we type a command line and press the enter key, bash performs several processes upon the text before it carries out our command. We have seen a couple of cases of how a simple character sequence, for example "`*`", can have a lot of meaning to the shell. The process that makes this happen is called *expansion*. With expansion, we type something and it is expanded into something else before the shell acts upon it. To demonstrate what we mean by this, let's take a look at the `echo` command. `echo` is a shell builtin that performs a very simple task. It prints out its text arguments on standard output:

​	每次我们在命令行中输入命令并按下回车键时，bash在执行我们的命令之前会对文本进行几个处理过程。我们已经看到了一些简单字符序列（例如"`*`"）对于shell来说具有很多意义的情况。使这种情况发生的过程被称为*扩展*。通过扩展，我们在键入某些内容时，shell会在对其执行操作之前将其扩展为其他内容。为了演示我们所说的，让我们来看一下`echo`命令。`echo`是一个shell内置命令，执行一个非常简单的任务，即在标准输出上打印出其文本参数：

```bash
[me@linuxbox me]$ echo this is a test
this is a test
```

That's pretty straightforward. Any argument passed to `echo` gets displayed. Let's try another example:

​	这很简单明了。任何传递给`echo`的参数都会被显示出来。让我们尝试另一个示例：

```bash
[me@linuxbox me]$ echo *
Desktop Documents ls-output.txt Music Pictures Public Templates Videos
```

So what just happened? Why didn't `echo` print "`*`"? As we recall from our work with wildcards, the "`*`"character means match any characters in a filename, but what we didn't see in our original discussion was how the shell does that. The simple answer is that the shell expands the "`*`" into something else (in this instance, the names of the files in the current working directory) before the `echo` command is executed. When the enter key is pressed, the shell automatically expands any qualifying characters on the command line before the command is carried out, so the `echo` command never saw the "`*`", only its expanded result. Knowing this, we can see that `echo` behaved as expected.

​	发生了什么？为什么`echo`没有打印出"`*`"？从我们之前使用通配符的工作中，我们记得"`*`"字符表示匹配文件名中的任意字符，但是我们在原始讨论中没有看到shell是如何实现的。简单的答案是，shell在执行`echo`命令之前将"`*`"扩展为其他内容（在这种情况下是当前工作目录中文件的名称）。当按下回车键时，shell会自动在执行命令之前展开命令行上的任何限定字符，因此`echo`命令从未看到"`*`"，只看到其扩展后的结果。了解这一点，我们可以看到`echo`的行为符合预期。

## 路径名扩展 Pathname Expansion

The mechanism by which wildcards work is called *pathname expansion*. If we try some of the techniques that we employed in our earlier lessons, we will see that they are really expansions. Given a home directory that looks like this:

​	通配符起作用的机制被称为*路径名扩展*。如果我们尝试一些我们之前在课程中使用的技术，我们会发现它们实际上是扩展。给定一个如下所示的主目录：

```bash
[me@linuxbox me]$ ls
Desktop
ls-output.txt
Documents Music
Pictures
Public
Templates
Videos
```

we could carry out the following expansions:

​	我们可以进行以下扩展：

```bash
[me@linuxbox me]$ echo D*
Desktop Documents
```

and:

以及：

```bash
[me@linuxbox me]$ echo *s
Documents Pictures Templates Videos
```

or even:

甚至是：

```bash
[me@linuxbox me]$ echo [[:upper:]]*
Desktop Documents Music Pictures Public Templates Videos
```

and looking beyond our home directory:

并且可以查看超出主目录的内容：

```bash
[me@linuxbox me]$ echo /usr/*/share
/usr/kerberos/share /usr/local/share
```



## 波浪号扩展 Tilde Expansion

As we recall from our introduction to the `cd` command, the tilde character ("`~`") has a special meaning. When used at the beginning of a word, it expands into the name of the home directory of the named user, or if no user is named, the home directory of the current user:

​	正如我们在介绍`cd`命令时所记得的，波浪号（"`~`"）具有特殊的含义。当在单词的开头使用时，它会扩展为命名用户的主目录，如果没有指定用户，则扩展为当前用户的主目录：

```bash
[me@linuxbox me]$ echo ~
/home/me
```

If user "foo" has an account, then:

​	如果用户"foo"有一个帐户，则：

```bash
[me@linuxbox me]$ echo ~foo
/home/foo
```



## 算术扩展 Arithmetic Expansion

The shell allows arithmetic to be performed by expansion. This allow us to use the shell prompt as a calculator:

​	Shell允许通过扩展执行算术运算。这使我们可以将Shell提示符用作计算器：

```bash
[me@linuxbox me]$ echo $((2 + 2))
4
```

Arithmetic expansion uses the form:

​	算术扩展使用以下形式：

```bash
$((expression))
```

where expression is an arithmetic expression consisting of values and arithmetic operators.

其中，表达式是由值和算术运算符组成的算术表达式。

Arithmetic expansion only supports integers (whole numbers, no decimals), but can perform quite a number of different operations.

​	算术扩展仅支持整数（无小数），但可以执行许多不同的操作。

Spaces are not significant in arithmetic expressions and expressions may be nested. For example, to multiply five squared by three:

​	在算术表达式中，空格不重要，表达式可以嵌套。例如，要将五的平方乘以三：

```bash
[me@linuxbox me]$ echo $(($((5**2)) * 3))
75
```

Single parentheses may be used to group multiple subexpressions. With this technique, we can rewrite the example above and get the same result using a single expansion instead of two:

​	可以使用单括号来分组多个子表达式。使用这种技术，我们可以重写上面的例子，并使用单个扩展而不是两个来获得相同的结果：

```bash
[me@linuxbox me]$ echo $(((5**2) * 3))
75
```

Here is an example using the division and remainder operators. Notice the effect of integer division:

​	下面是一个使用除法和取余运算符的示例。注意整数除法的效果：

```bash
[me@linuxbox me]$ echo Five divided by two equals $((5/2))
Five divided by two equals 2
[me@linuxbox me]$ echo with $((5%2)) left over.
with 1 left over.
```



## 大括号扩展 Brace Expansion

Perhaps the strangest expansion is called *brace expansion*. With it, we can create multiple text strings from a pattern containing braces. Here's an example:

​	也许最奇怪的扩展被称为*大括号扩展*。使用它，我们可以从包含大括号的模式创建多个文本字符串。以下是一个示例：

```bash
[me@linuxbox me]$ echo Front-{A,B,C}-Back
Front-A-Back Front-B-Back Front-C-Back
```

Patterns to be brace expanded may contain a leading portion called a *preamble* and a trailing portion called a *postscript*. The brace expression itself may contain either a comma-separated list of strings, or a range of integers or single characters. The pattern may not contain embedded whitespace. Here is an example using a range of integers:

​	要扩展的模式可以包含称为*preamble*的前导部分和称为*postscript*的尾随部分。大括号表达式本身可以包含逗号分隔的字符串列表，或者是整数或单个字符的范围。模式不得包含嵌入的空格。以下是使用整数范围的示例：

```bash
[me@linuxbox me]$ echo Number_{1..5}
Number_1 Number_2 Number_3 Number_4 Number_5
```

A range of letters in reverse order:

​	以相反顺序的字母范围：

```bash
[me@linuxbox me]$ echo {Z..A}
Z Y X W V U T S R Q P O N M L K J I H G F E D C B A
```

Brace expansions may be nested:

​	大括号扩展可以嵌套：

```bash
[me@linuxbox me]$ echo a{A{1,2},B{3,4}}b
aA1b aA2b aB3b aB4b
```

So what is this good for? The most common application is to make lists of files or directories to be created. For example, if we were a photographer and had a large collection of images we wanted to organize into years and months, the first thing we might do is create a series of directories named in numeric "Year-Month" format. This way, the directory names will sort in chronological order. we could type out a complete list of directories, but that's a lot of work and it's error-prone too. Instead, we could do this:

​	那么这有什么好处？最常见的应用是创建要创建的文件或目录的列表。例如，如果我们是摄影师，有很多图像要按年份和月份组织，我们可能首先要做的是创建以数字"年份-月份"格式命名的一系列目录。这样，目录名称将按照时间顺序排序。我们可以输入完整的目录列表，但那是很麻烦的工作，而且容易出错。相反，我们可以这样做：

```bash
[me@linuxbox me]$ mkdir Photos
[me@linuxbox me]$ cd Photos
[me@linuxbox Photos]$ mkdir {2017..2019}-{01..12}
[me@linuxbox Photos]$ ls
2017-01 2017-07 2018-01 2018-07 2019-01 2019-07
2017-02 2017-08 2018-02 2018-08 2019-02 2019-08
2017-03 2017-09 2018-03 2018-09 2019-03 2019-09
2017-04 2017-10 2018-04 2018-10 2019-04 2019-10
2017-05 2017-11 2018-05 2018-11 2019-05 2019-11
2017-06 2017-12 2018-06 2018-12 2019-06 2019-12
```

Pretty slick!

​	很漂亮！

## 参数扩展 Parameter Expansion

We're only going to touch briefly on *parameter expansion* in this lesson, but we'll be covering it more later. It's a feature that is more useful in shell scripts than directly on the command line. Many of its capabilities have to do with the system's ability to store small chunks of data and to give each chunk a name. Many such chunks, more properly called *variables*, are available for our examination. For example, the variable named "USER" contains our user name. To invoke parameter expansion and reveal the contents of USER we would do this:

​	在本课程中，我们只会简要涉及*参数扩展*，但我们将在后面对其进行更详细的介绍。这是一个在Shell脚本中比在命令行直接使用更有用的功能。它的许多功能与系统存储小数据块和为每个块命名有关。我们可以检查许多这样的块，更准确地称为*变量*。例如，名为"USER"的变量包含我们的用户名。要调用参数扩展并显示USER的内容，我们可以这样做：

```bash
[me@linuxbox me]$ echo $USER
me
```

To see a list of available variables, try this:

​	要查看可用变量的列表，请尝试以下命令：

```bash
[me@linuxbox me]$ printenv | less
```

With other types of expansion, if we mistype a pattern, the expansion will not take place and the echo command will simply display the mistyped pattern. With parameter expansion, if we misspell the name of a variable, the expansion will still take place, but will result in an empty string:

​	与其他类型的扩展不同，如果我们拼写错误的模式，扩展将不会发生，echo命令将只显示错误拼写的模式。但是，对于参数扩展，如果我们拼写变量名称错误，扩展仍将发生，但结果将为空字符串：

```bash
[me@linuxbox me]$ echo $SUER
[me@linuxbox ~]$
```



## 命令替换 Command Substitution

*Command substitution* allows us to use the output of a command as an expansion:

​	*命令替换* 允许我们将命令的输出作为扩展使用：

```bash
[me@linuxbox me]$ echo $(ls)
Desktop Documents ls-output.txt Music Pictures Public Templates Videos
```

A clever one goes something like this:

​	一个巧妙的例子如下：

```bash
[me@linuxbox me]$ ls -l $(which cp)
-rwxr-xr-x 1 root root 71516 2007-12-05 08:58 /bin/cp
```

Here we passed the results of `which cp` as an argument to the `ls` command, thereby getting the listing of of the `cp` program without having to know its full pathname. We are not limited to just simple commands. Entire pipelines can be used (only partial output shown):

​	在这里，我们将 `which cp` 的结果作为 `ls` 命令的参数传递，从而获取 `cp` 程序的列表，而无需知道其完整路径名。我们不仅仅限于简单的命令，还可以使用整个流水线（仅显示部分输出）：

```bash
[me@linuxbox me]$ file $(ls /usr/bin/* | grep bin/zip)
/usr/bin/bunzip2:
/usr/bin/zip:      ELF 32-bit LSB executable, Intel 80386, version 1 
(SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.15, stripped
/usr/bin/zipcloak: ELF 32-bit LSB executable, Intel 80386, version 1
(SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.15, stripped
/usr/bin/zipgrep:  POSIX shell script text executable
/usr/bin/zipinfo:  ELF 32-bit LSB executable, Intel 80386, version 1
(SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.15, stripped
/usr/bin/zipnote:  ELF 32-bit LSB executable, Intel 80386, version 1
(SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.15, stripped
/usr/bin/zipsplit: ELF 32-bit LSB executable, Intel 80386, version 1
(SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.15, stripped
```

In this example, the results of the pipeline became the argument list of the file command. There is an alternate syntax for command substitution in older shell programs which is also supported in `bash`. It uses back-quotes instead of the dollar sign and parentheses:

​	在此示例中，流水线的结果成为 `file` 命令的参数列表。在旧的 shell 程序中，还有一种替代的命令替换语法，也在 `bash` 中支持。它使用反引号而不是美元符号和括号：

```bash
[me@linuxbox me]$ ls -l `which cp`
-rwxr-xr-x 1 root root 71516 2007-12-05 08:58 /bin/cp
```



## 引号 Quoting

Now that we've seen how many ways the shell can perform expansions, it's time to learn how we can control it. Take for example:

​	现在我们已经了解了 shell 执行扩展的多种方式，是时候学习如何控制它了。例如：

```bash
[me@linuxbox me]$ echo this is a     test
this is a test
```

or:

或者：

```bash
[me@linuxbox me]$ [me@linuxbox ~]$ echo The total is $100.00
The total is 00.00
```

In the first example, word-splitting by the shell removed extra whitespace from the echo command's list of arguments. In the second example, parameter expansion substituted an empty string for the value of "`$1`" because it was an undefined variable. The shell provides a mechanism called *quoting* to selectively suppress unwanted expansions.

​	在第一个示例中，Shell 的词分割功能删除了 echo 命令参数列表中的额外空格。在第二个示例中，参数扩展将一个空字符串替换为"`$1`"的值，因为它是一个未定义的变量。Shell 提供了一种称为*引号*的机制，可以选择性地抑制不需要的扩展。

## 双引号 Double Quotes

The first type of quoting we will look at is double quotes. If we place text inside double quotes, all the special characters used by the shell lose their special meaning and are treated as ordinary characters. The exceptions are "`$`", "`\`" (backslash), and "\`" (back- quote). This means that word-splitting, pathname expansion, tilde expansion, and brace expansion are suppressed, but parameter expansion, arithmetic expansion, and command substitution are still carried out. Using double quotes, we can cope with filenames containing embedded spaces. Imagine we were the unfortunate victim of a file called `two words.txt`. If we tried to use this on the command line, word-splitting would cause this to be treated as two separate arguments rather than the desired single argument:

​	我们将首先看一下的引号类型是双引号。如果我们将文本放在双引号内，Shell 中使用的所有特殊字符都失去了它们的特殊含义，被视为普通字符。例外的是 "`$`"、"`\`"（反斜杠）和 "\`"（反引号）。这意味着词分割、路径名扩展、波浪线扩展和大括号扩展被抑制，但参数扩展、算术扩展和命令替换仍然会进行。使用双引号，我们可以处理包含空格的文件名。假设我们是一个不幸的`two words.txt`文件的受害者。如果我们尝试在命令行上使用它，词分割将使其被视为两个独立的参数，而不是所需的单个参数：

```bash
[me@linuxbox me]$ ls -l two words.txt
ls: cannot access two: No such file or directory
ls: cannot access words.txt: No such file or directory
```

By using double quotes, we can stop the word-splitting and get the desired result; further, we can even repair the damage:

​	通过使用双引号，我们可以阻止词分割并获得所需的结果；此外，我们甚至可以修复损坏：

```bash
[me@linuxbox me]$ ls -l "two words.txt"
-rw-rw-r-- 1 me me 18 2020-02-20 13:03 two words.txt
[me@linuxbox me]$ mv "two words.txt" two_words.txt
```

There! Now we don't have to keep typing those pesky double quotes. Remember, parameter expansion, arithmetic expansion, and command substitution still take place within double quotes:

​	现在！我们不再需要不断输入那些烦人的双引号。请记住，参数扩展、算术扩展和命令替换仍然会在双引号内进行：

```bash
[me@linuxbox me]$ echo "$USER $((2+2)) $(cal)"
me 4
February 2020
Su Mo Tu We Th Fr Sa
                1  2
 3  4  5  6  7  8  9
10 11 12 13 14 15 16
17 18 19 20 21 22 23
24 25 26 27 28 29
```

We should take a moment to look at the effect of double quotes on command substitution. First let's look a little deeper at how word splitting works. In our earlier example, we saw how word-splitting appears to remove extra spaces in our text:

​	我们应该花点时间来看一下双引号对命令替换的影响。首先，让我们更深入地了解词分割是如何工作的。在我们之前的示例中，我们看到词分割似乎删除了文本中的额外空格：

```bash
[me@linuxbox me]$ echo this is a     test
this is a test
```

By default, word-splitting looks for the presence of spaces, tabs, and newlines (linefeed characters) and treats them as delimiters between words. This means that unquoted spaces, tabs, and newlines are not considered to be part of the text. They only serve as separators. Since they separate the words into different arguments, our example command line contains a command followed by four distinct arguments. If we add double quotes:

​	默认情况下，词分割会查找空格、制表符和换行符（换行字符），并将它们视为单词之间的分隔符。这意味着未加引号的空格、制表符和换行符不被视为文本的一部分。它们只是分隔符。由于它们将单词分隔为不同的参数，我们的示例命令行包含一个命令和四个不同的参数。如果我们添加双引号：

```bash
[me@linuxbox me]$ echo "this is a     test"
this is a     test
```

word-splitting is suppressed and the embedded spaces are not treated as delimiters, rather they become part of the argument. Once the double quotes are added, our command line contains a command followed by a single argument. The fact that newlines are considered delimiters by the word-splitting mechanism causes an interesting, albeit subtle, effect on command substitution. Consider the following:

​	词分割被抑制，嵌入的空格不再被视为分隔符，而是成为参数的一部分。一旦添加了双引号，我们的命令行包含一个命令和一个单独的参数。换行符被词分割机制视为分隔符，对命令替换产生了一个有趣但微妙的影响。考虑以下示例：

```bash
[me@linuxbox me]$ echo $(cal)
February 2020 Su Mo Tu We Th Fr Sa 1 2 3 4 5 6 7 8 9 10 11 12 13 14
15 16 17 18 19 20 21 22 23 24 25 26 27 28 29
[me@linuxbox me]$  echo "$(cal)"
February 2020
Su Mo Tu We Th Fr Sa
                1  2
 3  4  5  6  7  8  9
10 11 12 13 14 15 16
17 18 19 20 21 22 23
24 25 26 27 28 29
```

In the first instance, the unquoted command substitution resulted in a command line containing thirty-eight arguments. In the second, a command line with one argument that includes the embedded spaces and newlines.

​	在第一个示例中，未加引号的命令替换导致一个包含三十八个参数的命令行。在第二个示例中，命令行只有一个参数，其中包含嵌入的空格和换行符。

## 单引号 Single Quotes

When we need to suppress all expansions, we use single quotes. Here is a comparison of unquoted, double quotes, and single quotes:

​	当我们需要抑制所有扩展时，我们使用单引号。以下是未加引号、双引号和单引号的比较：

```bash
[me@linuxbox me]$ echo text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER
text /home/me/ls-output.txt a b foo 4 me
[me@linuxbox me]$ echo "text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER"
text ~/*.txt {a,b} foo 4 me
[me@linuxbox me]$ echo 'text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER'
text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER
```

As we can see, with each succeeding level of quoting, more and more of the expansions are suppressed.

​	正如我们所看到的，随着引号层次的增加，越来越多的扩展被抑制。

## 转义字符 Escaping Characters

Sometimes we only want to quote a single character. To do this, we can precede a character with a backslash, which in this context is called the *escape character*. Often this is done inside double quotes to selectively prevent an expansion:

​	有时我们只想引用一个单个字符。为此，我们可以在字符前加上反斜杠，这在这个上下文中称为*转义字符*。通常，这是在双引号内部选择性地阻止扩展的方法：

```bash
[me@linuxbox me]$ echo "The balance for user $USER is: \$5.00"
The balance for user me is: $5.00
```

It is also common to use escaping to eliminate the special meaning of a character in a filename. For example, it is possible to use characters in filenames that normally have special meaning to the shell. These would include "`$`", "`!`", "`&`", " ", and others. To include a special character in a filename we can to this:

​	在文件名中，使用转义通常是为了消除字符的特殊含义。例如，可以在文件名中使用通常对shell具有特殊含义的字符，包括"`$`"、"`!`"、"`&`"、" "和其他字符。要在文件名中包含特殊字符，我们可以这样做：

```bash
[me@linuxbox me]$ mv bad\&filename good_filename
```

To allow a backslash character to appear, escape it by typing "`\`". Note that within single quotes, the backslash loses its special meaning and is treated as an ordinary character.

​	要允许反斜杠字符出现，通过键入"`\`"来转义它。请注意，在单引号内部，反斜杠失去了其特殊含义，被视为普通字符。

## 更多反斜杠技巧 More Backslash Tricks

If we look at the `man` pages for any program written by the [GNU project](http://www.gnu.org/), we will see that in addition to command line options consisting of a dash and a single letter, there are also long option names that begin with two dashes. For example, the following are equivalent:

​	如果我们查看由[GNU项目](http://www.gnu.org/)编写的任何程序的`man`页，我们会看到除了由破折号和单个字母组成的命令行选项外，还有以两个破折号开头的长选项名。例如，下面两者是等价的：

```bash
ls -r
ls --reverse
```

Why do they support both? The short form is for lazy typists on the command line and the long form is mostly for scripts though some options may only be available in long form. Sometimes it is better to use a long option when the option is obscure or we want to document more clearly what an option is. This is especially useful when writing scripts where maximum readability is desired, and besides, anytime we can save ourselves a trip to the man page is a good thing.

​	为什么它们都被支持？短格式适用于命令行上的懒惰打字者，而长格式主要用于脚本，尽管某些选项可能仅在长格式中可用。当选项晦涩难懂或者我们想更清楚地记录选项是什么时，使用长选项可能更好。这在编写希望最大可读性的脚本时特别有用，而且，任何时候我们能够节省一次访问man页都是一件好事。

As we might suspect, using the long form options can make a single command line very long. To combat this problem, we can use a backslash to get the shell to ignore a newline character like this:

​	正如我们所猜测的，使用长格式选项可能会使单个命令行非常长。为了解决这个问题，我们可以使用反斜杠来使shell忽略换行符，像这样：

```bash
ls -l \
   --reverse \
   --human-readable \
   --full-time
```

Using the backslash in this way allows us to embed newlines in our command. Note that for this trick to work, the newline must be typed immediately after the backslash. If we put a space after the backslash, the space will be ignored, not the newline. Backslashes are also used to insert special characters into our text. These are called *backslash escape characters*. Here are the common ones:

​	以这种方式使用反斜杠可以在命令中插入换行符。请注意，为使此技巧生效，换行符必须紧跟在反斜杠后面输入。如果在反斜杠后面加上空格，则空格将被忽略，而不是换行符。反斜杠还用于在文本中插入特殊字符。这些特殊字符称为*反斜杠转义字符*。以下是常见的转义字符：

| **转义字符 Escape Character** | **名称 Name** | **可能的用途 Possible Uses**                                 |
| :---------------------------- | :------------ | :----------------------------------------------------------- |
| \n                            | newline       | 在文本中添加空行<br />Adding blank lines to text             |
| \t                            | tab           | 在文本中插入水平制表符<br />Inserting horizontal tabs to text |
| \a                            | alert         | 让我们的终端发出警报<br />Makes our terminal beep            |
| \\                            | backslash     | 插入一个反斜杠<br />Inserts a backslash                      |
| \f                            | formfeed      | 将其发送给打印机以弹出页面<br />Sending this to our printer ejects the page |

The use of the backslash escape characters is very common. This idea first appeared in the C programming language. Today, the shell, C++, Perl, python, awk, tcl, and many other programming languages use this concept. Using the `echo` command with the -e option will allow us to demonstrate:

​	使用反斜杠转义字符非常常见。这个概念最初出现在C编程语言中。如今，Shell、C++、Perl、Python、Awk、Tcl和许多其他编程语言都使用了这个概念。使用带有-e选项的`echo`命令可以让我们进行演示：

```bash
[me@linuxbox me]$ echo -e "Inserting several blank lines\n\n\n"
Inserting several blank lines

[me@linuxbox me]$ echo -e "Words\tseparated\tby\thorizontal\ttabs."
Words separated   by  horizontal  tabs
[me@linuxbox me]$ echo -e "\aMy computer went \"beep\"."
My computer went "beep".
[me@linuxbox me]$ echo -e "DEL C:\\WIN2K\\LEGACY_OS.EXE"
DEL C:\WIN2K\LEGACY_OS.EXE
```

