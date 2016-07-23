---
layout: post
title: "Intro to Lambda Calculus and it's relationship to Functional Programming"
date: 2016-07-23 17:38:18 +1000
comments: true
categories: functional-programming, code
---
This post is written based on the First Chapter (All you need is lambda) of the [HaskellBook](http://haskellbook.com/) which is great book for learning Haskell. All the code examples in this post will be using Haskell but should be understandable to anyone having decent experience in any programming language.
Lambda calculus is a model of computation devised in the 1930s by Alanzo Church. It's one process for formalizing a method. Like Turing machines it formalizes the concept of effective computability, thus determining which problems or classes of problems, can be solved.
You might be thinking but thats Calculus(Math), how is it related to functional programming or programming in general. It is indeed deeply related.

Lets define Functional programming, we know that functional programming is a programming paradigm that relies on functions modeled on mathematical functions. The essence of functional programming is that programs are a combination of expressions. Expressions include concrete values, variables and also functions. Functional programming languages are all based on the lambda calculus. Though some of them incorporate features into the language that are not translatable into lambda expressions. Haskell doesn't do that because it's a pure functional language. We will not get into what it means for a functional programming language to be *Pure* because it's an topic of it's own.

## What is a function ( in Math )?
We all have studied in High school math( Set theory ), that a function is a relation between a set of inputs and a set of outputs. The function itself defines and represents the relationship. For example, when you apply the addition function to two inputs, it maps those two inputs to an output -- the sum of those numbers. A simple function can be `f(x) = x + 1` which takes one argument named `x`. The relationship between the input and the output is described in the function body. For this function, it will add 1 to whatever value x is and return that result.

When we apply the above function to a value, such as 1, we substitute the value in for x:

```
f(1) = 1 + 1 = 2
```

*Understanding functions in this way - as a mapping of a set of inputs to a set of outputs is crucial to understanding functional programming.*

## Structure of Lambda Terms

The lambda calculus has three basic components, or lambda terms: `expressions`, `variables` and `abstractions`. The word `expression` refers to a superset of all those things: an expression that can be a variable name, an abstraction or a combination of those things.

An *abstraction* is a *function*. It is a lambda term that has a head and a body and  is applied to an argument. An argument is an input value. The head of a function is a `𝜆(lambda)` followed by a variable name.The body of the function is another expression. So a simple function might look like: `𝜆x.x` . It's an identity function because it just return the argument passed to it. In the previous example of `f(x) = x + 1` we were talking about a function called `f`, but the lambda abstraction `𝜆x.x` has no name. It's an *anonymous function (we all are familiar with this term, because it's a very powerful idea and almost all imperative languages have some sort of implementation of it too)*. In the definition the dot separates the parameters of the lambda function from the body.

### Beta reduction
When we apply a function to an argument, we substitute the input expression for all instances of bound variables within the body of the abstraction. We then also eliminate the head of the abstraction, since it's only purpose was to bind a variable. This process is called *beta reduction*.

An example of a beta reduction can be:

``` bash Example of Beta Reduction
we have this lambda 𝜆x.x ,
if we want to apply the number 2 into the lambda above,
we simply substitute 2 for each bound variable in the body of the function,
and eliminate the head:
(𝜆x.x) 2
2
As another example, if we have 𝜆x.x+1
then if we apply 2 it becomes
(𝜆x.x+1) 2
==> 2+1
==> 3
```
I could continue and do some more complex *beta reductions* but will restrain myself

### Multiple arguments
Each lambda can only bind one parameter and can only accept one argument. Functions that require multiple arguments have multiple, nested heads. When you apply it once and eliminate the first(left-most) head, the next one is applied and so on. This formulation is named after *Haskell Curry* and is commonly called currying. So
```
𝜆xy.xy
is actually convenient shorthand for two nested lambdas( one for each argument, x and y)
like following
𝜆x.(𝜆y.xy)
```
### Evaluation is simplification
There is a thing named *beta normal form* in lambda calculus. It's a form when you can't beta reduce(apply lambdas to arguments) the term any further. This corresponds to a fully evaluated expression, or, in programming a fully executed program. In terms of executing a program it could mean continue applying lambda functions to their arguments until it become irreducible at which point the execution will finish. For example `𝜆x.x` is in beta normal form as we can't reduce it any further with what we have.
But `(𝜆x.x) 2` is not because it's an lambda awaiting an application so we can reduce it to `2` by replacing instances of `x` with `2` in the body of the the lambda. `2` is in *beta normal form* because it's a literal value and we can't reduce it any further.

## Relationship with functional programming
Like I said earlier Functional programming languages are based on *Lambda Calculus*, as we have learned a bit more about *lambdas* we can relate more about how they are connected. We might have heard the phrase *Functions are first class citizens in functional languages*. Lets explain that a bit. Imperative languages usually treat functions differently than other values(variables, objects and other inhabitants of a program) in the program though that is changing with omnipresence of lambdas(anonymous) function everywhere.

But in functional languages functions are just like any other value. You can toss function around, return another function from a function body, compose functions to build complex functionalities from simple functions and all sorts of cool things.

And if the function is a pure( like mathematical one) which always return the same output for the same input you don't even need to care where the function is being evaluated, be it in another core of the same machine or in the cloud because you don't need to care. All you want is the result of applying the function to an input.

*Building Software is a complex task* and Composability is a(*or is the*) way to handle complexity. You start with simple building blocks and compose them to build complex program to solve complex problems. Pure functions are such building blocks.

In a way, programs in functional languages are like reducing expressions(remember *beta reduction* from last paragraph). The compilers continue evaluating expressions until it reaches a point when it can't reduce the expression anymore and the execution is complete.

I wont get any deeper into this in this post. Research and learn more if you are still not bored.

## Haskell, Haskell, Haskell

Haskell is purely based on *Lambda Calculus*. In fact it's typed lambda calculus. On the surface level it has some syntactic sugar which could lead one to believe this thing doesn't have a equivalent form in lambda calculus. But it ultimately boils down a very simple intermediate language construct which is then evaluated by STG(Spineless Tagless Graph-Reducing Machine) which is a abstract virtual machine and convert that code into binary. It has some very powerful abstractions understanding which properly could enable anyone to write better code in any other languages(This is not something I am saying myself, I have heard it countless times from many great programmers). By the way, Haskell is not the only language in which you can learn functional programming, but it's definitely one of the good ones.

### Some Haskell examples

I will not explain any of the syntaxes, will just try to relate some of the things I mentioned above with haskell code.
You can type them in `ghci` repl and they should run fine.

#### A lambda in haskell is very similar to lambda from lambda calculus,
``` haskell A simple Lambda
\x -> x + 1
-- which takes a single param and add 1 to it, we can use it like
(\x -> x + 1) 2
```
#### Haskell supports currying, so you can define function which can take multiple arguments, like
``` Haskell Simple Addition Function
add x y = x + y
-- which is in de-sugared form becomes similar to
let add = \x -> \y -> x + y
```
#### Composing functions in Haskell is very easy
``` Haskell Function composition
-- lets define a square function
let square x = x * x
-- lets define a sum function which takes a list and add all the values
let sum = foldr (+) 0

-- and another function which negate the argument
let neg = negate -- negate is a standard function

-- now using the above function we can define our complex function( hint: compose them)
let negateSquaredSum = neg . square . sum

-- now you can call it like
negateSquaredSum [1,2,3,4]
-- which will result in -100
```
In our case they are simple function. But they can be anything, and we can then execute the smaller building blocks ( `neg`, `square` and `sum`) anywhere(could be other cores or maybe in the cloud) and then combine the results.

Its not about Haskell, you can do the above in any language, it's about the idea of `function composition` or in general `composition`.

The best thing about all the above codes no matter how complex they become, they are type checked by the compiler. Haskell is a statically and strongly typed language. So if your program compiles there's a much greater chance it will work.

Enough for a single blog post. Hope you enjoyed it. Stay tuned for more Haskell and functional programming related posts.
