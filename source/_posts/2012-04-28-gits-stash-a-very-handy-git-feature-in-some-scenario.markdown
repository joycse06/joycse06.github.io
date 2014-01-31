---
layout: post
title: Gits stash - a very handy git feature in some scenario
date: '2012-4-28'
comments: true
categories: life
link: false
---
I have started using <strong>git</strong>(a DVCS) from last month and I am liking it much. Some days back I came across the <strong>git stash</strong> command in a Stack Overflow question and was glad that git have this feature which svn doesn't. I will be describing a scenario when <strong>git stash</strong> is very helpful.

<strong>Scenario:</strong> If you are using <strong>git </strong>it can be assumed that you are working in a team and there are other developers working on the same team. Let's say at some point one of your team mate At some knock you on skype and ask to test a feature he just pushed into the repo. After hearing the name of the feature you find out that you two are working on the same source file and you are in the middle of something. Now you have two immediate solutions:
<ol>
	<li>Try committing your broken code which git wont allow as the remote branch is one step ahead and have updates on the same file, it will ask you to do <strong>git pull</strong> first.</li>
	<li>Try git pull which will trigger conflict too.</li>
</ol>
While I was working with svn I used to copy the file somewhere else and then try <strong>svn up</strong> and then accepting server's version of the code using <strong>tf</strong>(their full). After checking the feature I would have copied the new things back from the file I copied earlier.

Using

``` bash

git stash

```

we can push all current changes into git staging area temporarily. Then doing a git pull would work fine. Now we can check the new featured our peer coder added. After that we can use

``` bash

git stash pop

```

to pull back the changes we previously pushed into the staging area, completing what we were doing and then commit the final changes later.

It's one of the features of git I am liking very much. A very handy feature in some scenarios. :)
