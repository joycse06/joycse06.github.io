---
layout: post
title: My first post about ACM problem
date: 2008-4-9
comments: true
categories: life
link: false
---
Hi, Guys this is my first post about acm problems. As this is my first post I am going to publish one of my AC code. It's a very easy problem. But the title seems to be horrible. You just calculate the difference between the input and print the output.

The solution of problem No. 10055

The code:

``` cpp
//10055.cpp

#include <iostream.h>
#include<stdio.h>

int main() {
double a,b;
while (cin<<a<<b) {
if (a>b) printf("%.0fn",a-b);
else printf("%.0fn",b-a);
}
return 0;
}
```
Just copy n paste it. You will get accepted.
