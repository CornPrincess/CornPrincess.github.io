---
layout: post
title:  "ARTS_Week5"
date:   2019-05-05 23:50:12 +0800
categories: ARTS
tags: ARTS substring KMP python
---

* content
{:toc}

# Algorithm
leetcode28:Implement strStr()

## Description
Implement strStr()

Return the index of the first occurrence of needle in haystack, or -1 if needle is not part of haystack.

## Example

> Input: haystack = "hello", needle = "ll"
Output: 2

> Input: haystack = "aaaaa", needle = "bba"
Output: -1

## Clarification
What should we return when needle is an empty string? This is a great question to ask during an interview.

For the purpose of this problem, we will return 0 when needle is an empty string. This is consistent to C's strstr() and Java's indexOf().

## Solution
### Approach1: brute force
We can set two points, one points to the start of haystack, the other points ot the start of needle, and use two loops to iterate the whole cases.

```java
class Solution {
    public int strStr(String haystack, String needle) {
        if(needle.length() == 0) return 0;
        
        int i = 0;
        int j = 0;
        while(i < haystack.length() && j < needle.length()) {
            if(haystack.charAt(i) == needle.charAt(j)) {
                i++;
                j++;
            } else {
                i = i - j + 1;
                j = 0;
            }
        }
        
        if(j == needle.length()) {
            return i - j;
        } else {
            return -1;
        }
    }
}
```
Runtime: 4 ms, faster than 47.77% of Java online submissions for Implement strStr().
Memory Usage: 38.8 MB, less than 7.14% of Java online submissions for Implement strStr().

```java
class Solution {
    public int strStr(String haystack, String needle) {
        if(needle.length() == 0) return 0;
        
        int n = haystack.length();
        int m = needle.length();
        
        for(int i = 0; i <= n - m; i++) {
            int j = 0;
            for(; j < m; j++) {
                if(needle.charAt(j) != haystack.charAt(i + j)) {
                    break;
                }
            }
            if(j == m) return i;
        }
        
        return -1;
    }
}
```
Runtime: 1 ms, faster than 88.44% of Java online submissions for Implement strStr().
Memory Usage: 37.4 MB, less than 81.73% of Java online submissions for Implement strStr().

### Complexity analysis
Time complextiy : O((n-m+1)*m). Assume that n is the length of haystack, and m is the length of needle.

Space complexity : O(1).

### Approach2: KMP Algorithm
KMP is a classic algorithm, there are many useful resources in the Internet, I will list below.
[KMP Substring Searching][1]
[KMP by Python][2]

```java
class Solution {
    public int strStr(String haystack, String needle) {
        if(needle.length() == 0) return 0;
        
        int n = haystack.length();
        int m = needle.length();
        int i = 0;
        int j = 0;
        int[] lps = new int[m];
        computeLPSArray(needle, lps);
        
        while(i < n && j < m ) {
            if(needle.charAt(j) == haystack.charAt(i)) {
                i++;
                j++;
            } else {
                if(j != 0) {
                    j = lps[j-1];
                } else {
                    i++;
                }
            }
        }
        
        if(j == m) {
            return i - j;
        } else {
            return -1;
        }
    
    }
    
    public void computeLPSArray(String needle, int[] lps) {
        int len = 0;
        int i = 1;
        lps[0] = 0;
        int m = needle.length();
        
        while(i < m) {
            if(needle.charAt(i) == needle.charAt(len)) {
                lps[i++] = ++len;
            } else {
                if(len != 0) {
                    len = lps[len - 1];
                } else {
                    lps[i] = 0;
                    i++;
                }
            }
        }
    }
}
```
### Complexity analysis
Time complextiy : O(n + m). Assume that n is the length of haystack, and m is the length of needle.

Space complexity : O(m).

Runtime: 3 ms, faster than 68.76% of Java online submissions for Implement strStr().
Memory Usage: 37.3 MB, less than 86.86% of Java online submissions for Implement strStr().

# Review
This week, I read the Netfix technology blog on the medium, named [Why is Python so slow?][3]

The author gives three top theories

 - It’s the GIL (Global Interpreter Lock)
 - It’s because its interpreted and not compiled
 - It’s because its a dynamically typed language

## It’s the GIL
In multi-thread programming, Unlike a single-threaded process, you need to ensure that when changing variables in memory, multiple threads don’t try and access/change the same memory address at the same time.

When CPython creates variables, it allocates the memory and then counts how many references to that variable exist,If the number of references is 0, then it frees that piece of memory from the system. This is why creating a “temporary” variable within say, the scope of a for loop, doesn’t blow up the memory consumption of your application.

The challenge then becomes when variables are shared within multiple threads, how CPython locks the reference count. There is a “global interpreter lock” that carefully controls thread execution. **The interpreter can only execute one operation at a time, regardless of how many threads it has.**

If you have a web-application (e.g. Django) and you’re using WSGI, then each request to your web-app is a separate Python interpreter, so there is only 1 lock per request. 

## It’s because its an interpreted language
If at a terminal you wrote python myscript.py then CPython would start a long sequence of reading, lexing, parsing, compiling, interpreting and executing that code.

So, why is Python so much slower than both Java and C# in the benchmarks if they all use a virtual machine and some sort of Bytecode? Firstly, .NET and Java are JIT-Compiled.

## It’s because its a dynamically typed language
In a “Statically-Typed” language, you have to specify the type of a variable when it is declared. Those would include C, C++, Java, C#, Go.

In a dynamically-typed language, there are still the concept of types, but the type of a variable is dynamic.

## Conclusion
Python is primarily slow because of its dynamic nature and versatility. It can be used as a tool for all sorts of problems, where more optimised and faster alternatives are probably available.
 
# Tips
This week, I will share a chrome extension named [word discoverer][4], it has following three main functions:

 - highlight the uncommon words and expressions
 - translate the word you choose on the website, and you can define the source translate website. 
 - save the vocabulary you added

Enjoy your time.
 
# Share
This week I will share the concept of the local and global variable by python. 

In brief, Parameters and variables that are assigned in a called function are said to exist in that function’s local scope. Variables that are assigned outside all functions are said to exist in the global scope.

Scopes matter for several reasons:

 - Code in the global scope cannot use any local variables.
 - However, a local scope can access global variables.
 - Code in a function’s local scope cannot use variables in any other local scope.
 - You can use the same name for different variables if they are in different scopes. That is, there can be a local variable named spam and a global variable also named spam.

I will use the classic examples in the book to explain them.

## Local Variables Cannot Be Used in the Global Scope
```python
def spam():
    eggs = 31337
spam()
print(eggs) # NameError: name 'eggs' is not defined
```
This makes sense if you think about it; when the program execution is in the global scope, no local scopes exist, so there can’t be any local variables. This is why only global variables can be used in the global scope.

## Local Scopes Cannot Use Variables in Other Local Scopes
```python
def spam():
    eggs = 99
    bacon()
    print(eggs)

def bacon():
    ham = 101
    eggs = 0

pam()
```
## Global Variables Can Be Read from a Local Scope
```python
def spam():
    print(eggs)
eggs = 42
spam()
print(eggs)
```
Since there is no parameter named eggs or any code that assigns eggs a value in the spam() function, when eggs is used in spam(), Python considers it a reference to the global variable eggs. 

## Local and Global Variables with the Same Name
```python
def spam():
    eggs = 'spam local'
    print(eggs) # prints 'spam local'
    def bacon():

eggs = 'bacon local'
    print(eggs) # prints 'bacon local'
    spam()
    print(eggs) # prints 'bacon local'

ggs = 'global'
bacon()
print(eggs) # prints 'global'
```
Since these three separate variables all have the same name, it can be confusing to keep track of which one is being used at any given time. This is why you should avoid using the same variable name in different scopes.

## The global Statement
```python
def spam():
    global eggs
    eggs = 'spam' # this is the global

def bacon():
    eggs = 'bacon' # this is a local
    def ham():
    print(eggs) # this is the global

eggs = 42 # this is the global
spam()
print(eggs)
```
If you ever want to modify the value stored in a global variable from in a function, you must use a global statement on that variable.

**If you try to use a local variable in a function before you assign a value to it**, as in the following program, Python will give you an error.

```python
def spam():
    print(eggs) # ERROR! UnboundLocalError: local variable 'eggs' referenced before assignment
    eggs = 'spam local'

eggs = 'global'
spam()
```
This error happens because Python sees that there is an assignment statement for eggs in the spam() function and therefore **considers eggs to be local.** But because print(eggs) is executed before eggs is assigned anything, the local variable eggs doesn’t exist. **Python will not fall back to using the global eggs variable.**

## Reference
[Automate The Boring Stuff Chapter3][5]


  [1]: https://www.youtube.com/watch?v=BXCEFAzhxGY
  [2]: https://github.com/xiaoylu/leetcode_category/tree/master/KMP
  [3]: https://hackernoon.com/why-is-python-so-slow-e5074b6fe55b
  [4]: https://chrome.google.com/webstore/detail/words-discoverer-expand-y/noncaeikjgpbdeoocblijjgegnobogib?hl=en-US
  [5]: https://automatetheboringstuff.com/chapter3/