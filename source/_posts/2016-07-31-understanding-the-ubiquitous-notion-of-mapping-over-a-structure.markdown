---
layout: post
title: "Understanding the ubiquitous notion of Mapping a function over a structure"
date: 2016-07-31 10:53:05 +1000
comments: true
categories: functional-programming, coding 
---

## What is Map?

The notion of Map has become so prevalent in all programming languages that almost everyone has some kind of exposure to it. Generically speaking, `Map` means applying a function to values which are inside some kind of structure. 

We usually use it a lot to apply a function to a list of values e.g mapping over the values of an `Array or List` to be more specific.

In this post we will first see some examples of mapping in different languages and then we dive deep into it's internals in terms of `Haskell` and maybe finish with a better understanding of how everything fits together. Saying that lets start.

<!-- more -->

## Some examples of Mapping in some popular langauges

### JS and JQuery

``` JS Map

// copies from MDN
var numbers = [1, 2, 3];
var roots = numbers.map(function(x) {
    return Math.pow(x, 2);
}); // => [ 1, 4, 9]
// the above code squares all values of the array

// In jQuery we always do things like
$( ":checkbox" )
  // which returns all checkboxes
  .map(function() {
    return this.id;
  })
// it applies the anonymous function over that collection of checkboxes and just return
// their ids

```

### Ruby

``` Ruby Map
[1, 2, 3].map { |n| n * n } #=> [1, 4, 9]
// which just squares all the values inside the array
```

### PHP
``` Php Map
$square = function ($n) {
  return $n * $n;
}

$arr = [1,2,3]
array_map($square, $arr) // => [1, 4, 9]
// Array is a primitive data type in PHP so we can't do
// $arr.map.....
```


## Map internals with Haskell

So lets see how an implementation of a `Map` function in Haskell looks like

```Haskell Map implementation
map :: (a -> b) -> [a] -> [b]
-- type signature of map, which says map takes a function ( a -> b ) and a list ( [a] )
-- apply the function on all elements of that list and returns another list ([b])
map f [] = []
-- base case if the function is empty just return an empty list
map f (x:xs) = f x : (map f xs)
-- otherwise apply the function to first element ( head )
-- and call map recursively on the rest of the list ( tail )
-- on a side note (:) is called the cons constructor which adds an element to a list
-- lets see how a simple function like (+1) it will apply to the list [1,2]
-- map (+1) [1,2]
-- (+1) 1 : (+1) 2 : [] ( this [] is coming from the base case of the recursion above) 
-- 2 : 3 : []
-- [2, 3]
```
You see how partially applied functions ( `(+1)` above ) comes in handy.

It's pretty simple right. But why contrain this great idea to only a list of values. Haskell takes it a step further
and generalizes it with an Abstraction named `Functor`. I will not get into details of `Functors` but will just use that to demonstrate the generic notion of mapping over a structure. So what is this structure thing I am talking about.

In the application above the list is the structure, it's characteristic is to hold a list of values. `Tree` is another structure which has `leafs` and `branches`. They are ways of organizing data.

The notion of `map` in generic sense is to apply a function over a strucure. Think of it like you want to throw a function over some kind of structure and apply it to the values inside. This can be any kind of strucure defined by you or in haskell `Types`. Haskell has a `TypeClass` named `Functor` which any custom type can implement and thereby define how map should work for that type.

Lets see how this works for a `Tree` type.

```Haskell Tree
data Tree a = Leaf a | Branch (Tree a) (Tree a) deriving (Show)
-- in laymen term is defines a tree data type, which has either a leaf or a branch with left
-- or right subtree
-- we can also see it's a recursive data structure as it's left and right subtree holds same structure
-- we get a sense of that from the definition itself
```

Now how should map works for this structure. In case of list we only had a list of values. In case of our `Tree` its a bit different but we still have values in the leaves which we may want to transform, as the owner of the `Data Type` we know best how to apply the function and on which data inside our structure to apply it to.

Let's see how we can define a `map` function for our tree.

```Haskell treeMap
treeMap :: (a -> b) -> Tree a -> Tree b
treeMap f (Leaf x) = Leaf (f x)
treeMap f (Branch left right) = Branch (treeMap f left) (treeMap f right)

-- example
treeMap (*2) (Branch (Branch (Leaf 1) (Leaf 2)) (Branch (Leaf 3) (Leaf 4)))
-- which multiplies 2 to every leaf of the tree and returns the new tree as below
Branch (Branch (Leaf 2) (Leaf 4)) (Branch (Leaf 6) (Leaf 8))
-- See all the leafs has been multiplied by 2
-- lets see function composition fits in here if we first want to multiply by 2 and then negate the numbers
-- we can do something like
treeMap (negate. (*2)) (Branch (Branch (Leaf 1) (Leaf 2)) (Branch (Leaf 3) (Leaf 4)))
-- function composition for the win
-- it should return
Branch (Branch (Leaf (-2)) (Leaf (-4))) (Branch (Leaf (-6)) (Leaf (-8)))
```

Oh I forgot to say `map` is a way of transforming data as thats the only way you can chagne data in `Pure functinal programming` as you can't mutate them in place. But we are lazy programmers, why define a new method or force others to define new map functions for our data types. Haskell abstracts this with `Functor` as I mentioned above. So lets define an instance of `Functor` for our tree data type.

```Haskell Functor instance of Tree

instance Functor Tree where
    fmap f (Leaf x) = Leaf (f x)
    fmap f (Branch left right) = Branch (fmap f left) (fmap f right)
-- it's same as our treeMap and does the same thing
```
 Once we have our `Functor` instance for `Tree` we can use the generic `fmap` function now to do the same thing we did above
 
```Haskell fmap example
 let tree = (Branch (Branch (Leaf 1) (Leaf 2)) (Branch (Leaf 3) (Leaf 4)))
  fmap (*2) tree
  -- or
  fmap (neg . (*2)) tree
```
  
  So the generic notion is `Map` is not just for array or list of values, rather it's a very powerful idea to apply any function over any kind of structures no matter how complex they are.
  
  Hope you enjoyed it. We may talk more about `Functors` later and see how haskell takes this idea of mapping to a whole new level and make it a lot more useful.

