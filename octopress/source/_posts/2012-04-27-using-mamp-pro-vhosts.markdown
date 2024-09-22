---
layout: post
title: 'Using MAMP Pro''s VHosts feature to run Rails app in a fancy domain name '
date: '2012-4-27'
comments: true
categories: rails web
link: false
---
I have been playing with ROR(Ruby On Rails) from last few days. Those who of you have idea about the the nature of Rails apps know that, to run a rails app you have to run the following command in the terminal from inside the apps directory.

``` bash

rails server

```

And the app will start running on <strong>http://0.0.0.0:3000</strong> or <strong>http://localhost:3000</strong> WEBrick LightWeight Web Server that Rails ships with by default.

As I have left <strong>http://localhost/</strong> long ago and been using fancy local domain names for all my projects, so I was searching for a solution on how can I do that with rails apps to. You know it doesn't feel good to type in some thing like <strong>http://0.0.0.0:3000</strong> in browser . Instead I would love some domain name like <strong>http://d.rails.dev</strong>  or something like that. It's just feels good typing that.

And I found a solution. :) Here's how to do it. In Mamp pro's control panel in the host tab create a new host. Write <strong>http://d.rails.dev</strong> as the server name. The disk location isn't important here but I have set it to my rails app directory. Now switch over to the advanced tab in right configuration panel and paste in the following code block in Customized Virtual Host General Settings option box:

``` bash

ServerName rails
ServerAlias *.dev

ProxyPass / http://localhost:3000/
ProxyPassReverse / http://localhost:3000/
ProxyPreserveHost On

```

As you can see from the above code it's actually forwarding all traffic in / which is <strong>http://d.rails.dev</strong> into <strong>http://localhost:3000</strong> (Where our actual rails app is running). Click apply and you are done.

Now start the rails app using <strong><em>rails server  </em></strong>command from terminal from within the rails app directory and point your browser into <strong>http://d.rails.dev</strong> and see your rails app is running there.

Pretty easy huh! But it provides a good amount of self satisfaction, well, at least for me. I like seeing fancy domain names for my web apps.

I haven't been able to test the process for MAMP but it shouldn't be much different, though you have to do everything manually from adding vhost entry into httpd.conf  and then add the host name entry into /etc/hosts file. (I will be writing another post on how to create a virtual host in mamp/lamp sometime soon).

Thanks for taking the time to read up to this point. :)
