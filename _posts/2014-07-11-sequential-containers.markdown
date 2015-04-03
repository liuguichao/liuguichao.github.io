---
layout: post
title: "Sequential Containers"
date: 2014-07-11 13:37:28 +0800
comments: true
category: C++
tags: [Containers]
---
**The Sequential Containers**

![](http://i.imgur.com/0PEOjIC.jpg)

**Iterator Ranges**

An *iterator range* is denoted by a pair of iterators each of which refers to an element, or to one past the last element,in the same container. These two iterators,
often referred to as *begin* and *end*—or (somewhat misleadingly) as *first* and *last*—mark a range of elements from the container.

The name *last*, although commonly used, is a bit misleading, because the second iterator never refers to the last element of the range. Instead, it refers to a point one past the last element. The elements in the range include the element denoted by *first* and every element from *first* up to but not including *last*.

This element range is called a **left-inclusive interval**. The standard mathematical notation for such a range is**[ begin, end)**.indicating that the range begins with *begin* and ends with, but does not include,
*end*. The iterators begin and end must refer to the same container. The iterator end may be equal to begin but must not refer to an element before the one denoted by begin.

**Subscripting and Safe Random Access**

If we want to ensure that our index is valid, we can use the **at** member instead.The at member acts like the subscript operator, but if the index is invalid, atthrows
an out_of_rangeexception.

**capacity and size**

It is important to understand the difference between *capacity* and *size*. The *size* of a container is the number of elements it already holds; its *capacity* is how many elements it can hold before more space must be allocated.

**Numeric Conversions**

![](http://i.imgur.com/Cq46gBy.jpg)

**Container Adaptors**

In addition to the sequential containers, the library defines three sequential container adaptors: **stack**, **queue**, and **priority_queue**. An adaptor is a general concept in the library. There are container, iterator, and function adaptors. Essentially, an adaptor is a mechanism for making one thing act like another. A container adaptor
takes an existing container type and makes it act like a different type. For example,the stack adaptor takes a sequential container (other than array or forward_list)and makes it operate as if it were a stack.The **stack** type is defined in the stack header.The **queue** and **priority_queue** adaptors are defined in the queue header.