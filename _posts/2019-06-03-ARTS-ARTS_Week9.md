---
layout: post
title:  "ARTS_Week9"
date:   2019-06-03 23:50:12 +0800
categories: ARTS
tags: ARTS Python 
---

* content
{:toc}

# Algorithm
leetcode 53. Maximum Subarray

## Description
Given an integer array nums, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

## Example 
> Input: [-2,1,-3,4,-1,2,1,-5,4],
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.

## Solution
### Approach1: Brute Force
```java
class Solution {
    public int maxSubArray(int[] nums) {
        int result = 0;
        int max = Integer.MIN_VALUE;
        for (int i = 0; i < nums.length; i++) {
            result += nums[i];
            
            if (result > max) {
                max = result;
            }
            
            if (result < 0) {
                result = 0;
            }
        }
        return max;
    }
}
```

### Complexity analysis
Time complextiy : O(N). Assume that n is the length of nums.
Space complexity : O(1).

Runtime: 1 ms, faster than 94.43% 
Memory Usage: 38.7 MB, less than 45.76%

# Review
I am learning web development now, and I know agentzh is the creator of [openresty][1], I browsed the offical website and planed to try the tutorial next week.

# Tips
This week is a little busy, I just recommend a note app: [EverNote][2]

# Share
This week I will share some skills during interview, I think they are practial skills beacuse everyone wants to achieve dream offer.

## Always think ahead
Often, you’ll hear an interviewer ask this: “OK, I think that looks good. Now, how would you improve this code?” **This is a killer question.** Being able to come up with a working solution is the bare minimum to be considered for the position. These questions are basic competency tests. What separates the good candidates from great is the ability to think beyond what’s required.

Think about edge cases, scaling issues, problem areas. Always think one step ahead. If you’re using a recursive approach, what would happen if you have a large data set? If you’re using a hashing algorithm, how do you handle collisions? How likely is that to happen, and what’s the worst case scenario?

## There’s more than one answer
From an interviewer perspective, I’m not looking to check if you know what the right answer is. Yes, it is important that you can write a working solution, but it’s not the only thing.

More than that, I’m looking for how smart this person is, how the solution is derived, and what other creative solutions this person might be thinking about.

## OOP is not dead
Many candidates jump head-first into a programming problem without putting further thought into simplifying the code. I used to be in this bunch and, admittedly, I still do it sometimes.

## Craft your resume
If you’re starting out on your job search, then the very first thing you should do is prepare a great resume. That’s the number one thing that is often overlooked by job seekers and, arguably, is the lowest hanging fruit.

## Communicate early and communicate often
Communicate as often as you need. I tried to communicate early and often. Whenever there was an issue, I would raise it with the interviewer and let them know. It helped me determine if I was heading in the right direction, and course-correct if I was not.

## Reference
[5 things you need to know in a programming interview][3]


[1]: https://openresty.org/en/getting-started.html
[2]: https://www.yinxiang.com/?referer=en
[3]: https://medium.com/free-code-camp/the-most-important-things-you-need-to-know-for-a-programming-interview-3429ac2454b