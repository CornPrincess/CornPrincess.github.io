---
layout: post
title:  "ARTS_Week13"
date:   2019-07-03 23:50:12 +0800
categories: ARTS
tags: ARTS Bash 
---

* content
{:toc}

# Algorithm
leetcode 344. Reverse String

## Description
Write a function that reverses a string. The input string is given as an array of characters char[].

Do not allocate extra space for another array, you must do this by modifying the input array in-place with O(1) extra memory.

You may assume all the characters consist of printable ascii characters.

## Example
> Input: ["h","e","l","l","o"]
Output: ["o","l","l","e","h"]

> Input: ["H","a","n","n","a","h"]
Output: ["h","a","n","n","a","H"]

## Solution
### Approach1: 
```java
class Solution {
    public void reverseString(char[] s) {
        if (s == null || s.length == 0) {
            return;
        }
        
        for (int i = 0; i < s.length / 2; i++) {
            char temp = s[i];
            s[i] = s[s.length - i - 1];
            s[s.length - i - 1] = temp;
        }
    }
    
}
```
### Complexity analysis
- Time complextiy : O(n). 
- Space complexity : O(1).

Runtime: 1 ms, faster than 100.00% of Java online submissions for Reverse String.
Memory Usage: 47 MB, less than 90.49% of Java online submissions for Reverse String.

# Review
[Facebook’s Libra Masterplan][1]

# Tips
[Bash Basic][2]

# Share
[Follow these steps to solve any Dynamic Programming interview problem][3]


  [1]: https://onezero.medium.com/the-libra-masterplan-dc9560e41c87
  [2]: https://cornprincess.github.io/2019/07/03/Bash-Chapter-1.-bash-Basics/
  [3]: https://medium.com/free-code-camp/follow-these-steps-to-solve-any-dynamic-programming-interview-problem-cc98e508cd0e