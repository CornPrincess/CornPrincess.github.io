---
layout: post
title:  "ARTS_Week12"
date:   2019-06-24 23:50:12 +0800
categories: ARTS
tags: ARTS 
---

* content
{:toc}

# Algorithm
leetcode 242. Valid Anagram

## Description
Given two strings s and t , write a function to determine if t is an anagram of s.

## Example
> Input: s = "anagram", t = "nagaram"
Output: true
Input: s = "rat", t = "car"
Output: false

## Solution: Compare two array
```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }
        
        char[] str1 = s.toCharArray();
        char[] str2 = t.toCharArray();
        Arrays.sort(str1);
        Arrays.sort(str2);
        return Arrays.equals(str1, str2);
    }
}
```
### Approach1: Two Stacks

### Complexity analysis
- Time complexity : O(nlogn). Assume that nn is the length of ss, sorting costs O(nlogn) and comparing two strings costs O(n). Sorting time dominates and the overall time complexity is O(nlogn).

- Space complexity : O(1). Space depends on the sorting implementation which, usually, costs O(1) auxiliary space if heapsort is used. Note that in Java, toCharArray() makes a copy of the string so it costs O(n) extra space, but we ignore this for complexity analysis.

Runtime: 7 ms, faster than 32.76% of Java online submissions
Memory Usage: 37.3 MB, less than 76.69% of Java online submissions 

### Approach2: HashTable
```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }
        
        int[] counter = new int[26];
        for (int i = 0; i < s.length(); i++) {
            counter[s.charAt(i) - 'a']++;
            counter[t.charAt(i) - 'a']--;
        }
        
        for (int count : counter) {
            if (count != 0) {
                return false;
            }
        }
        return true;
    }
}
```

## Complexity Analysis
- Time complextiy : time complexity is O(n) because accessing the counter table is a constant time operation.
- Space complexity : Although we do use extra space, the space complexity is O(1) because the table's size stays constant no matter how large nn is.

Runtime: 4 ms, faster than 77.25% of Java online submissions for Valid Anagram.
Memory Usage: 36.3 MB, less than 81.82% of Java online submissions for Valid Anagram.

# Review
[The Future of Web Development][1]

# Tips
The notes for requests -- python library
The parameters are URL、Method、Headers、Body and so on.
 - if URL use https，we can add "verify=False" in request method to escape the security verify.
 - Body，the corresponding parameter in requests method is data。Take attention, The  data is assigned a string instead of a dict we can use json.dumps() to convert a dict to string.

# Share
This week I watched a video about [Test Driven Development with Spring Boot][2], the video is friendly to new development.


  [1]: https://hackernoon.com/the-future-of-web-development-8da936743d5c
  [2]: https://content.pivotal.io/springone-platform-2017/test-driven-development-with-spring-boot-sannidhi-jalukar-madhura-bhave