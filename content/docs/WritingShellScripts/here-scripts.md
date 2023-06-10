+++
title = "here 脚本"
weight = 3
date = 2023-06-08T11:16:14+08:00
description = ""
isCJKLanguage = true
draft = false

+++

# here 脚本 - Here Scripts

https://linuxcommand.org/lc3_wss0030.php

Beginning with this lesson, we will construct a useful application. This application will produce an HTML document that contains information about our system. As we construct our script, we will discover step by step the tools needed to solve the problem at hand.

​	从这一课开始，我们将创建一个有用的应用程序。这个应用程序将生成一个包含关于我们系统信息的 HTML 文档。在编写脚本的过程中，我们将逐步发现解决问题所需的工具。

## 使用脚本编写 HTML 文件 Writing an HTML File with a Script

As we may be aware, a well formed HTML file contains the following content:

​	如我们所知，一个格式正确的 HTML 文件包含以下内容：

```html
<html>
<head>
    <title>
    The title of your page
    </title>
</head>

<body>
    Your page content goes here.
</body>
</html>
```

Now, with what we already know, we could write a script to produce the above content:

​	现在，根据我们已经了解的内容，我们可以编写一个脚本来生成上述内容：

```bash
#!/bin/bash

# sysinfo_page - A script to produce an html file

echo "<html>"
echo "<head>"
echo "  <title>"
echo "  The title of your page"
echo "  </title>"
echo "</head>"
echo ""
echo "<body>"
echo "  Your page content goes here."
echo "</body>"
echo "</html>"
```

This script can be used as follows:

​	这个脚本可以按以下方式使用：

```bash
[me@linuxbox me]$ sysinfo_page > sysinfo_page.html
```

It has been said that the greatest programmers are also the laziest. They write programs to save themselves work. Likewise, when clever programmers write programs, they try to save themselves typing.

​	有人说最优秀的程序员也是最懒惰的。他们编写程序来减少工作量。同样地，聪明的程序员编写程序时，会尽量减少键入的次数。

The first improvement to this script will be to replace the repeated use of the `echo` command with a single instance by using quotation more efficiently:

​	对于这个脚本的第一个改进是将重复使用的 `echo` 命令替换为一个单独的实例，通过更有效地使用引号：

```bash
#!/bin/bash

# sysinfo_page - A script to produce an HTML file

echo "<html>
 <head>
   <title>
   The title of your page
   </title>
 </head>
 
 <body>
   Your page content goes here.
 </body>
 </html>"
```

Using quotation, it is possible to embed carriage returns in our text and have the `echo` command's argument span multiple lines.

​	使用引号，我们可以在文本中嵌入换行符，并且 `echo` 命令的参数可以跨多行。

While this is certainly an improvement, it does have a limitation. Since many types of markup used in HTML incorporate quotation marks themselves, it makes using a quoted string a little awkward. A quoted string can be used but each embedded quotation mark will need to be escaped with a backslash character.

​	尽管这无疑是一个改进，但它也有局限性。由于在 HTML 中使用了许多引号，因此使用引号字符串会有些不方便。可以使用引号字符串，但是每个嵌入的引号都需要用反斜杠字符进行转义。

In order to avoid the additional typing, we need to look for a better way to produce our text. Fortunately, the shell provides one. It's called a *here script*.

​	为了避免额外的键入，我们需要寻找一种更好的方式来生成我们的文本。幸运的是，shell 提供了一种方式，它被称为*here 脚本*。

```bash
#!/bin/bash

# sysinfo_page - A script to produce an HTML file

cat << _EOF_
<html>
<head>
    <title>
    The title of your page
    </title>
</head>

<body>
    Your page content goes here.
</body>
</html>
_EOF_
```

A here script (also sometimes called a here document) is an additional form of [I/O redirection](https://linuxcommand.org/lc3_lts0070.php). It provides a way to include content that will be given to the standard input of a command. In the case of the script above, the standard input of the `cat` command was given a stream of text from our script.

​	here 脚本（有时也称为 here document）是一种附加的[输入/输出重定向]({{< ref "/docs/LearnTheShell/i-o-redirection">}})形式。它提供了一种将内容提供给命令的标准输入的方法。在上面的脚本中，`cat` 命令的标准输入接收了来自我们脚本的文本流。

A here script is constructed like this:

​	here 脚本的结构如下：

```bash
command << token
content to be used as command's standard input
token
```

*token* can be any string of characters. "`_EOF_`" (EOF is short for "End Of File") is traditional, but we can use anything as long as it does not conflict with a bash reserved word. The token that ends the here script must exactly match the one that starts it, or else the remainder of our script will be interpreted as more standard input to the command which can lead to some really exciting script failures.

​	*token* 可以是任意字符字符串。"`_EOF_`"（EOF 是 "End Of File" 的缩写）是传统用法，但我们可以使用任何字符串，只要它不与 bash 的保留词冲突即可。结束 here 文档的标记必须与开始的标记完全匹配，否则我们脚本的其余部分将被解释为更多的命令标准输入，这可能导致一些令人激动的脚本失败。

There is one additional trick that can be used with a here script. Often, we might want to indent the content portion of the here script to improve the readability of the script. We can do this if we change the script as follows:

​	还有一种可以与 here 脚本一起使用的额外技巧。通常，我们可能希望对 here 脚本的内容进行缩进，以提高脚本的可读性。如果我们将脚本更改如下所示，就可以实现这一点：

```bash
#!/bin/bash

# sysinfo_page - A script to produce an HTML file

cat <<- _EOF_
    <html>
    <head>
        <title>
        The title of your page
        </title>
    </head>

    <body>
        Your page content goes here.
    </body>
    </html>
_EOF_
```

Changing the "`<<`" to "`<<-`" causes bash to ignore the leading tabs (but not spaces) in the here script. The output from the cat command will not contain any of the leading tab characters. This technique is a bit problematic, as many text editors are configured (and desirably so) to use sequences of spaces rather than tab characters.

​	将 "`<<`" 改为 "`<<-`"，会让 bash 忽略 here 脚本中的前导制表符（但不包括空格）。`cat` 命令的输出将不包含任何前导制表符。这种技巧有些问题，因为许多文本编辑器被配置为（并且应该如此）使用一系列空格而不是制表符。

O.k., let's make our page. We will edit our page to get it to say something:

​	好的，让我们制作我们的页面。我们将编辑我们的页面，使其显示一些信息：

```bash
#!/bin/bash

# sysinfo_page - A script to produce an HTML file

cat <<- _EOF_
    <html>
    <head>
        <title>
        My System Information
        </title>
    </head>

    <body>
    <h1>My System Information</h1>
    </body>
    </html>
_EOF_
```

In our next lesson, we will make our script produce some real information about the system.

​	在我们的下一课中，我们将让我们的脚本生成一些关于系统的真实信息。