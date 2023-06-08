+++
title = "权限"
date = 2023-06-08T10:56:17+08:00
weight = 9
description = ""
isCJKLanguage = true
draft = false

+++

# 权限 Permissions

https://linuxcommand.org/lc3_lts0090.php

The Unix-like operating systems, such as Linux differ from other computing systems in that they are not only *multitasking* but also *multi-user*.

​	类Unix操作系统（如Linux）与其他计算系统的不同之处在于它们不仅支持*多任务*，还支持*多用户*。

What exactly does this mean? It means that more than one user can be operating the computer at the same time. While a desktop or laptop computer only has one keyboard and monitor, it can still be used by more than one user. For example, if the computer is attached to a network, or the Internet, remote users can log in via `ssh` (secure shell) and operate the computer. In fact, remote users can execute graphical applications and have the output displayed on a remote computer. The X Window system supports this.

​	这到底意味着什么呢？这意味着多个用户可以同时操作计算机。虽然桌面或笔记本电脑只有一个键盘和显示器，但它仍然可以被多个用户使用。例如，如果计算机连接到网络或互联网，远程用户可以通过`ssh`（安全外壳）登录并操作计算机。实际上，远程用户可以执行图形应用程序，并在远程计算机上显示输出。X Window系统支持此功能。

The multi-user capability of Unix-like systems is a feature that is deeply ingrained into the design of the operating system. If we remember the environment in which Unix was created, this makes perfect sense. Years ago before computers were "personal," they were large, expensive, and centralized. A typical university computer system consisted of a large mainframe computer located in some building on campus and *terminals* were located throughout the campus, each connected to the large central computer. The computer would support many users at the same time.

​	类Unix系统的多用户能力是操作系统设计中根深蒂固的特性。如果我们记得Unix创建的环境，这是完全合理的。多年前，在计算机成为“个人电脑”之前，它们是庞大、昂贵和集中化的。典型的大学计算机系统由位于校园某个建筑物中的大型主机计算机组成，而*终端*则分布在整个校园，每个终端连接到大型中央计算机。计算机可以同时支持多个用户。

In order to make this practical, a method had to be devised to protect the users from each other. After all, we wouldn't want the actions of one user to crash the computer, nor would we allow one user to interfere with the files belonging to another user.

​	为了使这种情况变得实际可行，必须设计一种方法来保护用户免受彼此的干扰。毕竟，我们不希望一个用户的操作导致计算机崩溃，也不希望一个用户干扰其他用户的文件。

This lesson will cover the following commands:

​	本课程将涵盖以下命令： 

- `chmod` - modify file access rights
- `su` - temporarily become the superuser
- `sudo` - temporarily become the superuser
- `chown` - change file ownership
- `chgrp` - change a file's group ownership
- `chmod` - 修改文件访问权限
- `su` - 临时成为超级用户
- `sudo` - 临时成为超级用户
- `chown` - 更改文件所有者
- `chgrp` - 更改文件所属组

## 文件权限 File Permissions

On a Linux system, each file and directory is assigned access rights for the owner of the file, the members of a group of related users, and everybody else. Rights can be assigned to read a file, to write a file, and to execute a file (i.e., run the file as a program).

​	在Linux系统上，每个文件和目录都分配了对文件所有者、相关用户组的成员以及其他所有人的访问权限。可以分配的权限包括读取文件、写入文件和执行文件（即将文件作为程序运行）。

To see the permission settings for a file, we can use the `ls` command. As an example, we will look at the `bash` program which is located in the `/bin` directory:

​	要查看文件的权限设置，可以使用`ls`命令。例如，我们将查看位于`/bin`目录中的`bash`程序：

```bash
[me@linuxbox me]$ ls -l /bin/bash
-rwxr-xr-x 1 root root 1113504 Jun  6  2019 /bin/bash
```

Here we can see:

​	在这里我们可以看到： 

- The file "/bin/bash" is owned by user "root"
- The superuser has the right to read, write, and execute this file
- The file is owned by the group "root"
- Members of the group "root" can also read and execute this file
- Everybody else can read and execute this file
- 文件“/bin/bash”归属于用户“root”
- 超级用户具有读取、写入和执行此文件的权限
- 文件归属于组“root”
- 组“root”的成员也可以读取和执行此文件
- 其他所有人可以读取和执行此文件

In the diagram below, we see how the first portion of the listing is interpreted. It consists of a character indicating the file type, followed by three sets of three characters that convey the reading, writing and execution permission for the owner, group, and everybody else.

​	在下面的图表中，我们可以看到如何解释列表的第一部分。它由一个表示文件类型的字符组成，后面跟着三组三个字符，分别表示所有者、组和其他所有人的读取、写入和执行权限。

![permissions diagram](permissions_img/file_permissions.png)



## chmod

The `chmod` command is used to change the permissions of a file or directory. To use it, we specify the desired permission settings and the file or files that we wish to modify. There are two ways to specify the permissions. In this lesson we will focus on one of these, called the *octal notation* method.

​	`chmod`命令用于更改文件或目录的权限。要使用它，我们指定所需的权限设置以及要修改的文件或文件夹。有两种方法可以指定权限。在本课程中，我们将重点介绍其中之一，称为*八进制表示法*方法。

It is easy to think of the permission settings as a series of bits (which is how the computer thinks about them). Here's how it works:

​	将权限设置视为一系列位（计算机对其的思考方式）是很容易的。它的工作原理如下：

```
rwx rwx rwx = 111 111 111
rw- rw- rw- = 110 110 110
rwx --- --- = 111 000 000

and so on...

rwx = 111 in binary = 7
rw- = 110 in binary = 6
r-x = 101 in binary = 5
r-- = 100 in binary = 4
```

Now, if we represent each of the three sets of permissions (owner, group, and other) as a single digit, we have a pretty convenient way of expressing the possible permissions settings. For example, if we wanted to set `some_file` to have read and write permission for the owner, but wanted to keep the file private from others, we would:

​	现在，如果我们将每组权限（所有者、组和其他所有人）表示为一个数字，我们就有了一种非常方便的表达可能的权限设置的方法。例如，如果我们希望将`some_file`的所有者权限设置为读取和写入，但希望将文件保密不让其他人看到，我们可以执行以下命令：

```bash
[me@linuxbox me]$ chmod 600 some_file
```

Here is a table of numbers that covers all the common settings. The ones beginning with "7" are used with programs (since they enable execution) and the rest are for other kinds of files.

​	下表列出了涵盖所有常见设置的数字。以“7”开头的数字用于程序（因为它们允许执行），其余数字用于其他类型的文件。

| **值 Value** | **含义 Meaning**                                             |
| ------------ | ------------------------------------------------------------ |
| **777**      | **(rwxrwxrwx)** 权限无限制。任何人都可以做任何事。通常不是一个理想的设置。<br />**(rwxrwxrwx)** No restrictions on permissions. Anybody may do anything. Generally not a desirable setting. |
| **755**      | **(rwxr-xr-x)** 文件所有者可以读取、写入和执行该文件。其他所有人可以读取和执行该文件。此设置常用于所有用户使用的程序。<br />**(rwxr-xr-x)** The file's owner may read, write, and execute the file. All others may read and execute the file. This setting is common for programs that are used by all users. |
| **700**      | **(rwx------)** 文件所有者可以读取、写入和执行该文件。其他人没有任何权限。此设置对于仅所有者使用且必须保密的程序很有用。<br />**(rwx------)** The file's owner may read, write, and execute the file. Nobody else has any rights. This setting is useful for programs that only the owner may use and must be kept private from others. |
| **666**      | **(rw-rw-rw-)** 所有用户都可以读取和写入该文件。<br />**(rw-rw-rw-)** All users may read and write the file. |
| **644**      | **(rw-r--r--)** 文件所有者可以读取和写入文件，而其他所有人只能读取该文件。这是一个常见的设置，用于任何人都可以读取但只有所有者可以更改的数据文件。<br />**(rw-r--r--)** The owner may read and write a file, while all others may only read the file. A common setting for data files that everybody may read, but only the owner may change. |
| **600**      | **(rw-------)** 文件所有者可以读取和写入该文件。其他所有人没有任何权限。这是一个常见的设置，用于所有者希望保密的数据文件。<br />**(rw-------)** The owner may read and write a file. All others have no rights. A common setting for data files that the owner wants to keep private. |

## 目录权限 Directory Permissions

The `chmod` command can also be used to control the access permissions for directories. Again, we can use the octal notation to set permissions, but the meaning of the r, w, and x attributes is different:

​	`chmod`命令也可以用于控制目录的访问权限。同样，我们可以使用八进制表示法来设置权限，但是r、w和x属性的含义有所不同： 

- **r** - Allows the contents of the directory to be listed if the x attribute is also set.
- **w** - Allows files within the directory to be created, deleted, or renamed if the x attribute is also set.
- **x** - Allows a directory to be entered (i.e. `cd dir`).
- **r** - 如果也设置了x属性，则允许列出目录的内容。
- **w** - 如果也设置了x属性，则允许在目录中创建、删除或重命名文件。
- **x** - 允许进入目录（即`cd dir`）。

Here are some useful settings for directories:

​	以下是一些有用的目录设置：

| **值 Value** | **含义 Meaning**                                             |
| :----------- | :----------------------------------------------------------- |
| **777**      | **(rwxrwxrwx)** 权限无限制。任何人都可以列出文件、在目录中创建新文件和删除目录中的文件。通常不是一个好的设置。<br />**(rwxrwxrwx)** No restrictions on permissions. Anybody may list files, create new files in the directory and delete files in the directory. Generally not a good setting. |
| **755**      | **(rwxr-xr-x)** 目录所有者具有完全访问权限。其他人可以列出目录，但无法创建文件或删除文件。此设置适用于希望与其他用户共享的目录。<br />**(rwxr-xr-x)** The directory owner has full access. All others may list the directory, but cannot create files nor delete them. This setting is common for directories that you wish to share with other users. |
| **700**      | **(rwx------)** 目录所有者具有完全访问权限。其他人没有任何权限。此设置对于只有所有者可以使用且必须保密的目录很有用。<br />**(rwx------)** The directory owner has full access. Nobody else has any rights. This setting is useful for directories that only the owner may use and must be kept private from others. |

## 暂时成为超级用户 Becoming the Superuser for a Short While

It is often necessary to become the superuser to perform important system administration tasks, but as we know, we should not stay logged in as the superuser. In most distributions, there is a program that can give you temporary access to the superuser's privileges. This program is called `su` (short for substitute user) and can be used in those cases when you need to be the superuser for a small number of tasks. To become the superuser, simply type the `su` command. You will be prompted for the superuser's password:

​	经常需要成为超级用户执行重要的系统管理任务，但我们知道，不应该作为超级用户保持登录状态。在大多数发行版中，有一个程序可以让您暂时获得超级用户权限。该程序称为`su`（替代用户的简称），可以在需要成为超级用户执行一小部分任务的情况下使用。要成为超级用户，只需输入`su`命令。您将被要求输入超级用户的密码：

```bash
[me@linuxbox me]$ su
Password:
[root@linuxbox me]#
```

After executing the `su` command, we have a new shell session as the superuser. To exit the superuser session, type `exit` and we will return to your previous session.

​	执行`su`命令后，我们将获得一个新的超级用户shell会话。要退出超级用户会话，输入`exit`，然后将返回到以前的会话。

In most modern distributions, an alternate method is used. Rather than using `su`, these systems employ the `sudo` command instead. With `sudo`, one or more users are granted superuser privileges on an as needed basis. To execute a command as the superuser, the desired command is simply preceded with the `sudo` command. After the command is entered, the user is prompted for the their own password rather than the superuser's:

​	在大多数现代发行版中，使用了另一种方法。这些系统使用`sudo`命令而不是`su`。使用`sudo`，一个或多个用户被授予根据需要的超级用户权限。要以超级用户身份执行命令，只需在所需的命令之前加上`sudo`命令。输入命令后，用户将被要求输入自己的密码，而不是超级用户的密码：

```bash
[me@linuxbox me]$ sudo some_command
Password for me:
[me@linuxbox me]$
```

In fact, modern distributions don't even set the root account password thus making it impossible to log in as the root user. A root shell is still possible with `sudo` by using the "-i" option:

​	实际上，现代发行版甚至不设置root帐户密码，因此无法作为root用户登录。通过使用"-i"选项，仍然可以使用`sudo`命令获得完全的root shell：

```bash
[me@linuxbox me]$ sudo -i
Password for me:
root@linuxbox:~#
```



## 更改文件所有者和组 Changing File Ownership

We can change the owner of a file by using the `chown` command. Here's an example: Suppose we wanted to change the owner of `some_file` from "me" to "you". We could:

​	我们可以使用`chown`命令来更改文件的所有者。下面是一个例子：假设我们想将`some_file`的所有者从"me"改为"you"，我们可以执行以下命令：

```bash
[me@linuxbox me]$ sudo chown you some_file
```

Notice that in order to change the owner of a file, we must have superuser privileges. To do this, our example employed the `sudo` command to execute `chown`.

​	请注意，为了更改文件的所有者，我们必须具有超级用户权限。在这个例子中，我们使用`sudo`命令来执行`chown`命令。

`chown` works the same way on directories as it does on files.

​	`chown`命令对目录的操作方式与对文件的操作方式相同。

## 更改组所有权 Changing Group Ownership

The group ownership of a file or directory may be changed with `chgrp`. This command is used like this:

​	可以使用`chgrp`命令来更改文件或目录的组所有者。命令的使用方法如下：

```bash
[me@linuxbox me]$ chgrp new_group some_file
```

In the example above, we changed the group ownership of `some_file` from its previous group to "new_group". We must be the owner of the file or directory to perform a `chgrp`.

​	在上面的示例中，我们将`some_file`的组所有者从原来的组更改为"new_group"。执行`chgrp`命令时，我们必须是文件或目录的所有者。

## 进一步阅读 Further Reading

- Chapter 9 of [*The Linux Command Line*](https://linuxcommand.org/tlcl.php) covers this topic in much more detail.
- [*The Linux Command Line*](https://linuxcommand.org/tlcl.php)的第9章详细介绍了这个主题。