+++
title = "输入/输出重定向"
date = 2023-06-08T10:56:00+08:00
weight = 7
description = ""
isCJKLanguage = true
draft = false

+++

# 输入/输出重定向 - I/O Redirection

https://linuxcommand.org/lc3_lts0070.php

In this lesson, we will explore a powerful feature used by command line programs called *input/output redirection*. As we have seen, many commands such as `ls` print their output on the display. This does not have to be the case, however. By using some special notations we can *redirect* the output of many commands to files, devices, and even to the input of other commands.

​	在这个课程中，我们将探索命令行程序中使用的一项强大功能，称为*输入/输出重定向*。正如我们所见，许多命令（如 `ls`）将它们的输出打印到显示器上。但是，情况并非总是如此。通过使用一些特殊的符号，我们可以将许多命令的输出*重定向*到文件、设备，甚至其他命令的输入。

## 标准输出 Standard Output

Most command line programs that display their results do so by sending their results to a facility called *standard output*. By default, standard output directs its contents to the display. To redirect standard output to a file, the ">" character is used like this:

​	大多数命令行程序将它们的结果显示出来是通过将结果发送到一个称为*标准输出*的设备。默认情况下，标准输出将其内容定向到显示器上。要将标准输出重定向到文件，可以使用 ">" 符号，像这样：

```bash
[me@linuxbox me]$ ls > file_list.txt
```

In this example, the `ls` command is executed and the results are written in a file named `file_list.txt`. Since the output of `ls` was redirected to the file, no results appear on the display.

​	在这个例子中，执行了 `ls` 命令，并将结果写入名为 `file_list.txt` 的文件中。由于 `ls` 的输出被重定向到文件，所以在显示器上没有显示任何结果。

Each time the command above is repeated, `file_list.txt` is overwritten from the beginning with the output of the command `ls`. To have the new results *appended* to the file instead, we use ">>" like this:

​	每次重复执行上述命令时，`file_list.txt` 将被从头开始覆盖为 `ls` 命令的输出。如果要将新结果*追加*到文件而不是覆盖，可以使用 ">>"，像这样：

```bash
[me@linuxbox me]$ls >> file_list.txt
```

When the results are appended, the new results are added to the end of the file, thus making the file longer each time the command is repeated. If the file does not exist when we attempt to append the redirected output, the file will be created.

​	当结果被追加时，新的结果会添加到文件的末尾，从而使得文件每次重复执行命令时都会变长。如果在尝试追加重定向输出时文件不存在，文件将会被创建。

## 标准输入 Standard Input

Many commands can accept input from a facility called *standard input*. By default, standard input gets its contents from the keyboard, but like standard output, it can be redirected. To redirect standard input from a file instead of the keyboard, the "<" character is used like this:

​	许多命令可以从一个称为*标准输入*的设备接受输入。默认情况下，标准输入从键盘获取内容，但是像标准输出一样，它也可以被重定向。要将标准输入从文件而不是键盘重定向，可以使用 "<" 符号，像这样：

```bash
[me@linuxbox me]$ sort < file_list.txt
```

In the example above, we used the `sort` command to process the contents of `file_list.txt`. The results are output on the display since the standard output was not redirected. We could redirect standard output to another file like this:

​	在上面的例子中，我们使用了 `sort` 命令来处理 `file_list.txt` 的内容。由于没有重定向标准输出，结果会输出到显示器上。我们可以像这样将标准输出重定向到另一个文件：

```bash
[me@linuxbox me]$ sort < file_list.txt > sorted_file_list.txt
```

As we can see, a command can have both its input and output redirected. Be aware that the order of the redirection does not matter. The only requirement is that the redirection operators (the "<" and ">") must appear after the other options and arguments in the command.

​	如我们所见，一个命令可以同时重定向其输入和输出。需要注意的是，重定向的顺序不重要。唯一的要求是重定向操作符（"`<`" 和 "`>`"）必须出现在命令中其他选项和参数之后。

## 管道 Pipelines

The most useful and powerful thing we can do with I/O redirection is to connect multiple commands together to form what are called *pipelines*. With pipelines, the standard output of one command is fed into the standard input of another. Here is a very useful example:

​	使用I/O重定向最有用和强大的功能之一是将多个命令连接在一起，形成所谓的*管道*。通过管道，一个命令的标准输出被发送到另一个命令的标准输入。下面是一个非常有用的例子：

```bash
[me@linuxbox me]$ ls -l | less
```

In this example, the output of the `ls` command is fed into `less`. By using this `"| less"` trick, we can make any command have scrolling output.

​	在这个例子中，`ls` 命令的输出被发送到 `less` 命令。通过使用这个 `"| less"` 的技巧，我们可以使任何命令都具有滚动输出。

By connecting commands together, we can accomplish amazing feats. Here are some examples to try:

​	通过将命令连接在一起，我们可以完成令人惊奇的任务。以下是一些可以尝试的例子：

| **命令 Command**                | **What it does**                                             |
| :------------------------------ | :----------------------------------------------------------- |
| `ls -lt | head`                 | 显示当前目录中最新的10个文件。<br />Displays the 10 newest files in the current directory. |
| `du | sort -nr`                 | 显示一个目录列表，以及它们所占用的空间大小，从最大到最小排序。<br />Displays a list of directories and how much space they consume, sorted from the largest to the smallest. |
| `find . -type f -print | wc -l` | 显示当前工作目录及其所有子目录中的文件总数。<br />Displays the total number of files in the current working directory and all of its subdirectories. |

## 过滤器 Filters

One kind of program frequently used in pipelines is called a *filter*. Filters take standard input and perform an operation upon it and send the results to standard output. In this way, they can be combined to process information in powerful ways. Here are some of the common programs that can act as filters:

​	在管道中经常使用的一种程序称为*过滤器*。过滤器接受标准输入并对其进行操作，然后将结果发送到标准输出。通过这种方式，它们可以结合在一起以强大的方式处理信息。以下是一些常见的可以作为过滤器的程序：

| **程序 Program** | **What it does**                                             |
| :--------------- | :----------------------------------------------------------- |
| `sort`           | 对标准输入进行排序，然后将排序结果输出到标准输出。<br />Sorts standard input then outputs the sorted result on standard output. |
| `uniq`           | 对排序后的标准输入数据流进行操作，删除重复的数据行（确保每行都是唯一的）。<br />Given a sorted stream of data from standard input, it removes duplicate lines of data (i.e., it makes sure that every line is unique). |
| `grep`           | 检查从标准输入接收到的每一行数据，并输出包含指定字符模式的每一行。<br />Examines each line of data it receives from standard input and outputs every line that contains a specified pattern of characters. |
| `fmt`            | 从标准输入读取文本，然后在标准输出上输出格式化后的文本。<br />Reads text from standard input, then outputs formatted text on standard output. |
| `pr`             | 从标准输入接收文本输入，将数据分页并准备好打印的页眉、页脚和分页符。<br />Takes text input from standard input and splits the data into pages with page breaks, headers and footers in preparation for printing. |
| `head`           | 输出其输入的前几行。可用于获取文件的标题。<br />Outputs the first few lines of its input. Useful for getting the header of a file. |
| `tail`           | 输出其输入的最后几行。可用于获取日志文件中的最新条目等。<br />Outputs the last few lines of its input. Useful for things like getting the most recent entries from a log file. |
| `tr`             | 字符转换。可用于执行诸如大小写转换或更改行终止字符类型（例如，将DOS文本文件转换为Unix样式的文本文件）等任务。<br />Translates characters. Can be used to perform tasks such as upper/lowercase conversions or changing line termination characters from one type to another (for example, converting DOS text files into Unix style text files). |
| `sed`            | 流编辑器。可执行比`tr`更复杂的文本转换操作。<br />Stream editor. Can perform more sophisticated text translations than `tr`. |
| `awk`            | 一种专为构建过滤器而设计的完整编程语言。非常强大。<br />An entire programming language designed for constructing filters. Extremely powerful. |

## 使用管道执行任务 Performing tasks with pipelines

1. **Printing from the command line.** Linux provides a program called `lpr` that accepts standard input and sends it to the printer. It is often used with pipes and filters. Here are a couple of examples:

2. **从命令行打印。** Linux提供了一个名为`lpr`的程序，可以接受标准输入并将其发送到打印机。它经常与管道和过滤器一起使用。以下是一些示例：

   ```
   cat poorly_formatted_report.txt | fmt | pr | lpr
   
   cat unsorted_list_with_dupes.txt | sort | uniq | pr | lpr
   ```

   In the first example, we use `cat` to read the file and output it to standard output, which is piped into the standard input of `fmt. fmt` formats the text into neat paragraphs and outputs it to standard output, which is piped into the standard input of `pr. pr` splits the text neatly into pages and outputs it to standard output, which is piped into the standard input of `lpr. lpr` takes its standard input and sends it to the printer.

   在第一个示例中，我们使用`cat`读取文件并将其输出到标准输出，然后通过管道将其传递给`fmt`的标准输入。`fmt`将文本格式化为整齐的段落，并将其输出到标准输出，然后通过管道将其传递给`pr`的标准输入。`pr`将文本整齐地分页，并将其输出到标准输出，然后通过管道将其传递给`lpr`的标准输入。`lpr`接受标准输入并将其发送到打印机。

   The second example starts with an unsorted list of data with duplicate entries. First, `cat` sends the list into `sort` which sorts it and feeds it into `uniq` which removes any duplicates. Next `pr` and `lpr` are used to paginate and print the list.

   第二个示例以一个包含重复条目的未排序数据列表开始。首先，`cat`将列表发送到`sort`，`sort`对其进行排序并将其传递给`uniq`，`uniq`删除任何重复项。然后使用`pr`和`lpr`进行分页和打印列表。

3. **Viewing the contents of tar files** Often you will see software distributed as a *gzipped tar file*. This is a traditional Unix style tape archive file (created with `tar`) that has been compressed with `gzip`. You can recognize these files by their traditional file extensions, ".tar.gz" or ".tgz". You can use the following command to view the directory of such a file on a Linux system:

4. **查看tar文件的内容** 经常会看到软件以*gzipped tar文件*的形式分发。这是一个传统的Unix风格的磁带归档文件（使用`tar`创建），经过`gzip`压缩。你可以通过文件的传统扩展名".tar.gz"或".tgz"来识别这些文件。你可以使用以下命令在Linux系统上查看此类文件的目录：

   ```
   tar tzvf name_of_file.tar.gz | less
   ```


## 进一步阅读 Further Reading

- Chapter 6 of [*The Linux Command Line*](https://linuxcommand.org/tlcl.php) covers this topic in more detail.
- [*The Linux Command Line*](https://linuxcommand.org/tlcl.php)的第6章更详细地介绍了这个主题。
- Chapters 19 through 21 of *The Linux Command Line* provide an in-depth look at the text processing tools available in Linux.
- 《The Linux Command Line》的第19至21章深入介绍了Linux中可用的文本处理工具。
- To learn more about the AWK programming language, consider the [AWK adventure](https://linuxcommand.org/lc3_adv_awk.php).
- 如果想更多了解AWK编程语言，请参考[AWK adventure](https://linuxcommand.org/lc3_adv_awk.php)。