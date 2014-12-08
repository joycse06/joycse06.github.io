---
layout: post
title: on and off jQuery's new event binding awesomeness
date: '2012-4-29'
comments: true
categories: web jquery
link: false
---
I have been using jQuery for quite a while and have used all of jQuery's event handlers like <em>$.bind $.live $delegate $.click etc. </em>

Along with the release of jQuery 1.7 , jQuery introduced two new methods for event handling. They are $.on and $.off and in jQuery.com they encouraged use of these two methods for event binding. Because no mater what other method you use like <em>$bind , $live, $delegate</em> jQuery is using $.on for all these under the hood. For a proof of these lets take a look at <em>$bind , $live, $delegate</em> definition in jQuery's source.

``` js

bind: function( types, data, fn ) {

return this.on( types, null, data, fn );

}

unbind: function( types, fn ) {

return this.off( types, null, fn );

}

live: function( types, data, fn ) {

jQuery( this.context ).on( types, this.selector, data, fn );

return this;

},

die: function( types, fn ) {

jQuery( this.context ).off( types, this.selector || &quot;**&quot;, fn );

return this;

}

delegate: function( selector, types, data, fn ) {

return this.on( types, selector, data, fn );

}

undelegate: function( selector, types, fn ) {

// ( namespace ) or ( selector, types [, fn] )

return arguments.length == 1? this.off( selector, &quot;**&quot; ) : this.off( types, selector, fn );

}

```

Now you can see that jQuery itself is using <em>$.on</em> and <em>$.off</em> for all event handling. So it's good time to start using these new functions for event handling.

Let's take a look at the syntax of <strong>$.on </strong> and how we can use it as a replacement for $.live, $bind and $delegate. Here is the function definition for $.on from jQuery.com.
<blockquote>
<h4>.on( events [, selector] [, data], handler(eventObject) )</h4>
<strong>events </strong>One or more space-separated event types and optional namespaces, such as "click" or "keydown.myPlugin".

<strong>selector </strong>A selector string to filter the descendants of the selected elements that trigger the event. If the selector is <span style="font-family: Monaco, Consolas, 'Andale Mono', 'DejaVu Sans Mono', monospace; font-size: x-small;"><span style="line-height: normal;">null</span></span> or omitted, the event is always triggered when it reaches the selected element.

<strong>data </strong>Data to be passed to the handler in <em>event.data</em> when an event is triggered.

<strong>handler(eventObject) </strong>A function to execute when the event is triggered. The value <em><span style="font-family: Monaco, Consolas, 'Andale Mono', 'DejaVu Sans Mono', monospace; font-size: x-small;"><span style="line-height: normal;">false</span></span></em> is also allowed as a shorthand for a function that simply does <span style="font-family: Monaco, Consolas, 'Andale Mono', 'DejaVu Sans Mono', monospace; font-size: x-small;"><span style="line-height: normal;">return false</span></span></blockquote>
seeing all these arguments it may seems a bit scary at first glance but you dont need to remember all of this. You just need to remember two basic scenarios we need most of the time.

If you need a simple event binding which doesn't need to persist to elements dynamically added to DOM(Document Object Model) through ajax or jQuery codes then you can use on directly on the target element using the syntax

``` js

$(selector).on(event,handler);

//example

$('a.simple').on('click',function(){

alert('I was clicked');

});

```

This will be added to all anchor elements with a class of <em><strong>simple. <em>But if you add new elements after the DOM is loaded this event binding wont work.</em></strong></em>

<em><strong><em></em></strong></em>Now if you want the event binding persist to new elements added to the dom then the syntax is a bit different

``` js

$(document).on('click','a.complex',function(){
alert('I was clicked');
});

```

now I am attaching the click event to document instead of the target element. Why is that? Because I want the event binding works for new elements too.

So what will happen when user click on an element?

If you have idea about event bubbles in JS then you know that any events triggering travels from source element to its parents unless you specify other wise.

So when user will click on any element document object will get a click event and on method will check if the source of the event is '<em>a.complex</em>' if it's true then it will fire the handler function. Check this in the following fiddle

[jsfiddle url="http://jsfiddle.net/GhbZv/6/" height="400px" include="result,html,js,css"]

Now as we are attaching the event with document as document will always exist in DOM it will work for all matching elements no matter when they are added to the DOM.

The syntax for $.off is simple

``` js

$(selector).off();

// to turn off a specific handler the syntax will be

$(selector).off(handler);

```

Though jQuery still supports previous event binding method's, it's good to write new event handlers using this two new methods, at least you can save a function call with this.  :)

<strong>Update:</strong>

And here goes some examples how you can convert previous $.bind, $.delegate or $.live calls

``` js
/* for $.bind to $.on */
/* Old: */ $(&quot;.foo&quot;).bind(&quot;click&quot;, handler);
/* New: */ $(&quot;.foo&quot;).on(&quot;click&quot;, handler);

/* for $.delegate to $.on */

/* Old: */ $(&quot;#container&quot;).delegate(&quot;.foo&quot;, &quot;click&quot;, handler);
/* New: */ $(&quot;#container&quot;).on(&quot;click&quot;, &quot;.foo&quot;, handler);

/* for $.live to $.on */

/* Old: */ $(&quot;.foo&quot;).live(&quot;click&quot;, handler);
/* New: */ $(document).on(&quot;click&quot;, &quot;.foo&quot;, handler);
```
