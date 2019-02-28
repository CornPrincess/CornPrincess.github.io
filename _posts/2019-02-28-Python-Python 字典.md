---
layout: post
title:  "Python-字典"
date:   2019-02-16 17:15:12 +0800
categories: Python
tags: Python 字典
---

* content
{:toc}

字典是Python里唯一内建的映射类型，字典指定值并没有特殊顺序，都存储在一个特殊的键（Key）里，键可以是数字，字符串和元组。
字典是一种可变容器模型，可以存储任意类型对象。

键必须是唯一的，且不可变，如字符串，数字和元组，值可以是任意类型。

## 字典的操作
### 字典的创建
```
>>> a = dict(one=1, two=2, three=3)
>>> b = {'one': 1, 'two': 2, 'three': 3}
>>> c = dict(zip(['one', 'two', 'three'], [1, 2, 3]))
>>> d = dict([('two', 2), ('one', 1), ('three', 3)])
>>> e = dict({'three': 3, 'one': 1, 'two': 2})
>>> a == b == c == d == e
True
```
也可以通过构造器创建
```
class dict(**kwarg)
class dict(mapping, **kwarg)
class dict(iterable, **kwarg)
```

### 格式化字符串
```
>>> p=dict([('name','minmin'),('age',17)])
      
>>> p
      
{'name': 'minmin', 'age': 17}
>>> p['name']
      
'minmin'
>>> type(p)
      
<class 'dict'>
>>> print('name: %(name)s' %p)
      
name: minmin
```

## 参考资料
1. 《Python3.5从零开始学》清华大学出版社 刘宇宙编著 2017年8月第一版
2. [Python官方文档][1]


  [1]: https://docs.python.org/3.7/library/stdtypes.html#mapping-types-dict