---
layout: post
title: "Functional Programming : My Intro to Higher Order Functions"
date: 2014-12-15 01:14:01 -0500
comments: true
categories: programming functional
---

In University time, I was mainly acquainted to C/CPP programming with some Java. Then to me  ```function``` was something special and very different than other inhabitants of the my then programming world. In that time I couldn't even think of passing/returning functions to other functions.

Then sometime at the end of 2009, while I was learning Javascript I saw different behaviors and JS seemed more liberal to me. Like I could declare functions without name, could also assign them to variables, It was like wow to me. I read about anonymous functions and IIFE( Immediately-Invoked Function Expression ) and they amazed me back then. I first learned from Hasin Hayder while working in Leevio that I can assign name of a function to a variable  and call them like any other function and could even play with their scope with ```apply``` and ```call``` ```Prototype``` methods.

 <!-- more -->

 Then anonymous function and closure kind of became normal to me while working with ```PHP``` and found I could even respond to undefined method calls in ```PHP``` Using ```__call()``` and ```_callStatic()``` ```magic methods``` . I didn't know that there's more surprise waiting for me.

 While playing with ```Scala``` I found I can pass a function to another function and using that function argument that function can return new function with new behaviors. Like a generator function. It was amazing to me. I bet it might amaze you too if you are not already familiar with it.

 These kind of functions are called ```Higher Order Functions```, functions which takes other functions as argument works on them and may return new functions. Functions are ```First Class Citizens``` in functional languages. **That means function are just like any other value. They can be passed/returned to/from other functions.**

 Enough rambling. Lets define a higher order function in ```Scala```.

 We want to define a function to find the sum of the a range of values, sum of their squares and sum of their cubes. In any imperative language we would just create three functions and calculate the sum using a loop. We can do the same in ```scala``` using recursion and three different function. But we dont want to do that.

Lets see the higher order function definition first and then I will explain that.

``` scala Higher Order Function
def sum(f: Int => Int): (Int, Int) => Int = {
    def sumF(a: Int, b: Int): Int =
        if (a > b) 0
        else f(a) + sumF(a + 1, b)
    sumF
}

```

The syntax can seem a bit odd to those not familiar with ```scala``` at all. The definition of the function tells that, the function ```sum``` takes another function as argument which is of signature ```f: Int => Int``` means that functions takes an integer and return an integer too. The part of the function definition after ```:``` which is ```(Int, Int) => Int``` tells the compiler that ```sum``` returns an function of type ```(Int, Int) => Int``` which means it takes two integers and return one as result.

Then we define another function inside the main ```sum``` function which is recursive version of a loop which runs from ```a to b``` which is our range and calculate the sum by passing each integer to the input function. Then at the last line of the definition we just have ```sumF``` which is the name of the function we declared inside. And If you dont already know scala return the result of last statement as return value of the functions and explicit return is not necessary which is the case in other functional languages too.

Now we can use this function to generate our desired functions like following,

```scala Derived functions

def sumInts         = sum(x => x)
def sumSquares      = sum(x => x * x)
def sumCubes        = sum(x => x * x * x)

/*
 x => x * x is just a syntactic sugar and  the short version of

 def square(x: Int): Int = {
    x * x
 }

 then we could just use
 def sumSquares  = sum(square)
*/

```

Here we are defining these functions by passing in an anonymous functions to the function ```sum``` . An anonymous function of the form ```x => x * x``` create an function which takes a single argument ```x``` and returns ```x * x``` which is the square of the argument.

Other than this we could also use the ```factorial``` function from [my last blog post](/blog/2014/12/a-bite-of-functional-programming/) and declare a sum of factorials function like

```scala Sum of factorials

def sumFactorials = sum(factorial)

```

and after these we can just use any of ```sumInts(10, 20)``` or ```sumSquares(10 , 20)``` etc to get the sum of the outputs of those functions.

These are very trivial things for any good functional programmer or any great programmer in general but the concept of this amazed me a lot. And I am really happy that I can do this now.

We could improve the declaration of our ```sum``` function to use ```tail recursion``` like I talked about in [my previous post](/blog/2014/12/a-bite-of-functional-programming/).

Maybe thats all from me for ```scala``` for the time being, I decided to learn ```Clojure``` instead and will be writing on that as I learn new cool things.

I am not a very good writer and not a very good programmer either. But I am trying to be good at both. You could always ask me things in comment and I will try my best to clarify things more if I can.
