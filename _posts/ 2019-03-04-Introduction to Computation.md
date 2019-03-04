---
layout: post
title:  "MIT6_0001F16_Lec1"
date:   2019-03-04 20:25:12 +0800
categories: Introduction to Computation 
tags: Python 
---

* content
{:toc}
本课程来源为MIT6_0001F16公开课，希望对大家有所帮助，有疑问的地方也请大家提出来。



## Leture1

先干了slide里的三碗鸡汤！

position yourself to succeed!

New to programming? PRACTICE. PRACTICE? PRACTICE!

can’t passively absorb programming as a skill



## WHAT DOES A COMPUTER DO

### Fundamentally:
- performs calculations 

a billion calculations per second! 

- remembers results 

100s of gigabytes of storage! 

### What kinds of calculations?
- built-in to the language
- ones that you define as the programmer 

computers only know what you tell them. (not have a mind.)

## Types of Knowledge

### Declarative knowledge is statements of fact. 

- someone will win a Google 

- Cardboard before class ends 

### Imperative knowledge is a recipe or “how-to”. 

1.   Students sign up for raffle 
2.   Ana opens her IDE 
3.   Ana chooses a random number between 1st and nth responder 
4.   Ana finds the number in the responders sheet. Winner! 

## Basic Machine Architecture

![image-20190304214358779](/Users/zhoutianbin/Library/Application Support/typora-user-images/image-20190304214358779.png)

## Object

objects are

- scalar (cannot be subdivided)
- non-scalar (have internal structure that can be accessed)

### Scalar Objects

- int – represent integers, ex. 5
- float – represent real numbers, ex. 3.27
- bool –representBooleanvaluesTrueandFalse
- NoneType – special and has one value, None
- can use type() to see the type of an object

### TYPE CONVERSIONS (CAST)

can convert object of one type to another

- float(3) converts integer 3 to float 3.0 
- int(3.9) truncates float 3.9 to integer 3