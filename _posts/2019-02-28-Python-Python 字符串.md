---
layout: post
title:  "Python-字符串"
date:   2019-02-16 17:15:12 +0800
categories: Python
tags: Python 字符串
---

* content
{:toc}

Python种最基本的数据结构是序列（Sequence），其中包含了6种内建序列，列表（List），元组（Tuple），字符串，Unicode字符串，buffer对象，xrange对象。

## 字符串操作
### 字符串不能修改
```Python
>>> field='just do it'
     
>>> field[-3:] = 'ok'
      
Traceback (most recent call last):
  File "<pyshell#28>", line 1, in <module>
    field[-3:] = 'ok'
TypeError: 'str' object does not support item assignment
>>> 
```
str类型的对象不支持修改。

### 字符串格式化
```
>>> print('hello, %s' %'world')
      
hello, world
```
`%s`为占位符，用来格式化字符串，此处主要记录语法。

```
>>> print('%s %s %d' %('2019','02',16))
  
2019 02 16
>>> print('%s %s %d' %['2019','02',16])

SyntaxError: invalid syntax
>>> 
>>> print('%*.*f' %(10, 5, 3.1415926))
      
   3.14159
>>> 
```
注：只有元组和字典可以格式化一个以上的值。

## 参考资料
1. 《Python3.5从零开始学》清华大学出版社 刘宇宙编著 2017年8月第一版
