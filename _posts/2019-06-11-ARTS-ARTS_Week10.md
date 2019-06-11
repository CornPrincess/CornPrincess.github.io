---
layout: post
title:  "ARTS_Week10"
date:   2019-06-11 23:50:12 +0800
categories: ARTS
tags: ARTS Python 
---

* content
{:toc}

# Algorithm
leetcode 234. Palindrome Linked List

## Description
Given a singly linked list, determine if it is a palindrome.

## Example 
> Input: 1->2
Output: false

> Input: 1->2->2->1 
Output: true

## Solution
### Approach1: Brute Force
```java
class Solution {
    public boolean isPalindrome(ListNode head) {
        ListNode fast = head;
        ListNode slow = head;
        
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
        }
        
        if (fast != null) {
            slow = slow.next;
        }
        
        slow = reverse(slow);
        fast = head;
        
        while (slow != null) {
            if (fast.val != slow.val) {
                return false;   
            }
            fast = fast.next;
            slow = slow.next;
        }
        return true;
    }

    public ListNode reverse(ListNode head) {
        ListNode prev = null;
        while (head != null) {
            ListNode next = head.next;
            head.next = prev;
            prev = head;
            head = next;
        }
        return prev;
    }
}
```

### Complexity analysis
Time complextiy : O(N). Assume that n is the length of LinkedList.
Space complexity : O(1).

Runtime: 1 ms, faster than 97.34% % 
Memory Usage: 40.9 MB, less than 91.63%

# Review
I am learning web development now, and I know agentzh is the creator of [openresty][1], I browsed the offical website and planed to try the tutorial next week.

# Tips
When we use the computer in company, it is a good custom to configure the  mirror and proxy to download the dependencies quickly.

# Share
This week I read an article about the basic architecture concepts that is valuable for  knew web developer beginner.

The article is [Web Architecture 101][2]


  [1]: https://openresty.org/en/getting-started.html
  [2]: https://engineering.videoblocks.com/web-architecture-101-a3224e126947