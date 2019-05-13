---
layout: post
title:  "ARTS_Week6"
date:   2019-05-13 23:50:12 +0800
categories: ARTS
tags: ARTS Python teach-by-yourself
---

* content
{:toc}

# Algorithm
leetcode35. Search Insert Position

## Description
Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You may assume no duplicates in the array.

## Example
> Input: [1,3,5,6], 5
Output: 2

> Input: [1,3,5,6], 2
Output: 1

> Input: [1,3,5,6], 7
Output: 4

> Input: [1,3,5,6], 0
Output: 0

## Solution
### Approach: binary search
The array is sorted, so it is satisfied with the condition of binary search.

```java
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
    int low = 0;
    int high = nums[1] -1;
    while (low <= high){
        int mid = low + (high-low) >> 1;
        if (nums[mid] == target){
            return mid;
        }
        if ( target > nums[mid] ) {
            low = mid+1;
        }else{
            high = mid-1;
        }
    }
    return low;
    }
}
```

### Complexity analysis
Time complextiy : O(logN). Assume that n is the length of nums.
Space complexity : O(1).

Runtime: 0 ms
Memory Usage: 39.6 MB

# Review
This week [I read a blog How I went from newbie to Software Engineer in 9 months while working full time][1], I am inspired by the author and I want to share some useful suggestion from it.

## Some Practical Advice
**Most of below advice are that I do not possess. I have to improve it.**

- It is easy to let your learning drag on and on if you don’t have any external pressure pushing you. So you need to create internal pressure.Your goal should be simple and quantitative. You should do enough research to come up with a reasonable goal. 
- make new Twitter, Reddit, Stack Overflow, Medium, and Quora accounts using my full name, so that I could build up an online presence.
- I started going to meetups early. Most people only started to attend meetups when they were looking for a job, but at that point it is almost too late. There are so many reasons to start early. To name a few:
 - Developing relationships takes a long time. Starting early means that you have connections who can vouch for you when looking for a job later
 - Talking about programming with strangers is a great way to prepare for interviews
 - You can learn new frameworks, tools, and learning resources from people who are ahead of you. This can influence your future learning plan.



## Useful Resources
 -  [learnprogramming subreddit][2]
 -  [freeCodeCamp forum][3]
 -  [Medium][4]
 -  [Meetup][5]



## Conclusion
Python is primarily slow because of its dynamic nature and versatility. It can be used as a tool for all sorts of problems, where more optimised and faster alternatives are probably available.
 
# Tips
I am learning python now, the following are some trips I have met with.
## The Multiple Assignment Trick
```python
>>> cat = ['fat', 'orange', 'loud']
>>> size = cat[0]
>>> color = cat[1]
>>> disposition = cat[2]

# you could type this line of code:
>>> cat = ['fat', 'orange', 'loud']
>>> size, color, disposition = cat

#The multiple assignment trick can also be used to swap the values in two variables:
>>> a, b = 'Alice', 'Bob'
>>> a, b = b, a
```
## sort() Method
There are three things you should note about the sort() method
 - The sort() is in place method, it means it returns None.
 - Cannot sort lists that have both number values and string values in them.
 - sort() uses “ASCIIbetical order” rather than actual alphabetical order for sorting strings.
```python
>>> spam = ['Alice', 'ants', 'Bob', 'badgers', 'Carol', 'cats']
>>> spam.sort()
>>> spam
['Alice', 'Bob', 'Carol', 'ants', 'badgers', 'cats']

#If you need to sort the values in regular alphabetical order, pass str. lower for the key keyword argument in the sort() method call.
>>> spam = ['a', 'z', 'A', 'Z']
>>> spam.sort(key=str.lower)
>>> spam
['a', 'A', 'z', 'Z']
```
## Review the Doc
There are several methods to review the python docmuents.
 - use help() function in the interrupter -- help(struct)
 - use '__ doc__' built-in function 
 - use user mannual or pydoc which installed together with python
 - visit the online [python doc][6]

## Reference and value
 - Python uses **references** whenever variables must store values of **mutable** data types, such as **lists or dictionaries**. 
 - For values of **immutable** data types such as **strings, integers, or tuples,** Python variables will store the value itself.
## copy
Python provides a module named copy that provides both the copy() and deepcopy() functions. 
 - copy.copy(list) it can copy the whole list, and the copyed list has the different reference from the origin one.
 - copy.deepcopy(list) it can copy the inner list.

## Dictionary and list
 - Unlike lists, items in dictionaries are unordered. The first item in a list named spam would be spam[0]. But there is no “first” item in a dictionary. While the order of items matters for determining whether two lists are the same, it does not matter in what order the key-value pairs are typed in a dictionary.

 - Because dictionaries are not ordered, they can’t be sliced like lists.

 - There are three dictionary methods that will return list-like values of the dictionary’s keys, values, or both keys and values: **keys(), values(), and items()**. **The values returned by these methods are not true lists: They cannot be modified and do not have an append() method.** But these data types (dict_keys, dict_values, and dict_items, respectively) can be used in for loops. 

 - Dictionaries have a get() method that takes two arguments: the key of the value to retrieve and a fallback value to return if that key does not exist.

 - Dictionaries have a setdefault() method, The first argument passed to the method is the key to check for, and the second argument is the value to set at that key if the key does not exist.The setdefault() method is a nice shortcut to ensure that a key exists. 

 - pprint module contains pprint() and pformat() functions that will “pretty print” a dictionary’s values.

## Method of String
rjust(), ljust(), center() -- they help print some tabular data.
 
# Share
[How to ask questions the samrt way][7]
We will have many questions to ask whenever we meet up a bug or need to learn new knowledge, in this case, learning how to ask questions the samrt way is quiet important. This [orignal blog][8] is wirtten by Eric S. Raymond, Rick Moen, thanks a lot.

The first thing to understand is that hackers actually like hard problems and good, thought-provoking questions about them.

I just pick some key points of the blog.

## Before Ask
- Try to find an answer by searching the archives of the forum or mailing list you plan to post to.

- Try to find an answer by searching the Web.

- Try to find an answer by reading the manual.

- Try to find an answer by reading a FAQ.

- Try to find an answer by inspection or experimentation.

- Try to find an answer by asking a skilled friend.

- If you're a programmer, try to find an answer by reading the source code.

## Be precise and informative about your problem
- Describe the symptoms of your problem or bug carefully and clearly.

- Describe the environment in which it occurs (machine, OS, application, whatever). Provide your vendor's distribution and release level (e.g.: “Fedora Core 7”, “Slackware 9.1”, etc.).

- Describe the research you did to try and understand the problem before you asked the question.

- Describe the diagnostic steps you took to try and pin down the problem yourself before you asked the question.

- Describe any possibly relevant recent changes in your computer or software configuration.

- If at all possible, provide a way to reproduce the problem in a controlled environment.


  [1]: https://medium.freecodecamp.org/how-i-went-from-newbie-to-software-engineer-in-9-months-while-working-full-time-460bd8485847
  [2]: learnprogramming%20subreddit
  [3]: https://forum.freecodecamp.org/
  [4]: https://www.medium.com/
  [5]: https://www.meetup.com/
  [6]: https://docs.python.org
  [7]: http://www.catb.org/~esr/faqs/smart-questions.html
  [8]: http://www.catb.org/~esr/faqs/smart-questions.html