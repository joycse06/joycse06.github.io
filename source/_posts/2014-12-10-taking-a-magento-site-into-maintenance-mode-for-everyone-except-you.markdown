---
layout: post
title: "Taking a magento site into maintenance mode for everyone except you"
date: 2014-12-10 02:54:25 -0500
comments: true
categories: web php
---

Magento takes a dead simple approach for activating the maintanence mode for any site using it. You just get into servers's terminal via ```ssh``` , ```cd``` into the magento root folder and run the following command ```touch maintenance.flag``` or just crate a file named ```maintenance.flag``` in the root of the your magento installation and voila, you will see the maintenance mode message when you visit your store now.

Now Its locked for everyone, but say you are updating somethings on the site and you would like to check how thats affecting the look of the store but you dont want anyone to view it before getting done. Fortunately thas easy too.

<!-- more -->

If you look into the ```index.php``` at your magento root folder you will see a code block like below

``` php index.php
$maintenanceFile = 'maintenance.flag';
if (file_exists($maintenanceFile)) {
    include_once dirname(__FILE__) . '/errors/503.php';
    exit;
}

```

I guess you understand what it does from the look of it, it just checks if the  ```maintenance.flag``` file and if it exists it include the ```errors/503.php``` file which contains the maintenance mode message. On a side note you can edit that file to change the message or to add some styling.

Now we want it to stay same for other but to allow us to view the site. How can you determine the request came from your machine in that script? Well you might already know, you have a public identifier which is your ```PUBLIC IP``` , and by using that you can bypass that check and view the site.

To make the solution a more robust we will use an array of IPs so you can whitelist more peoples like maybe your co-worker, or your QA or your boss. First we create an array like this, ```$allowed = array('27.147.178.86','27.147.185.185');``` , you can add as many IPs as you want in that array and each one of those owners will be able to view the store while it's still in maintenance mode. Now to get the IP of a Specific HTTP request you can go around the easier way with something like
  ```$ip = $_SERVER['REMOTE_ADDR'];``` But keep in mind that might not always work like when user is using behind a proxy server. Search in google for a better way to find the IP address using PHP.

We are almost done, just append ```&& !in_array($ip, $allowed)``` in the condition of ```if``` and you are done. The function ```in_array``` checks if the first argument is present in the array passed int he second argument and return true if found, we are negating the output of the function, because we want that part of the condition to return false and proceed with the code execution if the request came from one of the specified IPs.

Thats all you need. I am adding the complete code below.

``` php index.php

$ip = $_SERVER['REMOTE_ADDR'];
$allowed = array('27.147.178.84','27.147.180.185','96.240.89.118');

if (file_exists($maintenanceFile) && !in_array($ip, $allowed)) {
    include_once dirname(__FILE__) . '/errors/503.php';
    exit;
}

```
