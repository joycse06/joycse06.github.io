---
layout: post
title: "Looking for occurrences of a string in all files under a directory using grep"
date: 2014-12-10 04:52:18 -0500
comments: true
categories: linux terminal
---

 ```grep``` is a very simple command available on linux and OSX terminals which can do amazing things and can save a lot fo time. I was working on a Magento site this morning and on the checkout page I found the telephone field was using ```<input type="Number" ...``` while it should use ```<input type="text" ...``` or else phone number with dashes in them wont validate and customers wont be able to proceed with next step.

I was thinking of how can I find the file which have this code, those who of you are familiar with Magento knows that magento has a very organized file structure and hierarchies of templates, and different templates gets loaded based on active theme and layout xml files. So I have to find out all the occurrences of ```<input type="number" ...``` in the whole installation.

I knew I can use ```grep``` to do this. I instantly looked at the man page of ```grep``` and reviewed the available options and got what I needed.

The final command I wrote on the server's terminal was

``` bash Grep

grep -R -i -n 'type="number"' public_html/app

```

 ```-R``` tells ```grep``` to search recursively into that directory, ```-i``` is for ```ignore-case``` and ```-n``` finds and outputs the line number along with file name too. As soon as I ran that command it searched the whole ```app``` directory and came back with all the place where ```type="number"``` occurred with line number too. Then I just edited those files and the issue was fixed.

 What took me several minutes would have taken hours to search and find those files manually. Thats one of many benefits of using command line. And you can do pretty amazing things using/combining only a handful of commands.