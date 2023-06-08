+++
title = "位置参数"
weight = 12
date = 2023-06-08T11:19:07+08:00
description = ""
isCJKLanguage = true
draft = false

+++

# 位置参数 Positional Parameters

https://linuxcommand.org/lc3_wss0120.php

When we last left our script, it looked something like this:

​	当我们上次离开我们的脚本时，它看起来像这样：

```bash
#!/bin/bash

# sysinfo_page - A script to produce a system information HTML file

##### Constants

TITLE="System Information for $HOSTNAME"
RIGHT_NOW="$(date +"%x %r %Z")"
TIME_STAMP="Updated on $RIGHT_NOW by $USER"

##### Functions

system_info()
{
    echo "<h2>System release info</h2>"
    echo "<p>Function not yet implemented</p>"

}   # end of system_info


show_uptime()
{
    echo "<h2>System uptime</h2>"
    echo "<pre>"
    uptime
    echo "</pre>"

}   # end of show_uptime


drive_space()
{
    echo "<h2>Filesystem space</h2>"
    echo "<pre>"
    df
    echo "</pre>"

}   # end of drive_space


home_space()
{
    # Only the superuser can get this information

    if [ "$(id -u)" = "0" ]; then
        echo "<h2>Home directory space by user</h2>"
        echo "<pre>"
        echo "Bytes Directory"
        du -s /home/* | sort -nr
        echo "</pre>"
    fi

}   # end of home_space



##### Main

cat <<- _EOF_
  <html>
  <head>
      <title>$TITLE</title>
  </head>
  <body>
      <h1>$TITLE</h1>
      <p>$TIME_STAMP</p>
      $(system_info)
      $(show_uptime)
      $(drive_space)
      $(home_space)
  </body>
  </html>
_EOF_
```

We have most things working, but there are several more features we can add:

​	我们已经完成了大部分工作，但还有几个功能可以添加：

1. We should be able to specify the name of the output file on the command line, as well as set a default output file name if no name is specified.

2. 我们应该能够在命令行上指定输出文件的名称，如果没有指定名称，还应设置默认输出文件名。

3. Let's offer an interactive mode that will prompt for a file name and warn the user if the file exists and prompt the user to overwrite it.

4. 让我们提供一个交互模式，提示用户输入文件名，并在文件存在时警告用户并询问是否覆盖它。

5. Naturally, we want to have a help option that will display a usage message.

6. 当然，我们希望有一个帮助选项，显示用法信息。


All of these features involve using command line options and arguments. To handle options on the command line, we use a facility in the shell called *positional parameters*. Positional parameters are a series of special variables (`$0` through `$9`) that contain the contents of the command line.

​	所有这些功能都涉及使用命令行选项和参数。为了处理命令行上的选项，我们使用Shell中的一个功能，称为*位置参数*。位置参数是一系列特殊变量（`$0`到`$9`），它们包含命令行的内容。

Let's imagine the following command line:

​	让我们想象以下命令行：

```bash
[me@linuxbox me]$ some_program word1 word2 word3
```

If `some_program` were a bash shell script, we could read each item on the command line because the positional parameters contain the following:

​	如果`some_program`是一个bash shell脚本，我们可以读取命令行上的每个项目，因为位置参数包含以下内容：

- $0 would contain "some_program"
- $1 would contain "word1"
- $2 would contain "word2"
- $3 would contain "word3"
- `$0`将包含"some_program"
- `$1`将包含"word1"
- `$2`将包含"word2"
- `$3`将包含"word3"

Here is a script we can use to try this out:

​	以下是我们可以用来尝试的脚本：

```bash
#!/bin/bash

echo "Positional Parameters"
echo '$0 = ' $0
echo '$1 = ' $1
echo '$2 = ' $2
echo '$3 = ' $3
```



## 检测命令行参数 Detecting Command Line Arguments

Often, we will want to check to see if we have comand line arguments on which to act. There are a couple of ways to do this. First, we could simply check to see if `$1` contains anything like so:

​	通常，我们需要检查是否有命令行参数可供操作。有几种方法可以做到这一点。首先，我们可以简单地检查`$1`是否包含任何内容，例如：

```bash
#!/bin/bash

if [ "$1" != "" ]; then
    echo "Positional parameter 1 contains something"
else
    echo "Positional parameter 1 is empty"
fi
```

Second, the shell maintains a variable called `$#` that contains the number of items on the command line in addition to the name of the command (`$0`).

​	其次，Shell维护一个名为`$#`的变量，其中包含命令行上的项目数，以及命令的名称（`$0`）。

```bash
#!/bin/bash

if [ $# -gt 0 ]; then
    echo "Your command line contains $# arguments"
else
    echo "Your command line contains no arguments"
fi
```



## 命令行选项 Command Line Options

As we discussed before, many programs, particularly ones from [the GNU Project](http://www.gnu.org/), support both short and long command line options. For example, to display a help message for many of these programs, we may use either the "`-h`" option or the longer "`--help`" option. Long option names are typically preceded by a double dash. We will adopt this convention for our scripts.

​	如前所述，许多程序，特别是来自[GNU项目](http://www.gnu.org/)的程序，支持短选项和长选项。例如，要显示许多这些程序的帮助消息，我们可以使用"`-h`"选项或更长的"`--help`"选项。长选项名称通常以两个连字符开头。我们将采用这种约定来编写我们的脚本。

Here is the code we will use to process our command line:

​	以下是我们将用于处理命令行的代码：

```bash
interactive=
filename=~/sysinfo_page.html

while [ "$1" != "" ]; do
    case $1 in
        -f | --file )           shift
                                filename="$1"
                                ;;
        -i | --interactive )    interactive=1
                                ;;
        -h | --help )           usage
                                exit
                                ;;
        * )                     usage
                                exit 1
    esac
    shift
done
```

This code is a little tricky, so we need to explain it.

​	这段代码有点复杂，所以我们需要解释一下。

The first two lines are pretty easy. We set the variable `interactive` to be empty. This will indicate that the interactive mode has not been requested. Then we set the variable `filename` to contain a default file name. If nothing else is specified on the command line, this file name will be used.

​	前两行很简单。我们将变量`interactive`设置为空。这表示未请求交互模式。然后我们设置变量`filename`包含默认文件名。如果在命令行上没有指定其他内容，将使用此文件名。

After these two variables are set, we have default settings, in case the user does not specify any options.

​	设置这两个变量后，我们有了默认设置，以防用户未指定任何选项。

Next, we construct a `while` loop that will cycle through all the items on the command line and process each one with `case`. The `case` will detect each possible option and process it accordingly.

​	接下来，我们构建一个`while`循环，将循环遍历命令行上的所有项目，并使用`case`处理每个项目。`case`将检测每个可能的选项并相应地处理它。

Now the tricky part. How does that loop work? It relies on the magic of `shift`.

​	现在是棘手的部分。这个循环是如何工作的？它依赖于`shift`的魔力。

`shift` is a shell builtin that operates on the positional parameters. Each time we invoke `shift`, it "shifts" all the positional parameters down by one. `$2` becomes `$1`, `$3` becomes `$2`, `$4` becomes `$3`, and so on. Try this:

​	`shift`是一个Shell内置命令，用于操作位置参数。每次我们调用`shift`时，它会将所有位置参数向下“移动”一位。`$2`变为`$1`，`$3`变为`$2`，`$4`变为`$3`，依此类推。请试试这个：

```bash
#!/bin/bash

echo "You start with $# positional parameters"

# Loop until all parameters are used up
while [ "$1" != "" ]; do
    echo "Parameter 1 equals $1"
    echo "You now have $# positional parameters"

    # Shift all the parameters down by one
    shift

done
```



## 获取选项的参数 Getting an Option's Argument

Our "`-f`" option requires a valid file name as an argument. We use `shift` again to get the next item from the command line and assign it to `filename`. Later we will have to check the content of `filename` to make sure it is valid.

​	我们的"`-f`"选项需要一个有效的文件名作为参数。我们再次使用`shift`从命令行中获取下一个项目，并将其赋给`filename`。稍后，我们将需要检查`filename`的内容，以确保它是有效的。

## 将命令行处理器集成到脚本中  Integrating the Command Line Processor into the Script

We will have to move a few things around and add a usage function to get this new routine integrated into our script. We'll also add some test code to verify that the command line processor is working correctly. Our script now looks like this:

​	我们需要移动一些内容并添加一个usage函数来将这个新例程集成到我们的脚本中。我们还将添加一些测试代码来验证命令行处理器是否正常工作。我们的脚本现在如下所示：

```bash
#!/bin/bash

# sysinfo_page - A script to produce a system information HTML file

##### Constants

TITLE="System Information for $HOSTNAME"
RIGHT_NOW="$(date +"%x %r %Z")"
TIME_STAMP="Updated on $RIGHT_NOW by $USER"

##### Functions

system_info()
{
    echo "<h2>System release info</h2>"
    echo "<p>Function not yet implemented</p>"

}   # end of system_info


show_uptime()
{
    echo "<h2>System uptime</h2>"
    echo "<pre>"
    uptime
    echo "</pre>"

}   # end of show_uptime


drive_space()
{
    echo "<h2>Filesystem space</h2>"
    echo "<pre>"
    df
    echo "</pre>"

}   # end of drive_space


home_space()
{
    # Only the superuser can get this information

    if [ "$(id -u)" = "0" ]; then
        echo "<h2>Home directory space by user</h2>"
        echo "<pre>"
        echo "Bytes Directory"
        du -s /home/* | sort -nr
        echo "</pre>"
    fi

}   # end of home_space


write_page()
{
    cat <<- _EOF_
    <html>
        <head>
        <title>$TITLE</title>
        </head>
        <body>
        <h1>$TITLE</h1>
        <p>$TIME_STAMP</p>
        $(system_info)
        $(show_uptime)
        $(drive_space)
        $(home_space)
        </body>
    </html>
_EOF_

}

usage()
{
    echo "usage: sysinfo_page [[[-f file ] [-i]] | [-h]]"
}


##### Main

interactive=
filename=~/sysinfo_page.html

while [ "$1" != "" ]; do
    case $1 in
        -f | --file )           shift
                                filename=$1
                                ;;
        -i | --interactive )    interactive=1
                                ;;
        -h | --help )           usage
                                exit
                                ;;
        * )                     usage
                                exit 1
    esac
    shift
done


# Test code to verify command line processing

if [ "$interactive" = "1" ]; then
  echo "interactive is on"
else
  echo "interactive is off"
fi
echo "output file = $filename"


# Write page (comment out until testing is complete)

# write_page > $filename
```



## 添加交互模式 Adding Interactive Mode

The interactive mode is implemented with the following code:

​	交互模式使用以下代码实现：

```bash
if [ "$interactive" = "1" ]; then

    response=

    read -p "Enter name of output file [$filename] > " response
    if [ -n "$response" ]; then
        filename="$response"
    fi

    if [ -f $filename ]; then
        echo -n "Output file exists. Overwrite? (y/n) > "
        read response
        if [ "$response" != "y" ]; then
            echo "Exiting program."
            exit 1
        fi
    fi
fi
```

First, we check if the interactive mode is on, otherwise we don't have anything to do. Next, we ask the user for the file name. Notice the way the prompt is worded:

​	首先，我们检查交互模式是否打开，否则我们没有任何事情要做。接下来，我们要求用户输入文件名。注意提示的方式：

```bash
"Enter name of output file [$filename] > "
```

We display the current value of `filename` since, the way this routine is coded, if the user just presses the enter key, the default value of `filename` will be used. This is accomplished in the next two lines where the value of `response` is checked. If `response` is not empty, then `filename` is assigned the value of `response`. Otherwise, `filename` is left unchanged, preserving its default value.

​	我们显示`filename`的当前值，因为按下回车键时，如果用户没有输入任何内容，则将使用`filename`的默认值。在下面两行中，检查`response`的值。如果`response`不为空，则将`filename`赋值为`response`的值。否则，保持`filename`不变，保留其默认值。

After we have the name of the output file, we check if it already exists. If it does, we prompt the user. If the user response is not "y," we give up and exit, otherwise we can proceed.

​	在获得输出文件的名称后，我们检查它是否已经存在。如果存在，我们会提示用户。如果用户响应不是"y"，我们放弃并退出，否则我们可以继续。