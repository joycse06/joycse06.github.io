---
layout: post
title: Taking Advantage of Implicit looping In jQuery Setter Methods
date: '2012-7-8'
comments: true
categories: web jquery
link: false
---
jQuery setter methods are those methods using which we can set the value of any input field, or any attributes like width,height,classes etc and some of the setter methods are 
``` js

.val(val)
.height(height)
.width(width)
.attr(name,value)
.prop(property,val)
.text(text)
.html(html)
.addClass(className)
.....
.....

```

and most(or maybe all) of them have a special syntax like for <strong>.width()</strong> there is

``` js

.width( function(index, width) )

```

<strong>What jQuery does for this case is, it loops over the set elements in current selection internally and pass those one by one into that callback function.</strong> And <strong>index</strong> is the index of the element currently being iterated, and <strong>width</strong> is the current width of element. And you will also get the current element as this inside that function. 
This syntax is useful or intended for cases when you use the <strong>.width()</strong> method on a <strong>collection of elements</strong>. I wasn't aware of this until recently, but they can be very useful in cases. Suppose if you want to make the width of all divs in a page to half of it's current width, you can do that easily using this syntax like

``` js

$('div').width(function(index,width){
   return width/2;
});

```

So what you return from that function will be set as new width of that element. 
Pretty cool, huh! You could also use .each() to iterate over the divs collection and set their width manually. But the above code is much more cleaner and maybe a little faster.

Example of some cases when this implicit looping can be useful and will help you write cleaner code.

Suppose you want to give unique id to all li's inside a ul with id of list, you can do so the following way easily
``` js

$('ul#list li').attr('id',function(i){
   return 'list-item-'+i;
});

```

and they will get id's like list-item-1,list-item-2,list-item-3 etc.

Another useful thing can be to trim the value in a textbox as soon as users moves away from it. You can do that like

``` js

$('input').on('blur',function(){
  $(this).val(function(index,val){
    return $.trim(val);
  });
});

```

Or say in a text field field(which takes numbers), you always want to force two decimal point in floating values no matter how many decimal points have been entered by users, you can do that like 

``` js

$('input').on('blur',function(){
  $(this).val(function(index,val){
    return parseFloat(val);
  });
});

``` js

Example Fiddle: (Enter any number and click somewhere else)

[jsfiddle url="http://jsfiddle.net/joycse06/PbKVf/" height="400px" include="result,html,js,css"]

There are many useful uses of this callback function in setter methods.

Suppose you have a html5 data attribute named data-bg set on all div and onclick of some button or link or on some user action you want to set that as background image of those divs. You can do do so using

``` js

  $('something').on('click',function(){
     $('div').css('background-image',function(){
         return '/images/'+$(this).data('bg');
     });
  });

```

I can show many example of useful uses of this implicit looping. This is a very helpful and often overlooked feature in setter methods which you take advantage of. :)  

