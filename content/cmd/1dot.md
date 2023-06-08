+++
title = "1dot"
date = 2023-06-08T14:23:23+08:00
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++
https://linuxcommand.org/lc3_man_pages/doth.html

NAME
. - Execute commands from a file in the current shell.

SYNOPSIS
. filename [arguments]

DESCRIPTION
Execute commands from a file in the current shell.

    Read and execute commands from FILENAME in the current shell.  The
    entries in $PATH are used to find the directory containing FILENAME.
    If any ARGUMENTS are supplied, they become the positional parameters
    when FILENAME is executed.
    
    Exit Status:
    Returns the status of the last command executed in FILENAME; fails if
    FILENAME cannot be read.

SEE ALSO
bash(1)

IMPLEMENTATION
GNU bash, version 5.0.17(1)-release (x86_64-redhat-linux-gnu)
Copyright (C) 2019 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
