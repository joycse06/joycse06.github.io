---
layout: post
title: "Revisting Data Structure : Linked List Part 1"
date: 2014-12-06 21:27:38 -0500
comments: true
categories: programming cpp data-structure
---

Linked List is a very important data structure in terms of the concepts required to understand it's inner working and the operation that can be performed on it.

In this post we will be exploring structure of Linked List and operations that can be performed on Linked list.

So without further ado lets dive into the details.

<!-- more -->

For the sake of simplicity our linked list will be holding only a integer and an next link filed. We will be using a structure to accomplish this.

``` cpp Linked List Structure

struct Node {
    int data;
    struct Node* next;
}

```

So it's a simple structure which only has a int member and a next pointer of the same struct type to hold the address of the next item in the linked list.

As we have the template ready now, how can we create an instance of the structure? It's simple we can either use C style ```malloc()``` or CPP style ```new``` operator. We will use malloc for this post. So creating an instance of structure is like

``` cpp Malloc
struct Node* temp = (struct Node*) malloc(sizeof(struct Node));

```

In this line what we are doing is declaring and defining an pointer of type struct Node and dynamically allocating memory for it using malloc. ```Malloc``` takes the size of the data type, allocate that amount of memory dynamically at runtime in the heap( Head is the free memory from where every application can request memory from Operating system using function calls).

after this we can use this instance instance with pointer dereferencing or using the pointer operator like follows:

``` cpp accessing properties of the instance of struct

(*temp).data = 10;
// or we can simple use
temp->data = 10;

```

# Inserting data into Linked list

Data can be inserted at different points of the linked list like at the

* at the start of the List
* at the end of the List
* at nth position

## Insertion at the front of the list

Lets write a function which takes the head of the list and an integer and inserts that number at the front of the list. An example implementation is follows

``` cpp InsertAtFront Function

struct Node* InsertAtFront(struct Node* head, int data){
	struct Node * temp = (struct Node * ) malloc(sizeof(struct Node));
	(*temp).data = data; // or temp->data = x;
	(*temp).next = head; // or temp->next = head;
    head = temp;
    return head; // Return the new head
}

```

The function is pretty straight forward. We create a new node, add the data, point it the current head, change head to point to the new node and return the new head.

## Inserting at the end of the list

Insert at the end function will look like

``` cpp InsertAtEnd function

struct Node* InsertAtEnd(struct Node* head, int data) {
    struct node* temp = (struct Node * ) malloc(sizeof(struct Node));
    temp->data = data;
    temp->next = NULL;
    if(head == NULL){
        // the List is empty
        head = temp;
    }else {
        struct Node* last = head;
        while(last->next != NULL)
            last = last->next;
        last->next = temp;
    }
    return head;
}

```

In this function we first create a new node with the data and next pointer as NULL; Then we check if the list is currently empty we just make this new node the head of the list.

If the list is not empty we have first traverse the list to go to the last element of the list and then just add the newly created node in the next link of the last element;

## Inserting at the Nth Position in the list

for the sake of simplicity we will assume that the position argument will always be within the list and wont do out of bounds handling in this version.

``` cpp InsertAtNthPosition function

struct Node* InsertAtNthPosition(struct Node* head, int data, int pos){
    // Lets do it CPP style this time
    Node* temp1 = new Node();
    temp1->data = data;
    temp1->next = NULL;
    if( pos == 1 ) {
        temp1->next = head;
        head = temp1;
        return head;
    }else {
        Node* temp2 = head;
        for ( int iter = 0; iter < pos - 2; iter++) {
            temp2 = temp2->next;
        }
        temp1->next = temp2->next;
        temp2->next = temp1;
    }
}

```

Thats all for today. We will be talking about other operations on linked list like printing, deleting etc in future posts. Enjoy!