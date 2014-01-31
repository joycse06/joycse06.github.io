---
layout: post
title: How to Use the same Config file both in Localhost and in Live Server
date: '2012-4-27'
comments: true
categories: life
link: false
---
Every projects have some kind of configuration such
as database username, database password, database name blah blah
no matter whats language it's been written in.

But i will be primarily talking about php here.

In past what I used to change the config file every time I had to push updates into the live server either by FTP or SFTP
or  VCS(Version Control System) and that was painful.
But thanks to the internet I found some tricks somewhere in the internet that I dont remember right now which I have been using since then which allows me to use the same config file both in localhost and in live server.

Lets say we have the  following config values which are different when the code is running in Localhost and in server. Say for localhost they have the following values

``` php

var $dbUsername = 'local';

var $dbPassword = 'localpassword';

var $dbname = 'local_db_name';

```

And in server the value is different as follows

``` php

var $dbUsername = 'server';

var $dbPassword = 'serverpassword';

var $dbname = 'server_db_name';

```

Now as I have already said changing those before every push is a pain in the a**. The way we can have them both at the same time and both environment runs properly is as follows:

``` php

if($_SERVER['REMOTE_ADDR']=='127.0.0.1'){

//we are in localhost as the user's IP is 127.0.0.1

var $dbUsername = 'local';

var $dbPassword = 'localpassword';

var $dbname = 'local_db_name';

}else{

// we are in server

var $dbUsername = 'server';

var $dbPassword = 'serverpassword';

var $dbname = 'server_db_name';

}

```

FYI, $_SERVER['REMOTE_ADDR'] returns users IP address if the user is not using Proxy.

If you are using Virtual host then you can use $_SERVER['SERVER_NAME']. Suppose I have a virtual host setup for all my office projects as d.noobis.com then we can use the following conditionals to determine the correct environment

``` php

if($_SERVER['SERVER_NAME'] == 'd.noobis.com'){

// my local config goes here

}else if($_SERVER['SERVER_NAME'] == 'noobis.com'){

// Live servers config

}else if($_SERVER['SERVER_NAME'] == 'local.noobis.com'){

//someone else's config who is working with me in the same project have a VHost set up as local.noobis.com

}

```

Using this we can remove the burden of editing config file before every commit/upload of codebase into live server. This same technique can be used for frameworks which uses pure php variables for configuration options like Code Igniter.

But if your framework uses special formats for config files like YML then there might be different ways to separate development and production environment settings.

Hope you enjoyed the post. :)

UPDATE: 

As Zaman-A-Piri-Pasa suggested in the comment, if you are on windows using XAMPP use

``` php

if($_SERVER['REMOTE_ADDR']==’::1′)

```

instead to check for localhost.
