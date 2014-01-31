---
layout: post
title: Some Sources of English Word List
date: '2012-4-30'
comments: true
categories: life
link: false
---
I was in need of a english word list for a application and I was searching for a complete list.
First and formost What they are saying around the internet that there's no such thing as a complete list. English Language has lots of words and the count increases when you add slangs or science terms or other categories.

So I will be sharing some of the sources I have found which can save you some time if you need it at some point.

<strong>Finding one from within your *nix/mac OS</strong>: If you are on a mac or any other *nix distro, chances are that you already have a word list in you os. You can find it out by browsing the <em>/usr/share/dict/  </em>directory. In my mac which is 10.6.8 I have the following files in my <em>/usr/share/dict/ </em>directory:

``` bash
ls /usr/share/dict/
README  connectives  propernames  web2  web2a  words

```

So to check I just ran a

``` bash

cat words

```

and the <em>cat </em>command showed the full word list in the terminal. If you are sure that you dont need more than this then you can insert these words into a mysql table easily.

Login into mysql from your terminal window and select the desired database and run the following command:

``` sql

LOAD DATA LOCAL INFILE '/usr/share/dict/words' INTO TABLE words;

```

I am supposing that you have a table named words. If you want the words to be inserted into a multicolumn table the run this one instead

``` sql

LOAD DATA LOCAL INFILE '/usr/share/dict/words' INTO TABLE words(column);

```

Replace <em>column</em> with your column name which is of type VARCHAR.

I have found some other online datases too. Most of them are in excel format. You can convert excel files easily into a mysql database. The steps are:

Save the sql file in csv format(you can do that from excel save as dialogue) and then insert this into your table. I have used <em>Sequel Pro </em>to insert the csv into the table. If you face any problem doing this you can ask for details in comment.

And here are some other links where you can find word databases(xls,csv or sql format) too.

<a title="Wordnet" href="http://androidtech.com/html/wordnet-mysql-20.php" target="_blank">Wordnet</a>

<a title="Source Forge" href="http://wordlist.sourceforge.net/" target="_blank">Wordlist in source forge</a>

<a title="Puzzler" href="http://www.puzzlers.org/dokuwiki/doku.php?id=solving:wordlists:about:start" target="_blank">Puzzlers.org sources</a>

<a title="Info Chimps" href="http://www.infochimps.com/datasets/word-list-100000-official-crossword-words-excel-readable" target="_blank">InfoChimps</a>

<a title="Info Chimps" href="http://www.infochimps.com/datasets/word-list-350000-simple-english-words-excel-readable" target="_blank">Infochimps 350000 Words</a>

And guess what? They are all free. Try all of them to find the one thats suit's your need. :)

English Words list are needed if you are building applications like <em>Dictionaries</em> or <em>crosswords</em> <em>puzzle</em> or any kind of <em>word games</em>.
