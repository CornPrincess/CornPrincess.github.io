---
layout: post
title:  "ARTS_Week8"
date:   2019-05-25 23:50:12 +0800
categories: ARTS
tags: ARTS Python openresty
---

* content
{:toc}

# Algorithm
leetcode 189. Rotate Array

## Description
Given an array, rotate the array to the right by k steps, where k is non-negative.

## Example
> Input: [1,2,3,4,5,6,7] and k = 3
Output: [5,6,7,1,2,3,4]
Explanation:
rotate 1 steps to the right: [7,1,2,3,4,5,6]
rotate 2 steps to the right: [6,7,1,2,3,4,5]
rotate 3 steps to the right: [5,6,7,1,2,3,4]

## Example
> Input: [-1,-100,3,99] and k = 2
Output: [3,99,-1,-100]
Explanation: 
rotate 1 steps to the right: [99,-1,-100,3]
rotate 2 steps to the right: [3,99,-1,-100]

## Note
> Try to come up as many solutions as you can, there are at least 3 different ways to solve this problem.
Could you do it **in-place** with O(1) extra space?

## Solution
### Approach1: Brute Force
```java
class Solution {
    public void rotate(int[] nums, int k) {
        int previous;
        int temp;
        
        for (int i = 0; i < k; i++) {
            previous = nums[nums.length - 1];
            for (int j = 0; j < nums.length; j++) {
                temp = nums[j];
                nums[j] = previous;
                previous = temp;
            }
        }
    }
}
```

### Complexity analysis
Time complextiy : O(N*k). Assume that n is the length of nums, and k is the number of roatate tiems.
Space complexity : O(1).

Runtime: 83 ms, faster than 19.76% 
Memory Usage: 37.2 MB, less than 57.19%

### Approach2: Using Extra Array 
We use an extra array in which we place every element of the array at its correct position.

```java
class Solution {
    public void rotate(int[] nums, int k) {
        int[] a = new int[nums.length];
        for (int i = 0; i < nums.length; i++) {
            a[(i + k) % nums.length] = nums[i];
        }
        
        for (int i = 0; i < nums.length; i++) {
            nums[i] = a[i];
        }
    }
}
```

## Complexity Analysis
Time complexity : O(n). One pass is used to put the numbers in the new array. And another pass to copy the new array to the original one.
Space complexity : O(n). Another array of the same size is used.

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
I am learning web development now, and I know agentzh is the creator of [openresty][1], I browsed the offical website and planed to try the tutorial next week.

# Tips
I am learning python rege now, the following are some trips I have met with.

 - match with zero and more: *
 - match with one and more: +
 - match specific repetitions with curly brackets
```python
>>> greedyHaRegex = re.compile(r'(Ha){3,5}')
>>> mo1 = greedyHaRegex.search('HaHaHaHaHa')
>>> mo1.group()
'HaHaHaHaHa'

>>> nongreedyHaRegex = re.compile(r'(Ha){3,5}?')
>>> mo2 = nongreedyHaRegex.search('HaHaHaHaHa')
>>> mo2.group()
'HaHaHa'
```
Note that the question mark can have two meanings in regular expressions: declaring a nongreedy match or flagging an optional group. These meanings are entirely unrelated.

 - findAll() method
```python
>>> phoneNumRegex = re.compile(r'\d\d\d-\d\d\d-\d\d\d\d') # has no groups
>>> phoneNumRegex.findall('Cell: 415-555-9999 Work: 212-555-0000')
['415-555-9999', '212-555-0000']

>>> phoneNumRegex = re.compile(r'(\d\d\d)-(\d\d\d)-(\d\d\d\d)') # has groups
>>> phoneNumRegex.findall('Cell: 415-555-9999 Work: 212-555-0000')
[('415', '555', '9999'), ('212', '555', '0000')]
```

 - To summarize what the findall() method returns, remember the following:

  - When called on a regex with no groups, such as \d\d\d-\d\d\d-\d\d\d\d, the method findall() returns a list of string matches, such as ['415-555-9999', '212-555-0000'].

  - When called on a regex that has groups, such as (\d\d\d)-(\d\d\d)-(\d\ d\d\d), the method findall() returns a list of tuples of strings (one string for each group), such as [('415', '555', '9999'), ('212', '555', '0000')].



# Share
I am always looking forward to contributing to the open source, but I just don't know how to start it. I read an article about [how to cntribute to][2], and I will share some key points.

> If you’ve never contributed to open source before, take some advice from U.S. President John F. Kennedy, who once said, “Ask not what your country can do for you - ask what you can do for your country.”

> Contributing to open source happens at all levels, across projects. You don’t need to overthink what exactly your first contribution will be, or how it will look.

> Instead, start by thinking about the projects you already use, or want to use. The projects you’ll actively contribute to are the ones you find yourself coming back to.

> Within those projects, whenever you catch yourself thinking that something could be better or different, act on your instinct.

We can find open source projects via these websites.
[GitHub Explore][3]
[Open Source Friday][4]
[First Timers Only][5]
[CodeTriage][6]
[24 Pull Requests][7]
[Up For Grabs][8]
[Contributor-ninja][9]
[First Contributions][10]


  [1]: https://openresty.org/en/getting-started.html
  [2]: https://opensource.guide/how-to-contribute/
  [3]: https://github.com/explore/
  [4]: https://opensourcefriday.com/
  [5]: https://www.firsttimersonly.com/
  [6]: https://www.codetriagew.firsttimersonly.com/
  [7]: https://24pullrequests.com/
  [8]: https://up-for-grabs.net/
  [9]: https://contributor.ninja/
  [10]: https://firstcontributions.github.io/