---
layout: post
title: "Exploring Ruby's Proc, Lambda and Blocks"
date: 2014-12-30 02:56:59 -0500
comments: true
categories: programming ruby
---

I have a weakness for ROR(Ruby on Rails) partly due the reason I am always obsessed with learning to do things in better ways for a long time. I am still a noob but I want be a very good Software Architect someday, with deep knowledge and practical experience on software design and development following all the best practices.

And what I have found, Rails projects are naturally more likely to follow better ways of doing things, and community is much more robust. Though the reason behind my this perception can be the fact that I started with CodeIgniter mainly and worked on some some CI projects at the early stage of my Career which were initially done by some Indian developer and they did mostly everything from controller in CodeIgniter, there were no models, not rules of where to put what. It was a pure mess. Anyway, these things pushed me towards my quest for searching for better ways of doing things, and I did some research on ROR and found the community around ROR is great. They do TDD( Test Driven Design ) and there were many great tools to help developers test their code, TDD in PHP community is gaining traction too recently. But still I see most PHP devs takes testing as optional and find excuses to not write automated tests. Projects I have worked on didn't have chance/scope to do TDD. I am a not-so-experienced avid follower of TDD and design patterns and wanna be good on them.

<!-- more -->

Anyway, things are changing and TDD is more common in PHP community too, with libraries like PHPUnit, PHPSpec and Behat things became easier too.

I have always wanted to get involved into some ROR projects just to learn their ways of doing things and gather the framework-agnostic knowledge in it and hence improving my thinking process but that didn't happen for no good reason or lack of motivation. A month or so ago I was trying to get some knowledge on DDD( Domain Driven Design ) and Hexagonal Architecture but I found mostly ROR conference talks and my desire to learn ROR striked again. I was checking Ruby after a long time. And wanted to learn something new. Ruby has always amazed me for it's developer friendly syntax, 'Everything's object' paradigm and specially for lots of awesomely-named methods on every objects.

I would like to give example of a few like

``` ruby times method
4.times { puts "Hello Ruby!" }
```

which amazes me every time. Most of the languages I tried have something called primitive types and values like integers, floats, strings are treated specially. But in Ruby almost everything is object. There's no notion of primitive types and surprisingly at least to me even ```classes``` are objects too. To prove that we can do something like following in any ruby REPL

```ruby
# Output from Pry

Array.class # returns Class < Module
Array.class.superclass # returns Module < Object
Array.class.superclass.superclass # returns Object < BasicObject

```

So the hierarchy for ```Array``` class is

 dy```Array < Class < Module < Object < BasicObject```

So ```BasicObject``` is at the top of the Ruby object Model. And every class in ruby is a subclass of that. Lets stop here, i will write about the Ruby object model another day.

The designer of the language have put a lot of thought on the syntax specially to make the code more readable, and make developer's life easier. Lets see some other syntactic awesomeness

```ruby

email = "Joy Nag <joy@example.com>"

# you can apply regex just not the string object
email[/<(.*?)>/, 1]  # returns => "joy@example.com"

# you can do initialization and assignment of a
# variable with a single liner

(foo ||= []) << 'Hello World' # Returns an array with the
                              # string 'Hello World' appended

# calculating sum of all elements of an array

[1,2,3].inject(0) { |sum, num| sum + num } # returns 6

# squaring all elements of a array

[1,2,3].map { |n| n*n } # returns [ 1, 4, 9 ]

# selecting only the even numbers from an array

[1,2,3,4].select { |n| n % 2 == 0 } # returns [2,4]

# and there's the very handy .each method on enumerables
[1,2,4].each { |n| puts n } # prints 1 2 and 4 and returns
                            # the original array

```

Well, lets get back to the topic of this post.

blocks, procs and lambdas are all example of ```closures``` in ```ruby```. In laymen term **they are ways of grouping of code we want to run**. Lets start with ```Blocks```.

## Blocks

Blocks are still the only thing I am aware of where the everything's a object rule is broken. blocks are language constructs and doesn't have any type. Block are handy small chunk of code which can be passed around easily to methods to change the way of how they will behave. Kind of like allowing the user of a function or method to inject behaviors to that method based on what the output of that function or method changes. Like if we see from our previous example

```ruby Block example
[1,2,3,4].select { |n| n % 2 == 0 }

# { |n| n % 2 == 0 } is a block which takes one argument
                    # and return true or false based on the condition
```
 Block can be of two form. If the content of the block is small to fit in a line then you can just enclose that in ```{}```. And if the content if larger you can use the ```do end``` construct to create the block. Lets re-implement a simple version of the ```map``` method of array class. Ohh, here's one catch, Ruby system classes are open and you can add or overwrite any method on any system class at your own risk. It's a great power and if not done carefully can cause mess in your program.

```ruby mymap method

class Array
    def mymap
        out = [] # where we will be collecting the transformed values
                 # after passing them through the block
        if block_given?
            # a block has been passed, we are good
            self.each { |elem| out << yield(elem)
        else
            # block was not given, so return an enumerator like the
            # original map method
            out = to_enum :mymap

        out # this returns the transformed array
    end
end

# now we can use it like [1,2,3].mymap { |n| n*2 } to double
# every element of the array

```

We can see several other things about blocks here in this example, like you can check if a block was passed with the ```block_given?``` method and decide what to do if block was not given. Another reason we need to do this defensive thing is if we call ```yield``` anyway and if the method is called without a block, then the execution will halt with a ```LocalJumpError``` which we probably dont want.

So the way to execute a block is to is by call the system method ```yield``` and we can pass in any number of argument to it and  and it will return the output of the block. ```yield``` is magical because every object oriented rule in Ruby in suspended for this special mode of operation.

Blocks are too mainstream in ruby programs and found almost everywhere. Due to this conveninece  and other ways to enumerate are rarely used. After seeing this when there's these awesome ways to loop though a list or array why would anyone use traditional  ```for while do-while``` if not absolutely necessary.

Ok there lot more to block itself. Like you can't pass more than one block to a method. Blocks which are passed implicitly to a method can be converted to a explicit method parameter using ```&``` . Like we can do the following

```ruby Converting Implicit blocks to Explicit named parameter
def calculate(a, b , &block) # &block is an explicit param
    block.call(a, b)
end

# then you can call it like
puts calculcate(5,5) { |a, b| a + b }
```

You can do the other way around too, like converting a lambda into a implicit block and pass it to a function which doesnt receive a explicit block.

lets talk about lambdas.

## Lambdas

 ```Lambdas``` are actually ```Proc``` objects which slight differences. ```lambda``` is a keyword and not a function which return an instance of class ```Proc```. We can check this using the following

```ruby Lambda and Procs

proc = Proc.new { puts "Hello world" }
lam = lambda { puts "Hello World" }

proc.class # returns 'Proc'
lam.class  # returns 'Proc'

```

lambdas are kind of named blocks which can be passed around functions without conversions which is necessary for blocks.

Lets look at a slightly complex exercise from Rubymonk which I had hard time understanding first. Understanding this will help clear many things about the conversions

```ruby Implicit Explicit conversions
Filter = lambda do |array, &block|
    array.select &block
end

# now we can use this filter method like

Filter.call([1, 2, 3, 4]) {|number| number.even? } # returns [2,4]
# or
Filter.call([1, 2.0, 3, 4.0]) {|number| number.integer? } returns [1, 3]
# returns [1,3]
```

So lets break the Filter lambda down, that lambda takes two arguments first is an array and second is an explicit block so when we are calling it like

 ```Filter.call([1, 2, 3, 4]) {|number| number.even? }``` the block passed to the call method is implicit. But as our lambda takes an explicit block we are converting it while defining the arguments in the lambda like ```&block``` which converts any implicit block passed to a method or lambda to a named parameter. Then we are calling ```Array.select``` method inside it which on the other hand takes an implicit block. So time to convert the named parameter again to pass it to the ```Array.select``` method. Thats why we are doing it like ```array.select &block```. So this two way conversion should have cleared your head around the conversion.

## Proc

 ```Proc``` is a short for ```Procedure``` and its a class in ```Ruby```. We can define a proc like

```ruby Proc
p = Proc.new { puts "hello Proc" }

# and then we can use p just like any other variable

p.call  # prints hello Proc
p.class # returns Proc
a = p   # a now equals p
p       # it returns the proc object

```

## Differences between Block, Proc and Lambda

Unlike block the other two are instances of the ```Proc``` class and they can have standalone existance. while block is just part of the ```Syntax``` of a method call. It doesn't mean anything on a standalone basis and can only appear in argument lists.

 Like both ```{ puts "hello block" }``` and ```a = { puts "hello block" }``` causes ```Syntax Error```.

 At most only one block can be present in an argument list. In contrast you can pass around multiple procs to methods.

Like I said earlier, ```lambda``` and ```proc``` are both instances of the ```Proc``` class. But the have slight differences. I will go over them briefly below:

1. Lambdas check the number of arguments, while procs do not

This one is fairly straight forward and doesn't require much of a explanation. Lambdas halts execution with ```ArgumentError``` if the number of argument mismatch. While ```Proc``` return nill if no argument is passed, or ignore extra arguments if more arguments are provided.

2. Lambdas and procs treat the ```return``` keyword differently

 ```return``` inside of a lambda triggers the code right outside of the lambda code. Lets see an example:

```ruby
def lambda_test
  lam = lambda { return }
  lam.call
  puts "Hello world"
end

lambda_test                 # calling lambda_test prints 'Hello World'
```

so we see the ```return``` statement inside the ```lambda``` return the control of the code right outside of the lambda code. and the ```puts``` call execute properly.

while on the contrary

 ```return``` inside of a proc triggers the code outside of the method where the proc is being executed
```ruby
def proc_test
  proc = Proc.new { return }
  proc.call
  puts "Hello world"
end

proc_test                 # calling proc_test prints nothing
```

so we see the ```return``` statement in the ```proc``` return from the ```proc_test``` function itself.


So that's it for a single post. I have been following the courses at [Ruby Monk](https://rubymonk.com/) and they are great with exercises and all. Using ```Pry``` for trying out different things instead of the bundled ```irb``` as ```REPL```.

Hope you enjoyed it! :)



