---
layout: post
title:  "Introduction_to_Computation-MIT6_0001F16_Lec3"
date:   2019-03-08 23:50:12 +0800
categories: Introduction_to_Computation
tags: Python 
---

* content
{:toc}

第三节课，Ana讲得很好，继续边学英语边学python

## STRINGS
- quare brackets used to perform indexing into a string to get the value at a certain index/position
```python
s = "abc"
index: 0 1 2  #indexing always starts at 0 
index: -3 -2 -1  #last element always at index -1
```
- can slice strings using [start:stop:step]
- strings are “immutable” – cannot be modified
```python
s = "hello"
s[0] = 'y'  #gives an error 
s = 'y'+s[1:len(s)] #is allowed,s bound to new object
```

## GUESS-AND-CHECK
the process below also called exhaustive enumeration

 - given a problem...
 - you are able to guess a value for solution
 - you are able to check if the solution is correct
 - keep guessing until find solution or guessed all values
```python
cube = 8
for guess in range(abs(cube)+1):
    if guess**3 >= abs(cube):
        break
if guess**3 != abs(cube):
    print(cube, 'is not a perfect cube')
else:
    if cube < 0:
        guess = -guess
    print('Cube root of '+str(cube)+' is '+str(guess))
```

## APPROXIMATE SOLUTIONS
- good enough solution
- start with a guess and increment by some small value
- keep guessing  if |guess3-cube| >= epsilon for some small epsilon
- decreasing increment (size slower program) 
- increasing epsilon (less accurate answer)
```python
cube = 27
epsilon = 0.01
guess = 0.0
increment = 0.0001
num_guesses = 0
while abs(guess**3 - cube) >= epsilon: and guess <= cube :
    guess += increment
    num_guesses += 1
print('num_guesses =', num_guesses)
if abs(guess**3 - cube) >= epsilon:
    print('Failed on cube root of', cube)
else:
print(guess, 'is close to the cube root of', cube)
```

## BISECTION SEARCH
```python
cube = 27
epsilon = 0.01
num_guesses = 0
low = 0
high = cube
guess = (high + low)/2.0
while abs(guess**3 - cube) >= epsilon:
    if guess**3 < cube :
        low = guess
    else:
        high = guess
    guess = (high + low)/2.0
    num_guesses += 1
print 'num_guesses =', num_guesses
print guess, 'is close to the cube root of', cube
```

- guess converges on the order of log2N steps 
- bisection search works when value of function varies
- monotonically with input
- code as shown only works for positive cubes > 1 – why?
- challenges modify to work with negative cubes! 
- modify to work with x < 1!

## TODO
finish the challenge