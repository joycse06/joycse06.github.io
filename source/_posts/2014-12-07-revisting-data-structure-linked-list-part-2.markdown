---
layout: post
title: "Revisting Data Structure : Linked List Part 2"
date: 2014-12-07 22:57:17 -0500
comments: true
categories: programming cpp data-structure
---
 In previous part of the series We talked about Linked List in General and it's implementation details in C++ Programming language. We saw how to layout the structure of a Linked List and how to insert data into the list.

 In this episode we will be talking about how we can print all the nodes of a linked list. So we can assume from the nature of Linked list that we have to traverse the whole list and print each nodes value.

## Printing Linked List through Iteration Method

 The definition of the print function using iteration is as follows:

``` cpp printList function

 void printList(struct Node* head){
    struct Node* temp = head;
    printf("\nList is: ");
    while(temp != NULL) { // exit condition
        printf(" %d", temp->data); // Print the value of the Current Node
        temp = temp->next;        // Proceed to the next Node
    }
    printf("\n");
 }

```

<!-- more -->

The definition of the function is fairly straight forward. We take ```head``` of the list as argument and then using a while loop we traverse through the List and print the value at each node.

Lets see how can we achieve the same using Recursion.

## Printing Linked List through Recursion

We know when we use recursion in a function there must be two cases

1. Base case or exit condition: If we dont define a exit condition while using recursion, then the recursion won't stop and the function call stack will overflow eventually terminating the program at some point.

2. Recursive case : for all other general case we can call the same method recursively.

``` cpp printListRecursive function

void printListRecursive(struct Node* p) {
    if(p == NULL) return; // exit condition
    printf("%d ", p->data);  // Print the value
    printListRecursive(p->next); // Recursive call
}

```

Recursion is a very powerful tool, but you have to be careful while designing it. Great Power comes with Great Responsibilities, it's very true for recursion too.

What happens in this recursive version is we check if the pointer argument ```p``` is null, if it is NULL then we return from the function. If it's not that means we are not at the end of the list, then we print the data of current node and call the same function recursively.

 How recursion works internally requires knowledge of Function call stack which calls for another blog post, I will try to write a blog post on the simulation of recursion in memory.

 Thats all for today. We will talk about delete nodes from list and reversing a link list in next episodes. Stay tuned!

