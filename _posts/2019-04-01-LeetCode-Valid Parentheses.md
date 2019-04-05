---
layout: post
title:  "LeetCode-Valid Parentheses"
date:   2019-04-01 09:00:12 +0800
categories: LeetCode
tags: easy
---

* content
{:toc}

[original problem link][1]
#Description
Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

An input string is valid if:
- Open brackets must be closed by the same type of brackets.
- Open brackets must be closed in the correct order.

Note that an empty string is also considered valid.

**Example 1:**
> Input: "()"
Output: true

**Example 2:**
> Input: "()[]{}"
Output: true

**Example 3:**
> Input: "(]"
Output: false

**Example 4:**
> Input: "([)]"
Output: false

**Example 5:**
> Input: "{[]}"
Output: true

[original solution][2]
#Solution
**Intuition**
首先思考只有一种括号的情况，如(()()()()()),这种情况只要用计数器counter从左到右记录（的数量，遇见（就+1，遇见）就-1，遍历完成之后counter=0，那么输入的String就是valid。
Approach 1: Stacks
当表达式中有多种括号时，可以用stack，从左到右遍历是如果是左括号就入栈，如果遍历到了右括号，就把它和栈顶的左括号进行类型比较，如果类型相同，就pop栈顶的左括号，如果类型不同，那么String就是invalid。

```java
class Solution {
    public boolean isValid(String s) {
        Stack<Character> parenthese = new Stack<>();
        for(int i=0; i<s.length(); i++){
            if(s.charAt(i) == '(' || s.charAt(i) == '[' || s.charAt(i) == '{') {
                parenthese.push(s.charAt(i));
            } else {
                if(parenthese.isEmpty()) return false;
                
                if((s.charAt(i) == ')' && parenthese.peek().charValue() == '(') || (s.charAt(i) == ']' && parenthese.peek().charValue() == '[') || (s.charAt(i) == '}' && parenthese.peek().charValue() == '{')) {
                parenthese.pop();
                } else {
                    return false;
                }
            }
        }
        return parenthese.isEmpty();
    }
}
```
**Complexity**
Runtime: 2 ms, faster than 97.27% of Java online submissions for Valid Parentheses. O(n)
Memory Usage: 35.4 MB, less than 37.23% of Java online submissions for Valid Parentheses. O(n)

另一种代码实现：
```java
class Solution {
    private HashMap<Character, Character> map;
        
    public Solution() {
        this.map = new HashMap<Character, Character>();
        this.map.put(')', '(');
        this.map.put(']', '[');
        this.map.put('}', '{');
    }
        
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
            
        for(int i=0; i<s.length(); i++) {
            char c = s.charAt(i);
                
            if(this.map.containsKey(c)) {
                char topElement = stack.empty() ? '#' : stack.pop();
                if(topElement != map.get(c)) return false;
            } else {
                stack.push(c);
            }
        }
            
        return stack.isEmpty();
    }
}
```
**Complexity**
Runtime: 2 ms, faster than 97.27% of Java online submissions for Valid Parentheses. O(n)
Memory Usage: 35.6 MB, less than 36.97% of Java online submissions for Valid Parentheses.O(n)

```java
class Solution {
    public boolean isValid(String s) {
	Stack<Character> stack = new Stack<Character>();
        for (char c : s.toCharArray()) {
            if (c == '(')
                stack.push(')');
            else if (c == '{')
                stack.push('}');
            else if (c == '[')
                stack.push(']');
            else if (stack.isEmpty() || stack.pop() != c)
                return false;
        }
        return stack.isEmpty();
   }
}
```
**Complexity**
Runtime: 1 ms, faster than 99.89% of Java online submissions for Valid Parentheses. O(n)
Memory Usage: 35.6 MB, less than 36.92% of Java online submissions for Valid Parentheses. O(n)


  [1]: https://leetcode.com/problems/valid-parentheses/
  [2]: https://leetcode.com/problems/valid-parentheses/solution/