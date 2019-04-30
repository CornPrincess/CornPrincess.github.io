---
layout: post
title:  "ARTS_Week3"
date:   2019-04-22 23:50:12 +0800
categories: ARTS
tags: ARTS Array Recursion Dictionary
---

* content
{:toc}

# Algorithm
leetcode26:Remove Duplicates from Sorted Array

## Description
Given a sorted array nums, remove the duplicates in-place such that each element appear only once and return the new length.

Do not allocate extra space for another array, you must do this by modifying the input array in-place with O(1) extra memory.

## Example

> Given nums = [1,1,2],
Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively.
It doesn't matter what you leave beyond the returned length. 

> Given nums = [0,0,1,1,1,2,2,3,3,4], 
Your function should return length = 5, with the first five elements of nums being modified to 0, 1, 2, 3, and 4 respectively. It doesn't matter what values are set beyond
> the returned length.

## Solution
Approach: Two points
首先我们得到的数组是有序的，这是前提，此时我们就可以使用双指针的方法。
开始的时候让满指针i指向0位置，慢指针j指向1位置，比较nums[i]和nums[j]。
1.如果两者相等，那么j++，i保持原来位置，这样可以过滤掉相同的内容。
2.如果两者不等，那么i++，然后nums[i]=nums[j]，然后j++，开始进行上一步操作。

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if (nums.length == 0) return 0;
        
        int i = 0;
        int j;
        for(j = 1; j < nums.length; j++) {
            if (nums[j] != nums[i]) {
                i++;
                nums[i]  = nums[j];
            }
        }
        return i+1;
    }
}
```

## Complexity analysis

Time complextiy : O(n). Assume that n is the length of array. Each of ii and jj traverses at most nn steps.

Space complexity : O(1).

Runtime: 1 ms, faster than 99.98% of Java online submissions for Remove Duplicates from Sorted Array.
Memory Usage: 40.9 MB, less than 83.62% of Java online submissions for Remove Duplicates from Sorted Array.

# Review
这周要分享的还是[The key to accelerating your coding skills][1]，这篇博文也是之前陈皓叔在他博客中提到过的，现在分享一下我的感想。

文中提出了inflection point这一个观点，指出在编程学习过程中，程序员会遇到inflection point这么一个时期，在这个时期会感觉编程时所有的感觉都发生了变化，会感到自己处在黑暗中，很多技术都没学通，这一段时间会比较难熬，但是当熬过这段时间就会进入另一个境界，会得到一定的提升。
![inflection point][2]

文中具体介绍了学习编程各个时期。
1.The Tutorial Phase (3-8 weeks of serious coding）
这一时期主要是学习编程的一些基础知识，如何写一些简单的程序，在这个阶段最重要的是要注意编程过程中的细节，并且学会debug，学会如何利用google解决基本的问题。

2.The Inflection Point (2-4 weeks with the right mentality)
在这一时期可能编程的速度会比平时慢很多，也会对自己产生自我怀疑，但是坚持下去，过了这段时间就会提高自己的编程水平，并且可以增加自己的自信心。

根据我的理解，度过了第二个时期，可以成为初级程序员，文中作者也指出了如果是web开发者，那么度过了inflection point，应该具备一下的基本素质：
The web development inflection point：可以自己独立开发一整套web系统，包括搭建后台，设计数据库，进行数据库和前后台的连接，并可以添加第三方库。
The algorithm and data structures inflection point：
可以写出基本的排序算法，可以逆转列表，可以允许栈，堆，二叉树等数据机构进行编程，可以用递归 算法进行方案的实施。

# Tips
本周分享一个学习python时比较实用的[可视化网站][3]
这里以Fibonacci的递归算法进行演示，代码如下。

```python
def fib(x):
    """assumes x an int >= 0
       returns Fibonacci of x"""
    if x == 0 or x == 1:
        return 1
    else:
        return fib(x-1) + fib(x-2)
    
fib(4)
```
网站截图如下
![Fibonacci][4]

可以看到这个网站可以显示在递归的调用过程中栈和变量的变化，对于初学者来说还是很友好的，界面简洁易懂，而且还可以和这个网站在线的世界各地的程序员相互交流，相信这对于激发学习兴趣也是很有裨益的。

# Share
这里分享一下自己关于递归的理解，相信学习递归的过程中大家对于递归算法会有一些理解上的困难，我自己在学的时候经常陷入了想要追踪整个递归过程的牛角尖，现在看来这对初学递归算法是非常不可取的，学习递归重要的是要先整体全局上理解其思想，然后才是去看一些细节。

具体内容首发在博客上，[阅读原文][5]


  [1]: http://blog.thefirehoseproject.com/posts/learn-to-code-and-be-self-reliant/
  [2]: http://static.zybuluo.com/xiaocorn/qpktfa3p12n55cl66ro6bl2a/image.png
  [3]: http://pythontutor.com/visualize.html#mode=display
  [4]: http://static.zybuluo.com/xiaocorn/krrrgzy2yd4zay4n6jpkxv6z/image.png
  [5]: https://cornprincess.github.io/2019/03/28/Introduction_to_Computation-MIT6_0001F16_Lec6/