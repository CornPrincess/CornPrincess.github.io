---
layout: post
title:  "Python-简介"
date:   2019-02-16 16:20:12 +0800
categories: Python
tags: Python介绍
---

* content
{:toc}

2019年猪年年初七开始在上海实习，由于部门需要用Python写自动化测试用例，笔者从今天开始系统学习Python（版本为3.7.2），因此新开一个博客类别作为读书笔记，书本为》[《Python3.5从零开始学》][1]，书中所写难免有错误，希望大家发现后指出，一起学习交流。

Python的创始人是Guido van Rossum，一位荷兰的天才，Python这个名字来自于Guido所挚爱的电视剧——Monty Python's Flying Circus。更多关于Python的历史与介绍可以自行wiki。

简单来说，Python是一个简单，解释型，交互式，可移植，动态的，面向对象的超高级语。

## 数据类型
Python目前支持六种数据类型：Number，String，List，Tuple，Sets，Dictionary。（这种分类参考书上，与官网有所不一致。）
Python3支持三种不同的数据类型，int， float，complex，去掉了Python2的long。

## 表达式和语句
```python
>>> 3*3 # 表达式
9
>>> print(3*3) # 语句
9
>>> a=3*3 # 语句
>>>
```
观察以上代码，在交互模式下，对于前两个命令，解释器总是输出所有表达式的值（内部都使用相同的函数对结果进行呈现），而第三个赋值语句没有输出结果，因为语句不是表达式，所以没有值可供交互式解释器输出。

赋值语句有两个作用：一是建立新的变量，二是将值赋予变量，语句的特性即，它改变了事物。

## 运算符和操作对象
**Python支持7中运算符：算术运算符，比较运算符，赋值运算符，逻辑运算符，位运算符，成员运算符，身份运算符**。
这里记录一些之前没接触过的。

### 算术运算符
1. `a ** b`， 同`pow(a, b)`, 表示a的b次幂。
2. `a // b`, 取整除。 9 // 2 = 4， 9.0 // 2 = 4.0

### 比较运算符
1. Python3中不等于是`!=`， Python2中不等于是`<>`.
2. 在一些地方，可以用1代表True，0代表False

### 逻辑运算符
1.` a and b`， 若a为False或0，则返回False或0， 反之返回b。
2. `a or b`， 若a不为0（False）， 则返回a，反之返回b。
3. `not a`， 若a为0（False），则返回True，反之返回False。

### 成员运算符
`a in List`（`a not in List`）， 作用为查找a是否在在指定的序列里。

### 身份运算符
`a is b`（`a is not b`）， 作用为判断a，b是否引用自一个对象。

## 参考资料
1. 《Python3.5从零开始学》清华大学出版社 刘宇宙编著 2017年8月第一版


  [1]: https://read.douban.com/ebook/44251703/