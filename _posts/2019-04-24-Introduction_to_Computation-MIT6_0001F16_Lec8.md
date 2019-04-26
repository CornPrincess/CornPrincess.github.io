---
layout: post
title:  "Introduction_to_Computation-MIT6_0001F16_Lec8"
date:   2019-04-26 23:50:12 +0800
categories: Introduction_to_Computation
tags: Python 
---

* content
{:toc}

第八节课，这节主要讲python里class的概念，接下来摘抄部分slide。

# OBJECT ORIENTED PROGRAMMING

## EVERYTHING IN PYTHON IS AN OBJECT (and has a type) 

 - can create new objects of some type
 - can manipulate objects
 - can destroy objects
  - explicitly using del or just “forget” about them
  - python system will reclaim destroyed or inaccessible objects – called “garbage collection”
 
## ADVANTAGES OF OOP

 - **bundle data into packages together** with procedures that work on them through well-defined interfaces
 - **divide-and-conquer** development
  - implement and test behavior of each class separately 
  - increased modularity reduces complexity
 - classes make it easy to reuse code
  - many Python modules define new classes
  - each class has a separate environment (no collision on function names)
  - inheritance allows subclasses to redefine or extend a selected subset of a superclass’ behavior

## THE POWER OF OOP
- bundle together objects that share
 - common attributes and
 - procedures that operate on those attributes
- use abstraction to make a distinction between how to implement an object vs how to use the object
- build layers of object abstractions that inherit behaviors from other classes of objects
- create our own classes of objects on top of Python’s basic classes