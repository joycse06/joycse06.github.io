---
layout: post
title: "Hello Haskell -- Partial Function Application"
date: 2016-07-24 12:50:05 +1000
comments: true
categories: functional-programming, haskell
---
In this blog post, We will be talking shortly about Haskell's type system( which is a very broad topic, this post won't eve scratch the surface). I will just explain what is needed to explain `partial function application` means and how is it done in Haskell.

N.B. Installing Haskell is very easy with [Stack](https://docs.haskellstack.org/en/stable/README/), if you are on a Mac, you can just do `brew install haskell-stack` to install Stack. Then starting the `repl` is as easy as writing `stack ghci` in your favorite terminal.

Haskell is a statically typed functional programming language. That means everything has a type associated with it and the compiler checks and enforce types at compile time. So there are two phases of code in Haskell one is `Type Level Code` which involves type definition, type constructors etc. Another is `Term Level Code` which is the body of the program. That doesn't necessarily mean you will always have to write type signatures for trivial things. Haskell has a very powerful `Type Inference` system which can infer types from untyped code, so you don't have to specify types of all the things but Haskell will infer them from the code and complain if types of anything doesn't match. It uses `Hindley-Milner Inference` algorithm( well, an extended version of it for `Constrained Types`).
<!-- more -->

## What are types?
Types are how we group a set of values together that share something in common. Sometime that something is abstract and sometime that something is a specific model of a particular concept or domain. We can think of types as `Set` from mathematics to better guide our intuition. So for example what is the type of number 2, well it's a number, a member of the set of `Natural Numbers`. Type of `a` is character. You know this if you have worked with any statically typed programming language before like `C/C++ or Java`. However Haskell takes it to a whole new level.

## How do we define types for a simple addition function?
Let's write the function signature first then we will explain it.

```Haskell Type of an add Function
-- declare the type of the function
add :: Integer -> Integer -> Integer
-- now declare the function itself
add x y = x + y
```
What we are telling the Compiler by this is we have an `add` function which takes two
`integer` and returns result by adding them. If you we remember what I was telling about currying [in my previous post](/blog/2016/07/intro-to-lambda-calculus-and-its-relation-to-functional-programming/) you can't accept two params into a single function. You have to define them a nested lambdas, but it's a syntactic sugar Haskell provides to make it more useful, still make the core conform to whatever lambda calculus provides.
So the above function is pretty straight forward. Lets get to the point and talk about the main focus of this blog post.

## Partial function application
Partial application is the notion of supplying less arguments than the function expects. Let's see an example, we defined an `add` function in previous section, now if we want to define an `increment` function in terms of it we can do something like

```Haskell
inc :: Integer -> Integer
inc = add 1
```
ahh that seems simple, now lets explain how that works. We are assigning value of `add 1` as the body of our `inc` function.

We know from the type signature of `add` that it takes two integer and return their sum. So what happens when we supply it only one argument. We the value `1` gets bound to the first param `x` in the definition of `add` function and we get something like

```haskell
add 1 y = 1 + y
-- which is equivalent to
\y -> 1 + y -- a lambda which just add one to it's argument
```
Which is another function. A partially applied one. If we want to see it from the point of Lambda Calculus it will be more clear

```bash
# our add functions equivalent lambda calculus expression will be
𝜆x.𝜆y.x+y
# now if we apply 1 to it
(𝜆x.𝜆y.x+y) 1
# which gets bound to the left most lambda and it becomes
𝜆y.1+y
# because we can get rid of a lambda after its applied to a value
# finally what we get is a another lambda waiting for an argument

```
Thats what is happening with our `add` function too because haskell is typed lambda calculus. So `add 1` is an partially applied function waiting for another application to a value. It's easy but a very powerful idea, and lets one write cool functions in terms of other simpler functions. *Composability for the win!.*

We can similarly write these functions too,
``` Haskell
multiplyTen = (10 * )
-- * is a infix binary operator, so it requires two arguments
-- as we supplied one, it's a partially applied one and waiting for another
-- argument
-- we can invoke it like
multiplyTen 10
-- and it will give us 100
helloWorld = ("Hello " ++ )
-- ++ is the infix binary string concatenation operator( actually a function
-- requiring two arguments), we only supplied one so it's waiting for another one
-- if we run
helloWorld "World!"
-- it will return "Hello World!"
```

The examples in this blog post were intentionally contrived but The idea of `partial function application` is a profound one and we can do all sort of cool things with it.

Thats all for today. Hope you enjoyed it.
