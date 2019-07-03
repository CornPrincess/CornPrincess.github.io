---
layout: post
title:  "Chapter 1. Bash Basics"
date:   2019-07-03 23:50:12 +0800
categories: Bash
tags: File  
---

* content
{:toc}

## What is a Shell
A shell is any user interface to the UNIX operating system, i.e., any program that takes input from the user, translates it into instructions that the operating system can understand, and conveys the operating system's output back to the user. 
![image.png-59.6kB][1]

There are various types of user interfaces. bash belongs to the most common category, known as `character-based user interfaces`.

Remember that the shell itself is not UNIX **just the user interface to it**. UNIX is one of the first operating systems to make the user interface independent of the operating system.

## History of UNIX Shells
The first major shell was the **Bourne shell** (named after its inventor, Steven Bourne); it was included in the first popular version of UNIX, Version 7, starting in 1979. 

The first widely used alternative shell was the **C shell**, or **csh**. This was written by Bill Joy at the University of California at Berkeley as part of the Berkeley Software Distribution (BSD) version of UNIX that came out a couple of years after Version 7.

In recent years a number of other shells have become popular. The most notable of these is the **Korn shell**. This shell is a commercial product that incorporates the best features of the Bourne and C shells, plus many features of its own.

The **Bourne Again shell** (named in punning tribute to Steve Bourne's shell) was created for use in the GNU project.[2] The GNU project was started by Richard Stallman of the Free Software Foundation (FSF) for the purpose of creating a UNIX-compatible operating system and replacing all of the commercial UNIX utilities with freely distributable ones. 

bash, intended to be the standard shell for the GNU system, was officially "born" on Sunday, January 10, 1988. Brian Fox wrote the original versions of bash and readline and continued to improve the shell up until 1993. 

You might be able to find the location by typing `whereis bash` (especially if you are using the C shell); if that doesn't work, try `whence bash`, `which bash`, or this complex command

## Files
Files are the most important types of "things" on any UNIX system. A file can contain any kind of information, and indeed there are different types of files. Three types are by far the most important:

 - **Regular files:** Also called text files; these contain readable characters.
 - **Executable files:** Also called programs, The shell itself is a (non-human-readable) executable file called bash.
 - **Directories:** These are like folders that contain other files

 `cd -`: changes to whatever directory you were in before the current one.
 `ls -l`': tells ls to list the file's owner, size, time of last modification, and other information.
 `ls -a`: lists all files including the hidden files.

 Basic wildcards
|Wildcard|Matches|
|:--:|:--:|
|?|Any single character|
|*|Any strings characters|
|[set]|Any character in set|
|[!set]|Any character not in set|
 **pathname expansion**: it ispossible to use wildcards in the current directory, they can also be used as part of a pathname.
 **Brace Expansion**:  `echo b{ed,olt,ar}s` `echo {d..h} ` h.

## Input and Output
The UNIX I/O scheme is based on **two dazzlingly simple ideas.**

 - UNIX file I/O takes the form of arbitrarily long sequences of characters (bytes). 
 - Everything on the system that produces or accepts data is treated as a file. this includes hardware devices like disk drives and terminals. 

**Popular UNIX data filtering utilities**
|Utility|Purpose|
|:--:|:--:|
|cat|Copy input to output|
|grep|Search for strings in the input|
|sort|Sort lines in the input|
|cut|Extract columns from input|
|sed|Perform editing operations on input|
|tr|Translate characters in the input to other characters|
all of them (and most other UNIX utilities) accept input from standard input if you omit the argument.

`nice` command, where command can be a complex shell command line with pipes, redirectors, etc., then the command will run at a lower priority.

[1]: http://static.zybuluo.com/xiaocorn/ot7mqseskqdhfoobm1ck86eo/image.png
