+++
title = "流程控制 - 第三部分"
weight = 13
date = 2023-06-08T11:18:51+08:00
description = ""
isCJKLanguage = true
draft = false

+++

# 流程控制 - 第三部分 Flow Control - Part 3

https://linuxcommand.org/lc3_wss0130.php

Now that we have learned about positional parameters, it's time to cover the remaining flow control statement, `for`. Like `while` and `until`, `for` is used to construct loops. `for` works like this:

​	现在我们已经学习了关于位置参数的知识，是时候介绍剩下的流程控制语句了，即`for`循环。和`while`和`until`一样，`for`用于构建循环。`for`的使用方式如下：

```bash
for variable in words; do
    commands
done
```

In essence, `for` assigns a word from the list of words to the specified variable, executes the commands, and repeats this over and over until all the words have been used up. Here is an example:

​	简而言之，`for`将列表中的单词赋值给指定的变量，执行命令，然后重复这个过程，直到所有单词都被用完。下面是一个示例：

```bash
#!/bin/bash

for i in word1 word2 word3; do
    echo "$i"
done
```

`

In this example, the variable `i` is assigned the string "`word1`", then the statement `echo "$i"` is executed, then the variable `i` is assigned the string "`word2`", and the statement `echo "$i"` is executed, and so on, until all the words in the list of words have been assigned.

​	在这个示例中，变量`i`首先被赋值为字符串"`word1`"，然后执行语句`echo "$i"`，然后变量`i`被赋值为字符串"`word2`"，执行语句`echo "$i"`，依此类推，直到列表中的所有单词都被赋值。

The interesting thing about `for` is the many ways we can construct the list of words. All kinds of expansions can be used. In the next example, we will construct the list of words using command substitution:

​	`for`的有趣之处在于我们可以以多种方式构建单词列表。可以使用各种扩展。在下一个示例中，我们将使用命令替换来构建单词列表：

```bash
#!/bin/bash

count=0
for i in $(cat ~/.bash_profile); do
    count=$((count + 1))
    echo "Word $count ($i) contains $(echo -n $i | wc -c) characters"
done
```

Here we take the file `.bash_profile` and count the number of words in the file and the number of characters in each word.

​	在这里，我们读取`.bash_profile`文件，计算文件中的单词数以及每个单词中的字符数。

So what's this got to do with positional parameters? Well, one of the features of `for` is that it can use the positional parameters as the list of words:

​	那么这与位置参数有什么关系呢？好吧，`for`的一个特性是可以使用位置参数作为单词列表：

```bash
#!/bin/bash

for i in "$@"; do
    echo $i
done
```

The shell variable `"$@"` contains the list of command line arguments. This technique is often used to process a list of files on the command line. Here is a another example:

​	Shell变量`"$@"`包含命令行参数的列表。这种技巧通常用于处理命令行上的文件列表。下面是另一个示例：

```bash
#!/bin/bash

for filename in "$@"; do
    result=
    if [ -f "$filename" ]; then
        result="$filename is a regular file"
    else
        if [ -d "$filename" ]; then
            result="$filename is a directory"
        fi
    fi
    if [ -w "$filename" ]; then
        result="$result and it is writable"
    else
        result="$result and it is not writable"
    fi
    echo "$result"
done
```

Try this script. Give it a list of files or a wildcard like "`*`" to see it work.

​	尝试运行这个脚本。给它一个文件列表或通配符"`*`"，看看它的工作原理。

The use of `in "$@"` is so common that it is assumed if the `in words` clause is ommited.

​	使用`in "$@"`的方式非常常见，如果省略了`in words`子句，就默认使用它。

Here is another example script. This one compares the files in two directories and lists which files in the first directory are missing from the second.

​	下面是另一个示例脚本。这个脚本比较两个目录中的文件，并列出第一个目录中缺失于第二个目录中的文件。

```bash
#!/bin/bash

# cmp_dir - program to compare two directories

# Check for required arguments
if [ $# -ne 2 ]; then
    echo "usage: $0 directory_1 directory_2" 1>&2
    exit 1
fi

# Make sure both arguments are directories
if [ ! -d "$1" ]; then
    echo "$1 is not a directory!" 1>&2
    exit 1
fi

if [ ! -d "$2" ]; then
    echo "$2 is not a directory!" 1>&2
    exit 1
fi

# Process each file in directory_1, comparing it to directory_2
missing=0
for filename in "$1"/*; do
    fn=$(basename "$filename")
    if [ -f "$filename" ]; then
        if [ ! -f "$2/$fn" ]; then
            echo "$fn is missing from $2"
            missing=$((missing + 1))
        fi
    fi
done
echo "$missing files missing"
```

Now on to the real work. We are going to improve the `home_space` function to output more information. Recall that our previous version looked like this:

​	现在让我们进入真正的工作内容。我们将改进`home_space`函数以输出更多信息。回顾一下，我们之前的版本如下：

```bash
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
```

Here is the new version:

​	下面是改进后的版本：

```bash
home_space() {
    echo "<h2>Home directory space by user</h2>"
    echo "<pre>"
    format="%8s%10s%10s   %-s\n"
    printf "$format" "Dirs" "Files" "Blocks" "Directory"
    printf "$format" "----" "-----" "------" "---------"
    if [ $(id -u) = "0" ]; then
        dir_list="/home/*"
    else
        dir_list=$HOME
    fi
    for home_dir in $dir_list; do
        total_dirs=$(find $home_dir -type d | wc -l)
        total_files=$(find $home_dir -type f | wc -l)
        total_blocks=$(du -s $home_dir)
        printf "$format" "$total_dirs" "$total_files" "$total_blocks"
    done
    echo "</pre>"

}   # end of home_space
```

This improved version introduces a new command `printf`, which is used to produce formatted output according to the contents of a *format string*. `printf` comes from the C programming language and has been implemented in many other programming languages including C++, Perl, awk, java, PHP, and of course, bash. More information about `printf` format strings can be found at:

​	这个改进的版本引入了一个新命令`printf`，它根据格式字符串的内容生成格式化输出。`printf`源自C编程语言，并已在许多其他编程语言中实现，包括C++、Perl、awk、Java、PHP和当然还有bash。关于`printf`格式字符串的更多信息可以在以下链接中找到： 

- [GNU Awk User's Guide - Control Letters](http://www.gnu.org/software/gawk/manual/html_node/Control-Letters.html#Control-Letters)
- [GNU Awk User's Guide - Format Modifiers](http://www.gnu.org/software/gawk/manual/html_node/Format-Modifiers.html#Format-Modifiers)
- [GNU Awk 用户手册 - 控制字母](http://www.gnu.org/software/gawk/manual/html_node/Control-Letters.html#Control-Letters)
- [GNU Awk 用户手册 - 格式修饰符](http://www.gnu.org/software/gawk/manual/html_node/Format-Modifiers.html#Format-Modifiers)

We also introduce the `find` command. `find` is used to search for files or directories that meet specific criteria. In the `home_space` function, we use `find` to list the directories and regular files in each home directory. Using the `wc` command, we count the number of files and directories found.

​	我们还引入了`find`命令。`find`用于搜索符合特定条件的文件或目录。在`home_space`函数中，我们使用`find`列出每个主目录中的目录和普通文件。使用`wc`命令，我们计算找到的文件和目录的数量。

The really interesting thing about `home_space` is how we deal with the problem of superuser access. Notice that we test for the superuser with `id` and, according to the outcome of the test, we assign different strings to the variable `dir_list`, which becomes the list of words for the `for` loop that follows. This way, if an ordinary user runs the script, only his/her home directory will be listed.

​	`home_space`真正有趣的地方在于我们如何处理超级用户访问的问题。请注意，我们使用`id`测试超级用户，并根据测试的结果将不同的字符串赋给变量`dir_list`，它成为接下来的`for`循环的单词列表。这样，如果普通用户运行脚本，只有他/她的主目录会被列出。

Another function that can use a `for` loop is our unfinished `system_info` function. We can build it like this:

​	还有一个可以使用`for`循环的函数是我们未完成的`system_info`函数。我们可以这样构建它：

```bash
system_info() {
    # Find any release files in /etc

    if ls /etc/*release 1>/dev/null 2>&1; then
        echo "<h2>System release info</h2>"
        echo "<pre>"
        for i in /etc/*release; do

            # Since we can't be sure of the
            # length of the file, only
            # display the first line.

            head -n 1 "$i"
        done
        uname -orp
        echo "</pre>"
    fi

}   # end of system_info
```

In this function, we first determine if there are any release files to process. The release files contain the name of the vendor and the version of the distribution. They are located in the `/etc` directory. To detect them, we perform an `ls` command and throw away all of its output. We are only interested in the exit status. It will be true if any files are found.

​	在这个函数中，我们首先确定是否有任何需要处理的 release 文件。release 文件包含供应商的名称和发行版的版本。它们位于 `/etc` 目录中。为了检测它们，我们执行一个 `ls` 命令并丢弃其所有输出。我们只对退出状态感兴趣。如果找到任何文件，退出状态将为真。

Next, we output the HTML for this section of the page, since we now know that there are release files to process. To process the files, we start a `for` loop to act on each one. Inside the loop, we use the `head` command to return the first line of each file.

​	接下来，我们输出此页面部分的 HTML，因为现在我们知道有 release 文件要处理。要处理这些文件，我们开始一个 `for` 循环，对每个文件执行操作。在循环内部，我们使用 `head` 命令返回每个文件的第一行。

Finally, we use the `uname` command with the "o", "r", and "p" options to obtain some additional information from the system.

​	最后，我们使用 `uname` 命令和 "o"、"r" 和 "p" 选项从系统获取一些额外的信息。