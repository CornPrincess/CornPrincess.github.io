---
layout: post
title:  "Introduction_to_Computation-MIT6_0001F16_Lec7"
date:   2019-04-24 23:50:12 +0800
categories: Introduction_to_Computation
tags: Python 
---

* content
{:toc}

第七节课，坚持坚持坚持，入门课一定要看完，笔记也一定要写完，不论知识多么简单，都要写下来，因为这是对我自己的约定，一定要完成。

#TESTING, DEBUGGING, EXCEPTIONS, ASSERTIONS

## SET YOURSELF UP FOR EASY TESTING AND DEBUGGING
- from the start, design code to ease this part break program up into modules that can be tested and debugged individually
- document constraints on modules
• what do you expect the input to be?
• what do you expect the output to be?
- document assumptions behind code design

## CLASSES OF TESTS
- Unit testing
• validate each piece of program
• testing each function separately
- Regression testing
• add test for bugs as you find them
• catch reintroduced errors that were previously fixed
- Integration testing
• does overall program work? • tend to rush to do this

## DEBUGGING STEPS
- study program code
• don’t ask what is wrong
• ask how did I get the unexpected result • is it part of a family?
- scientific method
• study available data
• form hypothesis
• repeatable experiments
• pick simplest input to test with

example code:
```python
def get_ratios(L1, L2):
""" Assumes: L1 and L2 are lists of equal length of numbers
Returns: a list containing L1[i]/L2[i] """ 
    ratios = []
    for index in range(len(L1)):
        try:
            ratios.append(L1[index]/L2[index]) 
        except ZeroDivisionError:
            ratios.append(float('nan')) #nan = not a number except:
        except:
            raise ValueError('get_ratios called with bad arg')
    return ratios
```

## Assertions
- want to be sure that **assumptions** on state of computation are as expected
- use an **assert statement** to raise an AssertionError exception if assumptions not met
- an example of good **defensive programming**

ASSERTIONS AS DEFENSIVE PROGRAMMING:
- assertions don’t allow a programmer to control response to unexpected conditions
- ensure that execution halts whenever an expected condition is not met
- typically used to check inputs to functions, but can be used anywhere
- can be used to check outputs of a function to avoid propagating bad values
- can make it easier to locate a source of a bug.

WHERE TO USE ASSERTIONS?
- goal is to spot bugs as soon as introduced and make clear where they happened
- use as a supplement to testing
- raise exceptions if users supplies bad data input
- use assertions to
• check types of arguments or values
• check that invariants on data structures are met • check constraints on return values
• check for violations of constraints on procedure (e.g. no duplicates in a list)