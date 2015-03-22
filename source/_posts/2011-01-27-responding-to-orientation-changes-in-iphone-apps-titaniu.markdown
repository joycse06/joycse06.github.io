---
layout: post
title: Responding to orientation changes in Iphone Application Using Titanium
date: '2011-1-27'
comments: true
categories: programming ios
link: false
---
I am learning Titanium and Just Loving it. Earlier I thought i wont be able to do many things using titanium and started learning Obj-C and Cocoa-touch framework. And that path needs lots of study and learning before someone can build something really productive.

But the Scenario is different in case of Titanium. If you have basic programming concepts and have some experience with JavaScript you can start right away after a couple hours of study. And Titanium creates the native Obj-C codes for you. So there is basically no difference in an Iphone App made with Obj-C using UIKit framework with one built using Titanium. And even better is that you can deploy your Iphone App code In Andriod Platform with few changes. Supports for Blackberry is coming. So Titanium is a very good Parser itself, it takes you code written in JavaScript And Creates the Obj-C code for Iphone and Java Code for Android for you. As Titanium acquired Aptana few days ago We can expect that we will get a very good IDE for developing Titanium Apps with code Sense, debugging features and who know what else, lets wait and see what they brings.

And the amazing thing is that it's open source. They have an example app which have all the features Available in Iphone ranging from simple TableViews to 3D animations.

Anyway, let's talk about the Title of this post. You know Iphone,Ipod Touch, IPad have accelerometers built-in. That means it can detect orientation changes. Now responding to orientation changes Is a very important feature that every Iphone Apps should have. Doing it is not tough with Titanium. Here is a code block, which will respond to device orientation changes by changing the UI orientation. Place it anywhere in you app.js file.
``` js

Ti.Gesture.addEventListener(
'orientationchange', function(e) {
// alert("Called");
win.orientationModes = [
Titanium.UI.LANDSCAPE_LEFT,
Titanium.UI.LANDSCAPE_RIGHT,
Titanium.UI.PORTRAIT,
Titanium.UI.UPSIDE_PORTRAIT
];
if (e.orientation == Titanium.UI.LANDSCAPE_LEFT ||
e.orientation == Titanium.UI.LANDSCAPE_RIGHT) {
// Do anything you wanna do when user changes the orientation to LandScape
Ti.UI.orientation = Titanium.UI.LANDSCAPE_RIGHT;

} else {
// Revert the changes you made, the user is back to portrait mode
Titanium.UI.orientation = Titanium.UI.PORTRAIT;
}
});

```

The code is pretty straightforward. We are attaching an event listener into the Ti.Gesture and once the orientation of the device changes this callback is fired and we can do anything we want into that callback. I will try to write small posts on titanium as i go along learning Titanium.
