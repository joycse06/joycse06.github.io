---
layout: post
title: "The Unix Tar Pipe : Lots of Unix Knowledge in a simple one liner"
date: 2014-12-06 03:22:15 -0500
comments: true
categories: linux tools
---

I have recently been introduced to the Tar Pipe. It's not a command to run in the shell but a pattern which I liked very much.

It uses some primitive unix command and couple features of the shell to solve a problem. Let me talk a bit about the problem it solves. Suppose you have a directory called src and you want to clone it to a destination folder named dst preserving metadata like owners, groups and permissions of files and directories. In modern systems there are command like

``` bash Copy Command
cp -rp

# or

rsync -a

```
 using which you can clone contents of one directory to another preserving permissions and other metadata easily. But in the days when rsync -a was not there or the cp implementation didn't have the permissions preserving flag there was a tar pipe which did the same thing using unix tar command. The Tar Pipe looks like this

``` bash Tar Pipe

 (cd src && tar -c .) | (cd dst && tar -xp)

```
<!-- more -->

So why I liked a command very much considering its obsolete now. The reason is if we look closely there's lot of Unixy stuff in this one line.

Looking from the outside we see two subshells [ (cd src && tar -c .) and (cd dst && tar -xp) ] joined by the pipe. A subshell basically tells bash or zsh to fork itself the execute the code inside the parantheses in a new process. And this actuallly isotates the subshell's current working directory, its variables and other options.

So the left part of the Tar pipe change the directory into src and tar the contents of that directory and output the stream into standard output. And on the other side of the pipe we have a blocking process with is waiting to be feed from the left side.

When the left side starts outputting, the right side change current directory into dst and starts reading from the other side of the pipe and untar it preserving permissions.

 On either side &&  ensures that if the directory doesn't exist tar command on the right won't fire at all.

 That all for today. I will be talking more about fork and tar in another post. Hope you enjoyed it.