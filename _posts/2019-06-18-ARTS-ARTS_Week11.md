---
layout: post
title:  "ARTS_Week11"
date:   2019-06-18 23:50:12 +0800
categories: ARTS
tags: ARTS Python Feynman
---

* content
{:toc}

# Algorithm
leetcode 232. Implement Queue using Stacks

## Description
Implement the following operations of a queue using stacks.

 - push(x) -- Push element x to the back of queue. 
 - pop() -- Removes the element from in front of queue. 
 - peek() -- Get the front element.
 - empty() -- Return whether the queue is empty.

## Example
> MyQueue queue = new MyQueue();
>
> queue.push(1);
> queue.push(2);  
> queue.peek();  // returns 1
> queue.pop();   // returns 1
> queue.empty(); // returns false

## Solution
### Approach1: Two Stacks
```java
class MyQueue {
    private int front;
    private Stack<Integer> s1;
    private Stack<Integer> s2;
    
    /** Initialize your data structure here. */
    public MyQueue() {
        s1 = new Stack<>();
        s2 = new Stack<>();
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        if (s1.isEmpty()) {
            front = x;
        }
        while (!s1.isEmpty()) {
            s2.push(s1.pop());
        }
        s2.push(x);
        while (!s2.isEmpty()) {
            s1.push(s2.pop());
        }
    }
    
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        int pop = s1.pop();
        if (!s1.isEmpty()) {
            front = s1.peek();
        }
        return pop;
    }
    
    /** Get the front element. */
    public int peek() {
        return front;
    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        return s1.isEmpty();
    }
}

```

### Complexity analysis
**push():**
Time complextiy : O(N). 
Space complexity : O(N).
**pop(), empty(), peek():**
Time complextiy : O(1). 
Space complexity : O(1).

Runtime: 45 ms, faster than 19.26%
Memory Usage: 34.3 MB, less than 45.05% 

### Approach2: Two Stacks

```java
class MyQueue {
    private int front;
    private Stack<Integer> s1;
    private Stack<Integer> s2;
    
    /** Initialize your data structure here. */
    public MyQueue() {
        s1 = new Stack<>();
        s2 = new Stack<>();
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        if (s1.isEmpty()) {
            front = x;
        }
        s1.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        if (s2.isEmpty()) {
            while (!s1.isEmpty()) {
                s2.push(s1.pop());
            }
        }
        int pop = s2.pop();
        return pop;
    }
    
    /** Get the front element. */
    public int peek() {
        if (!s2.isEmpty()) {
            return s2.peek();
        }
        return front;
    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        return s1.isEmpty() && s2.isEmpty();
    }
}
```

## Complexity Analysis
**push():**
Time complextiy : O(1). 
Space complexity : O(N).
**empty(), peek():**
Time complextiy : O(1). 
Space complexity : O(1).
**pop()**:
Time complextiy : Amortized O(1), Worst-case O(n). 
Space complexity : O(1).

Runtime: 1 ms, faster than 41.71%
Memory Usage: 36.4 MB, less than 57.64% 

### Approach3:Using Cyclic Replacements 
```java
class Solution {
    public void rotate(int[] nums, int k) {
        k = k % nums.length;
        int count = 0;
        for (int start = 0; count < nums.length; start++) {
            int current = start;
            int prev = nums[start];
            do {
                int next = (current + k) % nums.length;
                int temp = nums[next];
                nums[next] = prev;
                prev = temp;
                current = next;
                count++;
            } while (start != current);
        }
    }
}
```

### Complexity Analysis
Time complexity : O(n)O(n). Only one pass is used.
Space complexity : O(1)O(1). Constant extra space is used.

Runtime: 0 ms, faster than 100.00% 
Memory Usage: 36.3 MB, less than 58.01% 

# Review
This week the company has taught the concept of the microservice, I read this article to see how the main login function works. 
[How We Solved Authentication and Authorization in Our Microservice Architecture][1]

# Tips
I want to share some skills about docker, this is the senior developer told me.
1. use crtl + P and crtl Q to exit the docker container instead of using exit(), because it may casue the docker container down.
2. use docker exec -it [containerId] sh instead bash, beacause the docker not support bash. 

# Share
I read [Learning From the Feynman Technique][2]this week and want to share it.
> Richard Feynman (1918–1988), an author, graphic novel hero, intellectual, philosopher, physicist, and No Ordinary Genius is considered to be one of the most important physicists of all time.

The Feynman technique for teaching and communication is a mental model (a breakdown of his personal thought process) to convey information using concise thoughts and simple language. 

1. Identify the subject
Write down everything you know about the topic. Each time you run into new sources of information, add them to the note.

2. Teach it to a child
If you can teach a concept to a child, you’re way ahead of the game.

3. Identify your knowledge gaps
This is the point where the real learning happens. What are you missing? What don’t you know?

4. Organize + simplify + Tell a story
Start to tell your story. Piece together your notes and begin to spin a tale using concise explanations. Bring the most vital pieces of your knowledge about the topic together.


  [1]: https://medium.com/technology-learning/how-we-solved-authentication-and-authorization-in-our-microservice-architecture-994539d1b6e6
  [2]: https://medium.com/taking-note/learning-from-the-feynman-technique-5373014ad230