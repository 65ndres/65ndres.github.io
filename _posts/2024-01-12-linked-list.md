---
title: Linked Lists
date: 2024-01-12 12:00:00 -500
categories: [Back to Basics]
tags: [Basics, Computer Science, Linked Lists, Python ]
---


A linked list is the most sought-after data structure when it comes to handling dynamic data elements. I personally have never use them in a professional setting but nonetheles they are an important topic to understand because they fall under the catergory of Data Structures.


## Game Plan

* What it is.
* Types of Link Lists.
* Types of Operations.
* Code examples.

### What it is

A Linked list is a list where the nodes are linked together, Each node contains data and a pointer. That way they are linked together. Each points to where in the memory the next node is placed. The last node contains null in its second field because it will point to no node.

An important characteristic of link lists is that they are linear data structure, which means that there is a sequence and an order to how they are contructed and traversed. We can think of a linear data structure like a set of stairs: in order to get to the last we most pass through all the steps (idially). Linear data structures, however, are the opposite of non-linear structures. In non-linear data structures the nodes dont have to be arranged in order and can be connected to more than one node.

### Types of Link Lists

#### Singly Linked List

is the simplest type of linked list in which every node contains some data and a pointer to the next node of the same data type.

#### Doubly Linked List

A doubly linked list or a two-way linked list in a more complex because it contains two pointers, one for the next and one for the previous.

#### Circular Linked List

A circular linked list is a type of linked list where the last nodes next pointer points back to the first node of the list, creating a circular structure.

### Types of Operations

* Traversing: To traverse all nodes one by one.
* Insertion:  To insert new nodes at specific positions.
* Deletion:   To delete nodes from specific positions.
* Searching:  To search for an element from the linked list.


### These are a few important differences between Linked List and Arrays

#### Linked List:
* Data Structure: Non-contiguous
* Memory Allocation: Typically allocated one by one to individual elements
* Insertion/Deletion: Efficient
* Access: Sequential
#### Array:

* Data Structure: Contiguous
* Memory Allocation: Typically allocated to the whole array
* Insertion/Deletion: Inefficient
* Access: Random