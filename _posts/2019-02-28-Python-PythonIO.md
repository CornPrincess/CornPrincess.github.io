---
layout: post
title:  "Python-IO"
date:   2019-02-19 08:52:12 +0800
categories: Python
tags: PythonIO
---

* content
{:toc}

一种较为经典的IO调用方法：
```
path = './test.txt'

try:
    file_name = open(path, 'w')
    print('write length: ', file_name.write('love minmin'))
finally:
    if file_name:
        file_name.close()
```
不过可以用`with`字段来对代码进行改进，其中这是比较好的一篇[博客][1]
```
with open(path, 'w') as f:
    f_name = open(path, 'w')
    print('write length: ', f_name.write('love minmin'))
```

文件读取的迭代操作
```
path = './test.txt'

f_name = open(path, 'r')
while True:
    # c_str = f_name.readline(1) 按行操作
    c_str = f_name.read(1) # 按字节操作
    print('read str is:', c_str)
    if not c_str:
        break
f_name.close()
```

  [1]: https://www.cnblogs.com/DswCnblog/p/6126588.html