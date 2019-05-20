---
layout: post
title:  "ARTS_Week7"
date:   2019-05-20 23:50:12 +0800
categories: ARTS
tags: ARTS Python teach-by-yourself
---

* content
{:toc}

# Algorithm
leetcode 119. Pascal's Triangle II

## Description
Given a non-negative index k where k ≤ 33, return the kth index row of the Pascal's triangle.
Note that the row index starts from 0.

In Pascal's triangle, each number is the sum of the two numbers directly above it.

## Example
> Input: 3
Output: [1,3,3,1]

## Solution
### Approach:

```java
class Solution {
    public List<Integer> getRow(int rowIndex) {
        List<Integer> list = new ArrayList<>(rowIndex + 1);
        
        if (rowIndex < 0) return list;
        
        for (int i = 0; i <= rowIndex; i++) {
            list.add(1);
        }
        
        for (int i = 2; i <= rowIndex; i++) {
            for (int j = rowIndex - i + 1; j < rowIndex; j++) {
                list.set(j, list.get(j) + list.get(j+1));
            }
        }
        return list;
    }
}
```

### Complexity analysis
Time complextiy : O(N^2). Assume that n is the length of nums.
Space complexity : O(n).

Runtime:  1 ms, faster than 84.92% 
Memory Usage: 32.6 MB

# Review
This week, I will share a blog of [How I landed offers from Microsoft, Amazon, and Twitter without an Ivy League degree][1]

Yes, this blog is so inspiring, the author did not graduate from an Ivy league school. He went to a community college in Idaho for two years, and then finished the CS degree in a small Catholic university. but we can see that the **author is a skilled software engineer and so confident, these two points are so importamnt, and it is the foundation of all success.**

There are some other components that help him make his own success.

 - As the saying goes, cast your net far and wide. 
 - Try to be the “sponge” in the group
 - Break interview down into three categories: data structures, algorithms, and system design.
 - Nail your pitch. Do your research, use your background, and show the company why both of you would fit well together

Remember, Remember: the strong survives, the tough thrives.
 
# Tips
I am learning python now, the following are some trips I have met with.

## Escape Character
Remember that escape characters in Python use the backslash (\). The string value '\n' represents a single newline character, not a backslash followed by a lowercase n. You need to enter the escape character \\ to print a single backslash. So '\\n' is the string that represents a backslash followed by a lowercase n. However, by putting an r before the first quote of the string value, you can mark the string as a raw string, which does not escape characters.

Since regular expressions frequently use backslashes in them, it is convenient to pass raw strings to the re.compile() function instead of typing extra backslashes. Typing r'\d\d\d-\d\d\d-\d\d\d\d' is much easier than typing '\\d\\d\\d-\\d\\d\\d-\\d\\d\\d\\d'.

## Matching Multiple Groups with the Pipe
The | character is called a pipe. When both Batman and Tina Fey occur in the searched string, the first occurrence of matching text will be returned as the Match object.
```python
>>> heroRegex = re.compile (r'Batman|Tina Fey')
>>> mo1 = heroRegex.search('Batman and Tina Fey.')
>>> mo1.group()
'Batman'

>>> mo2 = heroRegex.search('Tina Fey and Batman.')
>>> mo2.group()
'Tina Fey'
```

# Share
Up to now, I release that I have to improve my English, including writing, speaking, listening, reading. I have searched so many resources in the Internet, finally, I found an awesome answer about [how to learn English][2].

There are three parts of the answer. First, why it is difficulty to learning English Grammar. English Grammar is the rules about how words change their forms and combine with other words to make a sentence. For most Chinese student, they spend several years to learn English grammar, but this is just waste time and worthless. Because we just accumulate and regurgitate fixed knowledge. 

Secondly, how to learn English grammar? 
 - Use the full time to learn English 
 - Learn English systematically
 - Deliberate practice

Thirdly, learning resource.
 - English Grammar in Use
 - The Process of Composition
 - On Writing Well
 - Elements of Style


  [1]: https://medium.freecodecamp.org/how-i-landed-offers-from-microsoft-amazon-and-twitter-without-an-ivy-league-degree-d62cfe286eb8
  [2]: https://www.zhihu.com/question/264195321