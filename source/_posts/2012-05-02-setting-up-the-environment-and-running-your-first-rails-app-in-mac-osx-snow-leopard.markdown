---
layout: post
title: 'Setting up the environment and Running your first Rails app in mac OSX Snow
  Leopard '
date: '2012-5-2'
comments: true
categories: rails
link: false
---
I have been into Rails recently and found out that configuring Rails Environment is easy in Mac. So I will be sharing the steps I followed to setup the environment and running my first rails app.

<strong>Installing a C compiler: </strong>Mac OSX's installation Disc comes with a set of developer tools which is not installed by default. So go ahead and install XCODE from the Installation DVD. It will install all necessary tools to compile and install packages from source. And we will need compiling source to install versions of ruby using RVM(Ruby version manager).

<strong>Installing RVM: </strong>Ruby Version Manager is a very handy tool while working with multiple version of Rubies. Snow Leopard comes with Ruby 1.8.7. And using RVM you can install Ruby 1.9.2 or the current stable version 1.9.3. And switching between version using RVM is very easy. So let's jump into installing RVM. Rvm's installation is very easy. The rvm <a title="RVM" href="https://rvm.io/rvm/install/" target="_blank">website</a> provides a one liner installation script to run in your terminal.

``` bash

$ curl -L get.rvm.io | bash -s stable

```
Then to load the rvm you have to run the following command:

``` bash

$ source ~/.rvm/scripts/rvm

```

But you are not going to load the rvm every time you start terminal. So its better to add the following file in your <em>~/.bashrc</em> or <em>~/.bash_profile</em> file and <em>~/.zshrc</em> if you are using zsh as your default shell.

``` bash

# place in ~/.bash_profile or ~/.bashrc as the very last line
[[ -s $HOME/.rvm/scripts/rvm ]] &amp;&amp; . $HOME/.rvm/scripts/rvm

```

After loading the rvm script file to check if rvm is properly installed in your system run the following command in terminal

``` bash

$ type rvm | head -n1

```

If you it says "<em>rvm is a shell function</em>" or "<em>rvm is a function</em>" then it's fine and you are good to go.

Now go ahead and install an updated ruby version like 1.9.2 or 1.9.3 using the command

``` bash

$ rvm install 1.9.2
# or
$ rvm install 1.9.3

```

It will take some time and that specific ruby version will be installed automatically. Now to check which version of ruby is default in terminal you can try the following command

``` bash

$ ruby -v

```

It's supposed to say 1.8.7  if you havent upgraded the default Ruby version. Now to use the newly installed Ruby you have to tell rvm to do so using the command

``` bash

# Use which version you installed using rvm
rvm use 1.9.2
# The above command will load the environment in current terminal session
# if you want to use this as the default version in terminal all the time use --default option instead
$ rvm use 1.9.2 --default

```

now if you run <em>ruby -v </em>you will see your desired ruby version. Now let install rails gem using <em>gem install </em>command.

``` bash

$ gem install rails

```

It will take some time and install latest <em>rails </em>system into your system. Now let's create a new rails project. Move to your desired directory and run the following command

``` bash

$ rails new blog

```

It will create a new rails application named blog in current directory. Now move into the new blog directory. and then run following command for bundling the gems :

``` bash

$ cd blog
$ bundle install

```

It will take some time and install all gems required to run the application. Now start the app using the follwoing command

``` bash

$ rails server

```

if you followed the previous steps properly you should see something like the following

``` bash

Booting WEBrick
Rails 3.2.3 application starting in development on http://0.0.0.0:3000
Call with -d to detach
Ctrl-C to shutdown server
[2012-05-02 14:57:59] INFO WEBrick 1.3.1
[2012-05-02 14:57:59] INFO ruby 1.9.2 (2012-02-14) [x86_64-darwin10.8.0]
[2012-05-02 14:57:59] INFO WEBrick::HTTPServer#start: pid=81487 port=3000

```

It will start WEBrick (a simple  web server rails ships with itself ). And you can open your browser and point it to http://0.0.0.0:3000 and you should see the following screen

<a href="http://blog.joynag.net/wp-content/uploads/2012/05/rails.png"><img class="aligncenter size-thumbnail wp-image-181" title="rails" src="http://blog.joynag.net/wp-content/uploads/2012/05/rails-150x150.png" alt="" width="150" height="150" /></a>

To test a full featured rails 3 app you can try this <a title="Invoice" href="https://github.com/linkworks/invoices" target="_blank">invoice</a> app from github. This app is quite complete and even have pdf invoice generation. To install and run it try the following command  in terminal, you will have to have GIT installed. If you dont have git installed on your machine follow instructions from this page <a title="Install Git" href="http://help.github.com/mac-set-up-git/" target="_blank">http://help.github.com/mac-set-up-git/</a> to install git.

``` bash

$ git clone https://github.com/linkworks/invoices.git

$ cd invoices

$ bundle install

$ rails server

```

and then check the app in your browser in the following url. http://0.0.0.0:3000 you should see the app running. :) here is a screenshot of the app after you add register and add some data

<a href="http://blog.joynag.net/wp-content/uploads/2012/05/invoices1.png"><img class="aligncenter size-thumbnail wp-image-186" title="invoices" src="http://blog.joynag.net/wp-content/uploads/2012/05/invoices1-150x150.png" alt="" width="150" height="150" /></a>

Like me if you want to get fancy url's for you rails apps through virtual hosts then follow the steps in <a href="http://blog.joynag.net/2012/04/using-mamp-pro-vhosts/" target="_blank">this tutorials</a> .

Rails by default uses sqlite as default database engine if you want to use mysql as default database then using the following command instead

``` bash

$ rails -d mysql blog

```

Then configure your database access info in the file located at <em>config/database.yml  </em>and you are good to go. Though you have to have <strong>mysql2</strong> gem installed in your system. Add

``` bash

gem 'mysql2'

```

in <em>Gemfile </em> in the root of your app directory and then run <em>bundle install </em>again

``` bash

$ bundle install

```

If you have MAMP installed and want to know how you can reuse mamp's mysql installation with rail's apps then follow <a title="MySql" href="http://blog.joynag.net/2012/04/using-mamp-mysql-installation-with-rails-app/" target="_blank">this tutorial</a> I wrote earlier. :)

If you are using ubuntu you may try this tutorial <a title="Rails on Ubuntu" href="http://blog.sudobits.com/2011/10/27/how-to-install-ruby-on-rails-in-ubuntu-11-10/" target="_blank">http://blog.sudobits.com/2011/10/27/how-to-install-ruby-on-rails-in-ubuntu-11-10/</a>

or this <a href="http://www.web2linux.com/05/installing-rails-3-on-ubuntu-10-04-lucid-lynx/" target="_blank">http://www.web2linux.com/05/installing-rails-3-on-ubuntu-10-04-lucid-lynx/</a>
