---
layout: post
title:  "Introduction_to_Computation-MIT6_0001F16_Lec5"
date:   2019-03-27 23:50:12 +0800
categories: Introduction_to_Computation
tags: Python 
---

* content
{:toc}

Ana教授的第五节课，还是那么通俗易懂，先分享一个学习编程很好的网站：http://pythontutor.com/（可能需要科学上网）本篇学习笔记某些截图出自该网站。

# Compound Data Types
## Tuple
- an **ordered** sequence of elements, can mix element types
- cannot change element values,**immutable** ()
- represented with **parentheses**
- conveniently used to swap variable values:
 - (x, y) = (y, x)

## List
- **ordered** sequence of information, accessible by index
- a list is denoted by **square brackets**, []
- a list contains elements
 - usually homogeneous (ie, all integers) 
 - can contain mixed types (not common)
- list elements can be changed so a list is **mutable**

### OPERATIONS ON LISTS
#### ADD
- add elements to end of list with L.append(element) -- mutates the list!
- combine lists together use L.extend(some_list) -- mutate list

#### REMOVE
- delete element at a specific index with del(L[index]) 
- remove element at end of list with L.pop(), returns the removed element
- remove a specific element with L.remove(element) 
 - looks for the element and removes it
 - if element occurs multiple times, removes first occurrence
 - if element not in list, gives an error
![list_remove][1]

#### CONVERT
- convert string to list with list(s), returns a list with every character from s an element in L
- can use s.split(), to split a string on a character parameter, splits on spaces if called without a parameter
- use ''.join(L) to turn a list of characters into a string, can give a character in quotes to add char between every element
![list_convert][2]

#### SORTED AND REVERSED
https://docs.python.org/3/tutorial/datastructures.html
![list sorted and reversed][3]

# MUTATION, ALIASING, CLONING
- lists are mutable
- behave differently than immutable types
- is an object in memory
- variable name points to object
- any variable pointing to that object is affected
- key phrase to keep in mind when working with lists is side effects

![object][4]
觉得slide很经典，很容易理解概念，就把几页slide截下了
![aliases][5]
![clone][6]
![sort][7]
![nest list][8]
![iteration][9]
**avoid** mutating a list as you are iterating over it


  [1]: http://static.zybuluo.com/xiaocorn/bj4dphakmod5iudzvyp5h0av/image.png
  [2]: http://static.zybuluo.com/xiaocorn/865tfh7c8d2hgiazy3ziyw3f/image.png
  [3]: http://static.zybuluo.com/xiaocorn/nc9m06bj1jqttrx9dg2wpzhx/image.png
  [4]: http://static.zybuluo.com/xiaocorn/ibachv24un1lpt2kb2l4yg9v/image.png
  [5]: http://static.zybuluo.com/xiaocorn/0aj7irbkswhbva3yb17ll414/image.png
  [6]: http://static.zybuluo.com/xiaocorn/5l66246lwgo4qq6ehl7stusp/image.png
  [7]: http://static.zybuluo.com/xiaocorn/s7qrbzlffseept4izlc0v8gk/image.png
  [8]: http://static.zybuluo.com/xiaocorn/yj3tke3ipkqtiastffxawj32/image.png
  [9]: http://static.zybuluo.com/xiaocorn/l61zz0yox1t9wizc14ffxt3e/image.png