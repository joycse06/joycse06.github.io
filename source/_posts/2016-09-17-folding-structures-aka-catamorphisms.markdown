---
layout: post
title: "Folding Structures aka Catamorphisms"
date: 2016-09-17 13:28:58 +1000
comments: true
categories: fp coding haskell
---

## What does it mean by Folding or Reducing a data structure?

Folding or Reducing is the notion of creating a summary value from a list of values contained inside a certain structure.
The structure can be as simple as list of values or a complex structure. The way one will want to reduce the structure is something
that will depend on the end goal or the structure of data. Folding or Reducing generalizes this notion which are also known as Catamorphisms.

<!-- more -->

## Deriving a fold function in Haskell

I will be providing the code examples in Haskell but they should be generic enough to understand the concept. Lets say we have the following list of values

```haskell
values = [1, 2, 3, 4, 5]
```
and we want to know the sum of all these values. In the imperative world we would start with a loop of some kind which will iterate over the values and add them

```
The following is pseudo-code
sum = 0
for (x in values) {
  sum = sum + x
}
```

now say we also need to compute the product of all values in the list, we can do something like following

```
The following is pseudo-code
product = 1
for (x in values) {
 product = product * x
}
```

Similary we may need to find the maximum or minimum of the values. And we are only considering a simple list, it can a tree or anything else.
But the notion of generating a summary value from a structure remains the same.

We programmers are lazy and don't like to write too much boilerplate code. So it would be cool if we could generalize this notion of reducing structures.

What do we need for that, we need a stuructre of some kind, we need a binary operation of some kind (i.e `+` or `*`) which takes two values and generate a single value.
And we also need an initial value to start with which is `0` or `1` above, if we look carefully we will see the initial value is related to the operation. It's a unit value for the
operation like if you add `0` to something, it doesn't change, same for `1` and multiplication.

Lets define the `fold` function in Haskell,

```haskell fold function
-- our fold function takes another function which takes two values of type a and b
-- and return b, which is our binary operation above,
-- then an intial value of type b and a list [a]
-- and return b
fold :: (a -> b -> b) -> b -> [a] -> b
-- our function is recursive, this is the base case where the list has no more elements left
fold _ acc [] = acc
-- this is the recursive case, where we call the function
-- with first element of the list and another recursive call to fold itself
fold f acc (x:xs) = f x (fold f acc xs)
```

In the type signature of `fold` `a` `b` are type variables so they can be of any types. The definition above is the called `foldr`,
there's also another fold called `foldl`, their implementation differs on the recursive case but details about that can be put off for
another post.

## Usage of the fold function

```haskell
values = [1, 2, 3, 4, 5]

sum = fold (+) 0 values -- sum will be 15

product = fold (*) 1 values -- product will be 120

```

I am not going to go any further in this blog post but a lot can be done using fold.

But before finishing lets have a look at how a simple call to `fold` is evaluated under the hood,
by writing out all the recursive calls

```
fold (+) 0 [1, 2, 3]
1 + (fold (+) 0 [2, 3])
1 + (2 + (fold (+) 0 [3]))
1 + (2 + (3 + (fold (+) 0 [])))
1 + (2 + (3 + 0))
1 + (2 + 3)
1 + 5
6
```

Most languages has some kind of `fold` function now. Hope you understood how folds works generally.

So go ahead and read up on how to reduce an `array` or a similar data structure in your favorite language and start using it.
