---
layout: post
title: "A bite of Functional Programming"
date: 2014-12-14 11:25:02 -0500
comments: true
categories: programming functional
---

I love programming languages and trying a new one is always fun. A year or so ago I first heard the name of functional paradigm of programming. I have came across blog posts, saw people talking about functional programming in programming communities over the last year. I have seen countless veteran programmers referring ```SICP``` as a must read for anyone who wants to be really good in programming. When I went over to search for ```SICP``` found the videos of original lecture of ```Hal Abelson``` and I heard the name of ```LISP``` from him while checking the first lecture.

From then on I have been cherishing a desire to learn a functional language and went ahead to try learning some like ```Haskell```, ```Clojure``` and ```Scala``` though couldn't get too far with anyone of them. But after every few months something inside me pushes me to get back to learning a functional language well and I spend several days reading blogs, trying out simple things.

<!-- more -->

Enrolled in ```Functional Programming in Scala``` Course in ```CourseEra``` two times but couldn't or didn't finish in either of those enrolments. But while doing the exercise of first/second chapter of the scala course I did some trivial things like ```finding factorial of a number``` and ```a parantheses matching program``` which got me more interested into it.

Though I am a know-nothing in functional programming, I decided I will be good enough in one of the functional languages someday. But I have a habit of digressing from the main target sometimes specially while learning something and over this very short period of time I have checked blogs/videos on many things related to functional paradigm like ```monads```, ```monoids```,  ```STM(Software Transactional Memory```, ```Programming without side effects```, ```Code as data```, ```Higher Order functions``` and many more. Though most of the things went over my head, I understood they are much profound ideas or concepts and understanding those properly will definitely make me a better programming.

 Enough talking, lets do some trivial functional programming together maybe ```finding the factorial of a number``` . Lets do it in scala first.

```scala factorial.scala

def factorial(n:Int):Int =
    if (n==0)
        1
    else
        n * factorial(n-1)

```

This program is fairly straight forward for anyone with basic programming background. The syntax can seem alien to anyone like me who doesn't know ```Scala``` but the functional body is pretty simple for anyone who knows ```recursion``` . We know factorial of a number is defined as ```factorial(n) = n*(n-1)*....*1``` . So in terms of recursion from ```Imperative Paradigm``` we see the base case is when value of ```n``` reaches ```zero``` we return one otherwise we call the same function recursively.

so the call stack for a call of ```factorial(5)``` would look like

```
factorial(5)
5*(factorial(4))
5*(4*(factorial(3)))
5*(4*(3*(factorial(2))))
5*(4*(3*(2*(factorial(1))))))

```

We see with each call we get more and more ```*'s``` and all steps stacks up in the call stack in memory which definitely have a limited amount of space. So if the number we want to calculate the factorial for is big enough we can get good old ```Stack Overflow Error```. So whats the solution to this. Most good languages has a features called ```Tail Recursion Optimization``` which is, if the  ```recursive``` call is of the same form as the main function call then the compiler/interpreter will resuse the same call stack instead of creating a new one each time.

But we cant just make the last call ```factorial(n-1)``` in that case we will lose the number n. So to make it tail recursive we can redefine the function like

```scala factorial

def factorial(n:Int, accumulator: Int): Int =
    if ( n==0 )
        accumulator
    else
        factorial((n-1), n*accumulator)

```

Now the last recursive call is of the same function signature and scala interpreter is design to optimize it with the feature called ```Tail Recursion Optimization``` . But this one also has one limitation, it requires the user of the function to pass 1 as the value of the ```accumulator``` which is a burden for the user and it wont return correct result if the user doesn't provide 1 as the value of the ```accumulator``` . To avoid this and provide user the same function signature like ```factorial(number)``` we can use a local function inside the main function. Such a function definition will look like

``` scala factorial
def factorial(number: Int) : Int = {
    def factorialWithAccumulator( number: Int, accumulator: Int) : Int = {
        if (number == 1)
            return accumulator
        else
            factorialWithAccumulator( number - 1, accumulator * number)
    }
    factorialWithAccumulator(number, 1)
 }

```

Now this function can be called like  ```factorial(5)``` and we can use it to calculate factorial of much bigger number as it's optimized we wont get  ```Stack Overflow ``` error. Another thing we can do is to use the  ```@tailrec ``` annotation before any function you expect the compiler to optimize and in that case the compiler will warn you if it cant optimize that recursive function.

That's all for today. I will be writing more about functional programming as I learn more about it.
