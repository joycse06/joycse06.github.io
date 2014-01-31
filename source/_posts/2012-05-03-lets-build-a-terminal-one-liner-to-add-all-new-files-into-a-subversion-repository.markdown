---
layout: post
title: Lets build a terminal one-liner to add all new files into a subversion repository
date: '2012-5-3'
comments: true
categories: life
link: false
---
Those who of you have been working with subversion for a while may have noticed that when you add a number of files in different directories into an existing subversion repository you have to add each one of them from command line which is a pain.

Though the following command adds all new files recursively into the repo but it also add <strong>svn:ignored</strong> files back into the tree which is bad.

``` bash

svn add --force *

```

So I was searching for a good solution today and found What I needed. In this tutorial I will to walking through the steps with explanation and finally we will have a ready-to-use one-liner, running which will add all new(untracked) files into a svn repo.

<strong>Using svn stat(or st) to check the status:  </strong>If you run the

``` bash

svn status

#or

svn st

```

command from within your repo directory it will show you a list of new and modified files, I ran the command in one of my repo and the output was like this

``` bash

?  .idea
?  application/models/book_model.php
?  application/views/find-books.php
M application/views/splash.php
?  application/controllers/books.php

```

The entries which have ? mark at the start are new files yet to be added into the svn repo. Lines starting with M means there are changes to be committed but the file is already in the repo. We are interested with the lines which start with ? So let's filter the output of <strong>svn stat </strong>to find the lines which start with ? .We can use grep to do this using syntax like

``` bash

svn status | grep &quot;^?&quot;

```

Which is telling grep to find lines which start with the character ?.  So this will out something like this for my repo

``` bash

?  .idea
?  application/models/book_model.php
?  application/views/find-books.php
?  application/controllers/books.php

```

Now we have got the list of new files which we can add to svn repo using svn add command. But we need to remove the ? from the starting of these lines to use them as arguments of svn add . We can do this easily using the command line scripting language <strong><a title="Awk" href="http://en.wikipedia.org/wiki/Awk" target="_blank">awk</a> </strong>using the following  syntax

``` bash

svn status | grep &quot;^?&quot; | awk '{print $2}'

```

we saw after using <strong>grep</strong> we had lines of this structure

``` bash

?  application/models/book_model.php

```

As we are using <strong>|</strong> awk, so <strong>awk</strong> is getting two arguments first one is ? and second one is file path. So as <strong>awk </strong>is printing $2 which means the second argument, the file path. So after adding awk block we will have the following output

``` bash
application/models/book_model.php
application/views/find-books.php
application/controllers/books.php
```

Which is exactly what we need to pass to svn add. Now using <a title="Xarg" href="http://en.wikipedia.org/wiki/Xargs" target="_blank">xarg</a> we can pass this argument set into<em> svn add</em> command with this syntax

``` bash

svn status | grep &quot;^?&quot; | awk '{print $2}' | xargs svn add

```

If you run this in your terminal all new files will be added to svn you can check that using <strong>svn stat </strong>again. Which will show that the files has been added for inclusion in remote repo in next commit.

We can make it a bit better though. Most of us use some kind of IDE's and those IDE's leave hidden files  for indexing and saving project specific settings. Or we may have hidden system files which we dont want to include in the repo. We can filter out the hidden files using another grep, the syntax is something like

``` bash

svn status | grep -v &quot;^.[ \t]*\..*&quot;

```

So what is <strong>grep -v "^.[ \t]*\..*" </strong>doing here. The pattern <strong>"^.[ \t]*\..*" </strong> is matching <strong>svn status </strong>entries which have hidden files in it. If you have some idea about regular expression you can figure that out yourself, if you cant you can comment in this post and I will explain in details. And the <strong>-v </strong>option is an <strong>invert-match</strong> switch which tells grep to output <strong>non-matching</strong> files, that means files which are <strong>not hidden</strong>.

So we have got this by far

``` bash

svn status | grep -v &quot;^.[ \t]*\..*&quot; | grep &quot;^?&quot; | awk '{print $2}' | xargs svn add

```

After that you can use svn stat again to make sure you are not committing anything you dont want to. Because it will add any non-hidden files in the repo. If you see that you have added a file you dont want to you can user the following command to remove that from next commit list

``` bash

svn revert filename

#or this if you wan to remove a directory

svn revert --recursive path/to/directory/

```

And if you want to add any hidden file like a <strong>.htaccess </strong>then after running this command you can always use another svn add command to add that specific file or if you want you can even remove the hidden file removing grep block from the command chain.

So the one-liner you can use from now on is

``` bash

svn status | grep -v &quot;^.[ \t]*\..*&quot; | grep &quot;^?&quot; | awk '{print $2}' | xargs svn add

```

This comes in handy When you add a lot of file in different directories and saves you time.

I am not very good at terminal things yet and found this snippet <a title="Snipplr" href="http://snipplr.com/view/15471/" target="_blank">here</a>.

This post is just an effort to better understand whats magic is going on in that one line.

Hope you enjoyed it. :)
