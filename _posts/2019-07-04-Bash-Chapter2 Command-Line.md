---
layout: post
title:  "Chapter2 Command-Line"
date:   2019-07-04 23:50:12 +0800
categories: Bash
tags: Command-Line  vi
---

* content
{:toc}

# Chapter 2. Command-Line Editing
This is exactly what bash allows you to do. It has editing modes that allow you to **edit command lines with editing commands** similar to those of the two most popular UNIX editors, `vi` and `emacs`. It also provides a much-extended analog to the C shell history mechanism called `fc` (for fix command) that, among other things, allows you to use your favorite editor directly for editing your command lines. To round things out, bash also provides the original C shell history mechanism.

bash initially starts interactively with emacs-mode as the default, First, you can use the set command:
```bash
$ set -o emacs

or:

$ set -o vi
```
The second way of selecting the editing mode is to set a readline variable in the file  `.inputrc`. 

## vi Editing Mode

**Editing commands in vi input mode**

|Command|Description|
|:--:|:--:|
|DEL|Delete previous character|
|CTRL-W|Erase previous word (i.e., erase until a blank)|
|CTRL-V|Quote the next character|
|ESC|Enter control mode (see below)|

**Basic vi control mode commands**
|Command|Description|
|:--:|:--:|
|h|Move left one character|
|l|Move right one character|
|w|Move right one word|
|b|Move left one word|
|W|Move to beginning of next non-blank word|
|B|Move to beginning of preceding non-blank word|
|e|Move to end of current word|
|E|Move to end of current non-blank word|
|0|Move to beginning of line|
|^|Move to first non-blank character in line|
|$|Move to end of line|

**Commands for entering vi input mode**
|Command|Description|
|:--:|:--:|
|i|Text inserted before current character (insert)|
|a|Text inserted after current character (append)|
|I|Text inserted at beginning of line|
|A|Text inserted at end of line|
|R|Text overwrites existing text|

**Abbreviations for vi-mode delete commands**
|Command|Description|
|:--:|:--:|
|D|Equivalent to d$ (delete to end of line)|
|dd|Equivalent to 0d$ (delete entire line)|
|C|Equivalent to c$ (delete to end of line, enter input mode)|
|cc|Equivalent to 0c$ (delete entire line, enter input mode)|
|X|Equivalent to dl (delete character backwards)|
|x|Equivalent to dh (delete character forwards)|

**vi control mode commands for searching the command history**
|Command|Description|
|:--:|:--:|
|k or -|Move backward one line|
|j or +|Move forward one line|
|G|Move to line given by repeat count|
|/string|Search backward for string|
|?string|Search forward for string|
|n|Repeat search in same direction as previous|
|N|Repeat search in opposite direction of previous|

** Miscellaneous vi-mode commands**
|Command|Description|
|:--:|:--:|
|~|Invert (twiddle) case of current character(s)|
|-|Append last word of previous command, enter input mode|
|CTRL-L|Clear the screen and redraw the current line on it; good for when your screen becomes garbled|
|#|Prepend # (comment character) to the line and send it to the history list; useful for saving a command to be executed later without having to retype it|

## The readline Startup File
The default startup file is called `.inputrc` and must exist in your home directory if you wish to customize readline. You can change the default filename by setting the environment variable `INPUTRC`