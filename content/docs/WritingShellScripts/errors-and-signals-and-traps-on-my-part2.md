+++
title = "错误、信号和陷阱（哦，我的天！）- 第二部分"
weight = 15
date = 2023-06-08T11:19:55+08:00
description = ""
isCJKLanguage = true
draft = false

+++

# 错误、信号和陷阱（哦，我的天！）- 第二部分 Errors and Signals and Traps (Oh, My!) - Part 2

https://linuxcommand.org/lc3_wss0150.php

Errors are not the only way that a script can terminate unexpectedly. We also have to be concerned with signals. Consider the following program:

​	错误并不是脚本意外终止的唯一方式。我们还需要关注信号。考虑以下程序：

```bash
#!/bin/bash

echo "this script will endlessly loop until you stop it"
while true; do
  : # Do nothing
done
```

After we launch this script it will appear to hang. Actually, like most programs that appear to hang, it is really stuck inside a loop. In this case, it is waiting for the `true` command to return a non-zero exit status, which it never does. Once started, the script will continue until bash receives a signal that will stop it. We can send such a signal by typing Ctrl-c, the signal called SIGINT (short for SIGnal INTerrupt).

​	启动这个脚本后，它看起来会一直挂起。实际上，像大多数看起来挂起的程序一样，它实际上是陷入了一个循环中。在这种情况下，它正在等待`true`命令返回一个非零的退出状态，但实际上它永远不会返回。一旦启动，脚本将一直运行，直到bash接收到一个停止它的信号。我们可以通过键入`Ctrl-c`来发送这样的信号，这个信号被称为SIGINT（代表SIGnal INTerrupt）。

## 自我清理 Cleaning Up After Ourselves

Okay, so a signal can come along and make our script terminate. Why does it matter? Well, in many cases it doesn't matter and we can safely ignore signals, but in some cases it will matter.

​	好的，信号可以让我们的脚本终止。这有什么关系呢？嗯，在许多情况下，这并不重要，我们可以安全地忽略信号，但在某些情况下，它确实很重要。

Let's take a look at another script:

​	让我们看看另一个脚本：

```bash
#!/bin/bash

# Program to print a text file with headers and footers

TEMP_FILE=/tmp/printfile.txt

pr $1 > "$TEMP_FILE"

read -p "Print file? [y/n]: "
if [ "$REPLY" = "y" ]; then
  lpr "$TEMP_FILE"
fi
```

This script processes the text file specified on the command line with the `pr` command and stores the result in a temporary file. Next, it asks the user if they want to print the file. If the user types "y", then the temporary file is passed to the `lpr` program for printing (substitute `less` for `lpr` if there isn't a printer attached to the system.)

​	这个脚本会使用`pr`命令处理命令行上指定的文本文件，并将结果存储在临时文件中。然后，它会询问用户是否要打印该文件。如果用户输入"y"，则将临时文件传递给`lpr`程序进行打印（如果系统上没有连接打印机，则替换为`less`）。

Admittedly, this script has a lot of design problems. While it needs a file name passed on the command line, it doesn't check that it received one, and it doesn't check that the file actually exists. But the problem we want to focus on here is that when the script terminates, it leaves behind the temporary file.

​	诚然，这个脚本存在很多设计问题。虽然它需要在命令行上传递一个文件名，但它没有检查是否接收到文件名，也没有检查文件是否实际存在。但我们想要关注的问题是，当脚本终止时，它会留下临时文件。

Good practice dictates that we delete the temporary file `$TEMP_FILE` when the script terminates. This is easily accomplished by adding the following to the end of the script:

​	良好的实践规定，在脚本终止时删除临时文件`$TEMP_FILE`。我们可以通过在脚本末尾添加以下内容来轻松实现：

```bash
rm "$TEMP_FILE"
```

This would seem to solve the problem, but what happens if the user types ctrl-c when the "Print file? [y/n]:" prompt appears? The script will terminate at the `read` command and the `rm` command is never executed. Clearly, we need a way to respond to signals such as SIGINT when the `Ctrl-c` key is typed.

​	这似乎解决了问题，但是如果用户在出现"打印文件？[y/n]："提示时键入ctrl-c会发生什么？脚本将在`read`命令处终止，并且`rm`命令将不会执行。显然，我们需要一种方法来响应诸如SIGINT的信号，即键入`Ctrl-c`键时的信号。

Fortunately, bash provides a method to perform commands if and when signals are received.

​	幸运的是，bash提供了一种在接收到信号时执行命令的方法。

## trap

The `trap` command allows us to execute a command when our script receives a signal. It works like this:

​	`trap`命令允许我们在脚本接收到信号时执行命令。它的用法如下：

```bash
trap arg signals
```

"signals" is a list of signals to intercept and "arg" is a command to execute when one of the signals is received. For our printing script, we might handle the signal problem this way:

​	"signals"是要拦截的信号列表，"arg"是接收到其中一个信号时要执行的命令。对于我们的打印脚本，我们可以通过以下方式处理信号问题：

```bash
#!/bin/bash

# Program to print a text file with headers and footers

TEMP_FILE=/tmp/printfile.txt

trap "rm $TEMP_FILE; exit" SIGHUP SIGINT SIGTERM

pr $1 > "$TEMP_FILE"

read -p "Print file? [y/n]: "
if [ "$REPLY" = "y" ]; then
  lpr "$TEMP_FILE"
fi
rm "$TEMP_FILE"
```

Here we have added a `trap` command that will execute "`rm $TEMP_FILE`" if any of the listed signals is received. The three signals listed are the most common ones that most scripts are likely to encounter, but there are many more that can be specified. For a complete list, type "`trap -l`". In addition to listing the signals by name, you may alternately specify them by number.

​	在这里，我们添加了一个`trap`命令，如果接收到列出的任何信号，则执行"`rm $TEMP_FILE`"。列出的三个信号是大多数脚本可能遇到的最常见的信号，但还有许多其他信号可以指定。要获取完整列表，请输入"`trap -l`"。除了按名称列出信号外，您还可以用数字指定信号。

## 来自外部空间的信号9 Signal 9 from Outer Space

There is one signal that you cannot trap: SIGKILL or signal 9. The kernel immediately terminates any process sent this signal and no signal handling is performed. Since it will always terminate a program that is stuck, hung, or otherwise screwed up, it is tempting to think that it's the easy way out when we have to get something to stop and go away. There are often references to the following command which sends the SIGKILL signal:

​	有一个信号是无法捕获的：SIGKILL或信号9。内核立即终止接收到此信号的任何进程，并且不执行任何信号处理。由于它始终终止程序的运行（无论是卡住、挂起还是其他故障），我们可能会觉得这是一种简便的方式来停止和结束某些东西。通常会提到以下命令发送SIGKILL信号：

```
kill -9
```

However, despite its apparent ease, we must remember that when we send this signal, no processing is done by the application. Often this is OK, but with many programs it's not. In particular, many complex programs (and some not-so-complex) create *lock files* to prevent multiple copies of the program from running at the same time. When a program that uses a lock file is sent a SIGKILL, it doesn't get the chance to remove the lock file when it terminates. The presence of the lock file will prevent the program from restarting until the lock file is manually removed.

​	然而，尽管它看起来很简单，但我们必须记住，当我们发送此信号时，应用程序不会执行任何处理。这在许多程序中可能是可以接受的，但在许多复杂程序（以及一些不那么复杂的程序）中，它是不可接受的。特别是，许多复杂程序（以及一些不那么复杂的程序）会创建*锁文件*，以防止同时运行多个程序副本。当发送SIGKILL给使用锁文件的程序时，它无法在终止时删除锁文件。锁文件的存在将阻止程序在手动删除锁文件之前重新启动。

Be warned. Use SIGKILL as a last resort.

​	请注意，SIGKILL只在万不得已时使用。

## 一个clean_up函数 A clean_up Function

While the `trap` command has solved the problem, we can see that it has some limitations. Most importantly, it will only accept a single string containing the command to be performed when the signal is received. We could get clever and use ";" and put multiple commands in the string to get more complex behavior, but frankly, it's ugly. A better way would be to create a function that is called when we want to perform any actions at the end of a script. For our purposes, we will call this function `clean_up`.

​	虽然`trap`命令解决了问题，但我们可以看到它有一些限制。最重要的是，它只接受包含在接收到信号时要执行的命令的单个字符串。我们可以巧妙地使用";"并将多个命令放在字符串中以获得更复杂的行为，但老实说，这样做并不美观。一个更好的方法是创建一个在我们希望在脚本末尾执行任何操作时调用的函数。对于我们的目的，我们将称此函数为`clean_up`。

```bash
#!/bin/bash

# Program to print a text file with headers and footers

TEMP_FILE=/tmp/printfile.txt

clean_up() {

  # Perform program exit housekeeping
  rm "$TEMP_FILE"
  exit
}

trap clean_up SIGHUP SIGINT SIGTERM

pr $1 > "$TEMP_FILE"

read -p "Print file? [y/n]: "
if [ "$REPLY" = "y" ]; then
  lpr "$TEMP_FILE"
fi
clean_up
```

The use of a clean up function is a good idea for our error handling routines too. After all, when a program terminates (for whatever reason), we should clean up after ourselves. Here is finished version of our program with improved error and signal handling:

​	使用清理函数对于我们的错误处理例程也是一个好主意。毕竟，当程序终止时（无论出于何种原因），我们应该在自己之后进行清理。这是改进后的程序版本，包括了更好的错误和信号处理：

```bash
#!/bin/bash

# Program to print a text file with headers and footers

# Usage: printfile file

PROGNAME="$(basename $0)"

# Create a temporary file name that gives preference
# to the user's local tmp directory and has a name
# that is resistant to tmp race attacks

if [ -d "~/tmp" ]; then
  TEMP_DIR=~/tmp
else
  TEMP_DIR=/tmp
fi
TEMP_FILE="$TEMP_DIR/$PROGNAME.$$.$RANDOM"

usage() {

  # Display usage message on standard error
  echo "Usage: $PROGNAME file" 1>&2
}

clean_up() {

  # Perform program exit housekeeping
  # Optionally accepts an exit status
  rm -f "$TEMP_FILE"
  exit $1
}

error_exit() {

  # Display error message and exit
  echo "${PROGNAME}: ${1:-"Unknown Error"}" 1>&2
  clean_up 1
}

trap clean_up SIGHUP SIGINT SIGTERM

if [ $# != "1" ]; then
  usage
  error_exit "one file to print must be specified"
fi
if [ ! -f "$1" ]; then
  error_exit "file $1 cannot be read"
fi

pr $1 > "$TEMP_FILE" || error_exit "cannot format file"

read -p "Print file? [y/n]: "
if [ "$REPLY" = "y" ]; then
  lpr "$TEMP_FILE" || error_exit "cannot print file"
fi
clean_up
```



## 创建安全的临时文件 Creating Safe Temporary Files

In the program above, there a number of steps taken to help secure the temporary file used by this script. It is a Unix tradition to use a directory called `/tmp` to place temporary files used by programs. Everyone may write files into this directory. This naturally leads to some security concerns. If possible, avoid writing files in the `/tmp` directory. The preferred technique is to write them in a local directory such as `~/tmp` (a tmp subdirectory in the user's home directory.) If files must be written in `/tmp`, we must take steps to make sure the file names are not predictable. Predictable file names may allow an attacker to create symbolic links to other files the attacker wants the user to overwrite.

​	在上面的程序中，采取了一些步骤来帮助保护该脚本使用的临时文件。在Unix中，惯例是使用一个名为`/tmp`的目录来存放程序使用的临时文件。任何人都可以将文件写入此目录。这自然引起了一些安全问题。如果可能的话，避免在`/tmp`目录中写入文件。首选的技术是将它们写入一个本地目录，例如`~/tmp`（用户主目录中的tmp子目录）。如果必须在`/tmp`中写入文件，我们必须采取措施确保文件名不可预测。可预测的文件名可能会允许攻击者创建符号链接到攻击者希望用户覆盖的其他文件。

A good file name will help identify what wrote the file, but will not be entirely predictable. In the script above, the following line of code created the temporary file `$TEMP_FILE`:

​	一个好的文件名将有助于识别写入文件的程序，但不会完全可预测。在上面的脚本中，以下代码行创建了临时文件`$TEMP_FILE`：

```bash
TEMP_FILE="$TEMP_DIR/$PROGNAME.$$.$RANDOM"
```

The `$TEMP_DIR` variable contains either `/tmp` or `~/tmp` depending on the availability of the directory. It is common practice to embed the name of the program into the file name. We have done that with the constant `$PROGNAME` constructed at the beginning of the sectipt. Next, we use the `$$` shell variable to embed the process id (pid) of the program. This further helps identify what process is responsible for the file. Surprisingly, the process id alone is not unpredictable enough to make the file safe, so we add the `$RANDOM` shell variable to append a random number to the file name. With this technique, we create a file name that is both easily identifiable and unpredictable.

​	`$TEMP_DIR`变量包含`/tmp`或`~/tmp`，具体取决于目录的可用性。通常的做法是将程序的名称嵌入到文件名中。我们在脚本开头构造的常量`$PROGNAME`已经实现了这一点。接下来，我们使用`$$` shell变量将程序的进程ID（PID）嵌入到其中。这进一步有助于识别哪个进程负责该文件。令人惊讶的是，仅有进程ID还不足以使文件变得安全，因此我们使用`$RANDOM` shell变量将一个随机数附加到文件名上。通过这种技术，我们创建了一个既容易识别又不可预测的文件名。

## 就是这样 There You Have It

This concludes the LinuxCommand.org tutorials. I sincerely hope you found them both useful and enjoyable. If you did, complete your command line education by downloading [my book](https://linuxcommand.org/tlcl.php).

​	这就是LinuxCommand.org教程的全部内容。我真诚希望您觉得它们既有用又有趣。如果是这样的话，通过下载[我的书](https://linuxcommand.org/tlcl.php)来完善您的命令行教育。