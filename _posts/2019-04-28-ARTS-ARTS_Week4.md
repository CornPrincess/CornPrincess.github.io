---
layout: post
title:  "ARTS_Week4"
date:   2019-04-28 23:50:12 +0800
categories: ARTS
tags: ARTS Array Two-points
---

* content
{:toc}

From week4, I will write this kind if  blog in English, hope everyone enjoy it and we can discuss with everything.

# Algorithm
leetcode27:Remove Element

## Description
Given an array nums and a value val, remove all instances of that value **in-place** and return the new length.

Do not allocate extra space for another array, you must do this by modifying the input array in-place with O(1) extra memory.

The order of elements can be changed. It doesn't matter what you leave beyond the new length.

## Example

> Given nums = [3,2,2,3], val = 3,
Your function should return length = 2, with the first two elements of nums being 2.
It doesn't matter what you leave beyond the returned length.

> Given nums = [0,1,2,2,3,0,4,2], val = 2,
Your function should return length = 5, with the first five elements of nums containing 0, 1, 3, 0, and 4.
Note that the order of those five elements can be arbitrary.
It doesn't matter what values are set beyond the returned length.

## Solution
Approach: Two points
If we got the key concept of two points, we can solve this problem very quickly, this is very same as the leetcode26.

In this case, we have to solve the problem in place, it means we can not use another array, beacuse it is impossible to remove en element from the array without making a copy of the array, so we can use two points.
One is slow point i, it's duty is to record the elemnet that we want.
The other is fast point j, it's duty is to scan all the element in the array.

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        if(nums == null || nums.length == 0) return 0;
        
        int i = 0;
        for(int j = 0; j < nums.length; j++) {
            if ( nums[j] != val ) {
                nums[i] = nums[j];
                i++;
            }
        }
        return i;
    }
}
```

## Complexity analysis

Time complexity : O(n). Assume the array has a total of n elements, both i and j traverse at most 2n steps.

Space complexity : O(1). 

## Another way:
Now consider case where the array contains less elements to remove, we will do many unnecessary copy operation.

So we can use while loop, because it is an unsure time loop, if nums[i] == val, we can swap the current element with the last one, and then dispose the last element, which means the lenght, we can just use one point.
```java
class Solution {
    public int removeElement(int[] nums, int val) {
        if(nums == null || nums.length == 0) return 0;
        
        int n = nums.length;
        int i = 0;
        while(i < n) {
            if(nums[i] == val) {
                nums[i] = nums[n-1];
                n--;
            } else {
                i++;
            }
        }
        return n;
    }
}
```
Time complexity : O(n). Assume the array has a total of n elements, i will traverse at most n steps.

Space complexity : O(1). 

# Review
I have read and look through several materials these evening,  just to find a topic and learn it to write the review. Up to now, I will do a reivew on the topic of how to work in Google. These are the main reference for the review.

 -  [The answer in Zhihu][1]
 -  [The answer in Quaro][2]
 -  [The github project][3]
  
Google is a great company, I think it's most people's dream company, I will share some my own opinion about how to get the google offer.

First of all, we have to be confident about ourselves, we can not be afraid of it in our mind and think is just a tall mountain that we can not conquer.

Ok, let us talk about some concrete methods and steps.

First, we have to find the right position, we can find the information in linkedin and google career, for me, my position is software engineer.

Second, we have to create a list of skills, we can find the "minimum qualifications" and "preferred qualification", with that, we can see our strenghts and shorts, and the most important and difficult task is to make a proper learning plan and stick to it! I am planning to write a blog to share how to make a plan and execuate it efficiently.

Thirdly, network with the current google employees. This is important, however, few people have the connection with googler, it looks like a little bit hard. But I always believe that where there is a will, there is a way.

For example, we can linkedin and hicounselor, both are the efficient applications to help people build a connection with the googler. Also, it is a good way to apply with employee referral, there are more than 75% hiring are done through internal referral.

Finally, strategy to ace the interview, there are three most important things to be done:

 - You have thoroughly studied basic and advanced concepts of skills identified in step 2
 - You have identified key insights of job position from company insiders
 - You have built good relationship with current employees of Google and Facebook so that they can endorse you. **THIS IS THE MOST IMPORTANT**

Trust yourself and take interview with full confidence, show your passion and zeal to learn during the interview. If you can convince the interviewer that you are willing to learn and improve your skills with time, then you will definitely be the winner.

# Tip
This week I spent most time to find some tools to make my work and study efficiently, this time, I want to share an application named trello, it has mobile application and online website, we can use it make todo list and make some card.

Next week, I just want to share some useful code skills, because this section is responsible for the code skill, I think.

# Share
I am happy to share my CS knowledge and work interview learning plan, I will share the online resource and my daily study plan.

I am always confused about how to find the best and most suitable learning reosurce for myself, honestly, there are huge resource in the Internet, and I am very appreaciative for the inventor of world wide website -- [Tim Berners-Lee][4], I have upload the video about him to the [BiliBili][5] and other pioneers of the CS industry, thanks for the contribution for the whole human. However, until now, I just have not made a practical plan to execuate it step by step.

So, I create a [github project][6] to record my cs notes and somoe resources which I thought vaulable, and I will record my study plan on it, hope you can join us and work together.


  [1]: https://www.zhihu.com/collection/327311430
  [2]: https://www.quora.com/How-can-someone-work-for-Google
  [3]: https://github.com/jwasham/coding-interview-university
  [4]: https://en.wikipedia.org/wiki/Tim_Berners-Lee
  [5]: https://www.bilibili.com/video/av50376002
  [6]: https://github.com/CornPrincess/Coding-your-life