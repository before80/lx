+++
title = "3dot"
date = 2023-06-08T14:22:51+08:00
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++
https://linuxcommand.org/lc3_man_pages/dbl_parensh.html

NAME
(( ... )) - Evaluate arithmetic expression.

SYNOPSIS
(( expression ))

DESCRIPTION
Evaluate arithmetic expression.

    The EXPRESSION is evaluated according to the rules for arithmetic
    evaluation.  Equivalent to "let EXPRESSION".
    
    Exit Status:
    Returns 1 if EXPRESSION evaluates to 0; returns 0 otherwise.

SEE ALSO
bash(1)

IMPLEMENTATION
GNU bash, version 5.0.17(1)-release (x86_64-redhat-linux-gnu)
Copyright (C) 2019 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>