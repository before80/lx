+++
title = "导览"
date = 2023-06-08T10:55:09+08:00
weight = 4
description = ""
isCJKLanguage = true
draft = false

+++

# 导览 - A Guided Tour

https://linuxcommand.org/lc3_lts0040.php

It's time to take our tour. The table below lists some interesting places to explore. This is by no means a complete list, but it should prove to be an interesting adventure. For each of the directories listed below, do the following:

​	是时候开始我们的导览了。下面的表格列出了一些有趣的探索地点。这并不是一个完整的列表，但应该会是一次有趣的冒险。对于下面列出的每个目录，执行以下操作： 

- `cd` into each directory.
- 使用 `cd` 进入每个目录。
- Use `ls` to list the contents of the directory.
- 使用 `ls` 列出目录的内容。
- If there is an interesting file, use the `file` command to determine its contents.
- 如果有一个有趣的文件，请使用 `file` 命令确定其内容。
- For text files, use `less` to view them.
- 对于文本文件，使用 `less` 查看它们。

| **目录 Directory** | **描述 Description**                                         |
| :----------------- | :----------------------------------------------------------- |
| `/`                | 文件系统开始的根目录。根目录可能只包含子目录。<br />The root directory where the file system begins. The root directory will probably contain only subdirectories. |
| `/boot`            | Linux 内核和引导加载程序文件存放的地方。内核是一个名为 `vmlinuz` 的文件。<br />This is where the Linux kernel and boot loader files are kept. The kernel is a file called `vmlinuz`. |
| `/etc`             | `/etc` 目录包含系统的配置文件。`/etc` 中的所有文件都应该是文本文件。一些感兴趣的地方有：`/etc/passwd` `passwd` 文件包含每个用户的基本信息。这是定义用户帐户的地方。`/etc/fstab` `fstab` 文件包含系统引导时挂载的设备表。此文件定义系统的磁盘驱动器。`/etc/hosts` 此文件列出系统内在本质上已知的网络主机名和 IP 地址。`/etc/init.d` 此目录包含在引导时启动各种系统服务的脚本。<br />The `/etc` directory contains the configuration files for the system. All of the files in `/etc` should be text files. Some points of interest are:`/etc/passwd`The `passwd` file contains the essential information for each user. This is where user accounts are defined.`/etc/fstab`The `fstab` file contains a table of devices that get mounted when the system boots. This file defines the system's disk drives.`/etc/hosts`This file lists the network host names and IP addresses that are intrinsically known to the system.`/etc/init.d`This directory contains the scripts that start various system services at boot time. |
| `/bin, /usr/bin`   | 这两个目录包含系统的大多数程序。`/bin` 目录包含系统运行所需的基本程序，而 `/usr/bin` 包含系统用户的应用程序。<br />These two directories contain most of the programs for the system. The `/bin` directory has the essential programs that the system requires to operate, while `/usr/bin` contains applications for the system's users. |
| `/sbin, /usr/sbin` | `sbin` 目录包含系统管理的程序，主要供超级用户使用。<br />The `sbin` directories contain programs for system administration, mostly for use by the superuser. |
| `/usr`             | `/usr` 目录包含支持用户应用程序的各种内容。一些亮点：`/usr/share/X11` X Window 系统的支持文件。`/usr/share/dict` 拼写检查器的词典。是的，Linux 自带拼写检查器。参见 [`look`](https://linuxcommand.org/lc3_man_pages/look1.html) 和 [`aspell`](https://linuxcommand.org/lc3_man_pages/aspell1.html)。`/usr/share/doc` 各种格式的文档文件。`/usr/share/man` 手册页保存在这里。The `/usr` directory contains a variety of things that support user applications. Some highlights:`/usr/share/X11`Support files for the X Window system`/usr/share/dict`Dictionaries for the spelling checker. Yes, Linux comes with a spelling checker. See [`look`](https://linuxcommand.org/lc3_man_pages/look1.html) and [`aspell`](https://linuxcommand.org/lc3_man_pages/aspell1.html).`/usr/share/doc`Various documentation files in a variety of formats.`/usr/share/man`The man pages are kept here. |
| `/usr/local`       | `/usr/local` 及其子目录用于安装本地机器上的软件和其他文件。这实际上意味着不是官方发行版的软件（通常放在 `/usr/bin` 中）放在这里。当您找到有趣的程序要安装到系统上时，它们应该安装在其中一个 `/usr/local` 目录中。最常选择的目录是 `/usr/local/bin`。<br />`/usr/local` and its subdirectories are used for the installation of software and other files for use on the local machine. What this really means is that software that is not part of the official distribution (which usually goes in `/usr/bin`) goes here.  When you find interesting programs to install on your system, they should be installed in one of the `/usr/local` directories. Most often, the directory of choice is `/usr/local/bin`. |
| `/var`             | `/var` 目录包含随系统运行而发生变化的文件。包括：`/var/log` 包含日志文件的目录。这些文件在系统运行时更新。定期查看此目录中的文件是监视系统健康状况的一个好办法。`/var/spool` 此目录用于保存排队等待某些进程处理的文件，例如邮件和打印作业。当用户的邮件首次到达本地系统时（假设它具有本地邮件，在现代非邮件服务器的计算机上很少见），消息首先存储在 `/var/spool/mail` 中。<br />The `/var` directory contains files that change as the system is running. This includes:`/var/log`Directory that contains log files. These are updated as the system runs. It's a good idea to view the files in this directory from time to time, to monitor the health of your system.`/var/spool`This directory is used to hold files that are queued for some process, such as mail messages and print jobs. When a user's mail first arrives on the local system (assuming it has local mail, a rare occurrence on modern machines that are not mail servers), the messages are first stored in `/var/spool/mail` |
| `/lib`             | 共享库（类似于其他操作系统中的 DLL）存放在这里。<br />The shared libraries (similar to DLLs in that other operating system) are kept here. |
| `/home`            | `/home` 是用户存储个人工作的地方。通常情况下，这是用户唯一被允许写入文件的地方。这使得事情保持整洁 :-)<br />`/home` is where users keep their personal work. In general, this is the only place users are allowed to write files. This keeps things nice and clean :-) |
| `/root`            | 超级用户的主目录。<br />This is the superuser's home directory. |
| `/tmp`             | `/tmp` 是程序可以写入其临时文件的目录。<br />`/tmp` is a directory in which programs can write their temporary files. |
| `/dev`             | `/dev` 目录是一个特殊目录，因为它并不真正包含通常意义上的文件。相反，它包含系统可用的设备。在 Linux（类似于 Unix）中，设备被视为文件。您可以像处理文件一样读取和写入设备。例如 `/dev/fd0` 是第一个软盘驱动器，`/dev/sda` 是第一个硬盘驱动器。内核理解的所有设备都在这里表示。<br />The `/dev` directory is a special directory, since it does not really contain files in the usual sense. Rather, it contains devices that are available to the system. In Linux (like Unix), devices are treated like files. You can read and write devices as though they were files. For example `/dev/fd0` is the first floppy disk drive, `/dev/sda` is the first hard drive. All the devices that the kernel understands are represented here. |
| `/proc`            | `/proc` 目录也是特殊的。这个目录不包含文件。事实上，这个目录根本不存在。它完全是虚拟的。`/proc` 目录包含了对内核本身的小窥视孔。这个目录中有一组编号的条目，对应着系统上运行的所有进程。此外，还有许多命名条目允许访问当前系统配置。可以查看其中的许多条目。尝试查看 `/proc/cpuinfo`。这个条目将告诉您内核对系统的 CPU 的看法。<br />The `/proc` directory is also special. This directory does not contain files. In fact, this directory does not really exist at all. It is entirely virtual. The `/proc` directory contains little peep holes into the kernel itself. There are a group of numbered entries in this directory that correspond to all the processes running on the system. In addition, there are a number of named entries that permit access to the current configuration of the system. Many of these entries can be viewed. Try viewing `/proc/cpuinfo`. This entry will tell you what the kernel thinks of the system's CPU. |
| `/media`           | 最后，我们来到 `/media`，这是一个普通的目录，以特殊的方式使用。`/media` 目录用于 *挂载点*。就像我们在[第二课](https://linuxcommand.org/lc3_lts0020.php)中学到的那样，不同的物理存储设备（如硬盘驱动器）附加到文件系统树的不同位置。将设备附加到树上的过程称为 *挂载*。要使设备可用，必须首先将其挂载。当您的系统启动时，它会读取 `/etc/fstab` 文件中的挂载指令列表，该文件描述了哪个设备挂载在目录树的哪个挂载点上。这处理了硬盘驱动器，但我们还可以拥有被认为是临时的设备，如光盘和 USB 存储设备。由于这些是可移动的，它们不会一直保持挂载状态。`/media` 目录由现代面向桌面的 Linux 发行版中的自动设备挂载机制使用。要查看使用的设备和挂载点，请键入 [`mount`](https://linuxcommand.org/lc3_man_pages/mount8.html)。<br />Finally, we come to `/media`, a normal directory which is used in a special way. The `/media` directory is used for *mount points*. As we learned in the [second lesson](https://linuxcommand.org/lc3_lts0020.php), the different physical storage devices (like hard disk drives) are attached to the file system tree in various places. This process of attaching a device to the tree is called *mounting*. For a device to be available, it must first be mounted.  When your system boots, it reads a list of mounting instructions in the `/etc/fstab` file, which describes which device is mounted at which mount point in the directory tree. This takes care of the hard drives, but we may also have devices that are considered temporary, such as optical disks and USB storage devices. Since these are removable, they do not stay mounted all the time. The `/media` directory is used by the automatic device mounting mechanisms found in modern desktop oriented Linux distributions. To see what devices and mount points are used, type [`mount`](https://linuxcommand.org/lc3_man_pages/mount8.html). |

> ​	A weird kind of file...
>
> ​	一种奇怪的文件...
>
> During your tour, you probably noticed a strange kind of directory entry, particularly in the `/lib` directory. When listed with `ls -l`, you might have seen something like this:
>
> ​	在你的导览中，你可能注意到了一种奇怪的目录条目，尤其是在 `/lib` 目录中。当用 `ls -l` 列出时，你可能会看到像这样的东西：
>
> ```
> lrwxrwxrwx     25 Jul  3 16:42 System.map -> /boot/System.map-4.0.36-3
> -rw-r--r-- 105911 Oct 13  2018 System.map-4.0.36-0.7
> -rw-r--r-- 105935 Dec 29  2018 System.map-4.0.36-3
> -rw-r--r-- 181986 Dec 11  2019 initrd-4.0.36-0.7.img
> -rw-r--r-- 182001 Dec 11  2019 initrd-4.0.36.img
> lrwxrwxrwx     26 Jul  3 16:42 module-info -> /boot/module-info-4.0.36-3
> -rw-r--r--  11773 Oct 13  2018 module-info-4.0.36-0.7
> -rw-r--r--  11773 Dec 29  2018 module-info-4.0.36-3
> lrwxrwxrwx     16 Dec 11  2019 vmlinuz -> vmlinuz-4.0.36-3
> -rw-r--r-- 454325 Oct 13  2018 vmlinuz-4.0.36-0.7
> -rw-r--r-- 454434 Dec 29  2018 vmlinuz-4.0.36-3
> ```
>
> Notice the files, `System.map, module-info` and `vmlinuz`. See the strange notation after the file names?
>
> 注意文件 `System.map`、`module-info` 和 `vmlinuz`。看到文件名后面的奇怪符号了吗？
>
> Files such as this are called *symbolic links*. Symbolic links are a special type of file that points to another file. With symbolic links, it is possible for a single file to have multiple names. Here's how it works: Whenever the system is given a file name that is a symbolic link, it transparently maps it to the file it is pointing to.
>
> ​	此类文件称为 *符号链接*。符号链接是一种特殊类型的文件，它指向另一个文件。通过符号链接，一个文件可以有多个名称。它的工作原理如下：每当系统获得一个符号链接的文件名时，它会自动地将其映射到指向的文件。
>
> Just what is this good for? This is a very handy feature. Let's consider the directory listing above (which is the `/boot` directory of an old system). This system has had multiple versions of the Linux kernel installed. We can see this from the files `vmlinuz-4.0.36-0.7` and `vmlinuz-4.0.36-3`. These file names suggest that both version 4.0.36-0.7 and 4.0.36-3 are installed. Because the file names contain the version it is easy to see the differences in the directory listing. However, this would be confusing to programs that rely on a fixed name for the kernel file. These programs might expect the kernel to simply be called `"vmlinuz"`. Here is where the beauty of the symbolic link comes in. By creating a symbolic link called `vmlinuz` that points to `vmlinuz-4.0.36-3`, we have solved the problem.
>
> ​	这有什么用呢？这是一个非常方便的功能。让我们来看看上面的目录列表（这是一个旧系统的 `/boot` 目录）。该系统安装了多个版本的 Linux 内核，我们可以从文件 `vmlinuz-4.0.36-0.7` 和 `vmlinuz-4.0.36-3` 看出这一点。这些文件名表明安装了版本 4.0.36-0.7 和 4.0.36-3。由于文件名包含版本信息，因此很容易在目录列表中看到它们之间的差异。然而，对于依赖于固定名称的内核文件的程序来说，这可能会造成困惑。这些程序可能期望内核简单地称为 `"vmlinuz"`。这就是符号链接的优点所在。通过创建一个名为 `vmlinuz` 的符号链接，它指向 `vmlinuz-4.0.36-3`，我们解决了这个问题。
>
> To create symbolic links, we use the [`ln`](https://linuxcommand.org/lc3_man_pages/ln1.html) command.
>
> ​	要创建符号链接，我们使用 `ln` 命令。

## 进一步阅读 Further Reading

- To learn more about the organization of the Linux filesystem, consult the [Filesystem Hierarchy Standard](https://refspecs.linuxfoundation.org/fhs.shtml)
- 要了解更多关于 Linux 文件系统组织的信息，请参阅[文件系统层次结构标准（Filesystem Hierarchy Standard）](https://refspecs.linuxfoundation.org/fhs.shtml)。