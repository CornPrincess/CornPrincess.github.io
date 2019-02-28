---
layout: post
title:  "Python-语句与函数"
date:   2019-02-18 18:17:10 +0800
categories: Python
tags: Python语句与函数
---

* content
{:toc}

### import语句
当解释器遇到import语句时，如果模块在当前路径就会被导入，搜索路径是一个解释器，会先搜索吧所有目录的列表。搜索路径是由一系列目录名组成的，Python解释器会一次从这些目录寻找引入的模块，`搜索路径`是在Python编译或者安装的时候确定的，被存储在`sys`模块的`path`变量中，可这样查看搜索路径。
```python
import sys

print('Python的搜索路径为： %s' %sys.path)
```

import有两种使用方式
```
# 导入模块的两种方式

# 导入整个模块
import math
import math as m(取别名)
# 导入模块中制定的对象到当前命名空间
from math import pi
from math import pi, sin(导入多个)
```

## 多种赋值方式
多个赋值操作同时进行,这种方式为`序列解包（sequence unpacking）`
```
x,y,z = 1,2,3

student = {'name':'minmin', 'age':18}
key, value = student.popitem()

nums = 1,2,3
a,b,c = nums
```

链式赋值
```
a,b,c = 1,2,3
```

## if语句
下面值作为布尔变量时，以下值为被解释器看作假，False和0等价，True和1等价：
```
False, None, 0, '', (), [], {}

>>> True == 1
True
>>> False == 0
True
>>> True + False +2
3
```

## 闭包（Closure）
如果在一个内部函数里对外部函数（不是在全局作用域）的变量进行引用，内部函数就被认为是闭包。
```
#!/usr/bin/python3
#-*-coding:UTF-8-*-

def sum_late(*args):
	def calc_sum():
		sum = 0
		for n in args:
			sum += n
		return sum
	return calc_sum
	
print('调用sum_late的结果： ', sum_late(1,2,3,4))
calc_sum = sum_late(1,2,3,4)
print('调用calc_sum的结果: ', calc_sum())

# 运行结果
调用sum_late的结果：  <function sum_late.<locals>.calc_sum at 0x000000000344A840
>
调用calc_sum的结果:  10
```

再看另外一个例子
```
def count():
	fs = []
	for n in range(1,4):
		def f():
			return n * n
		fs.append(f)
	return fs
	
f1, f2, f3 = count()

print('f1运行的结果是：', f1())
print('f2运行的结果是：', f2())
print('f3运行的结果是：', f3())

# 运行结果
f1运行的结果是： 9
f2运行的结果是： 9
f3运行的结果是： 9
```
由输出结果可以看到，返回的三个函数输出结果相同，因为它并非立刻执行，等到三个函数都返回了，其引用的变量n已经变成3，所以最终结果都为9。

**注**：返回闭包时，返回函数不要引用任何循环变量或者后续会发生变化的变量，否则很容易出错，上面的代码可以改为如下，已达到预期的效果。

```
#!/usr/bin/python3
#-*-coding:UTF-8-*-

def count():
	def f(i):
		def g():
			return i * i
		return g
	fs = []
	for n in range(1,4):
		fs.append(f(n))
	return fs
	
f1, f2, f3 = count()
print(f1, f2, f3)
print(f1(), f2(), f3())
```

## 递归与尾递归优化
最简单的求阶乘递归代码
```
def fact(n):
	if n == 1:
		return 1
	return n * fact(n-1) 
```
当递归次数太多，这时会发生栈溢出现象，此时可以用尾递归优化来解决。
尾递归是指在函数返回时调用函数本身，并且return语句**不能包含表达式**，这样编译器和解释器就可以进行优化，使得递归本身无论调用多少次都只占用一个栈帧。

书中给出的例子：
```
def tail_recursion(num, product):
	if num == 1:
		return product
	return tail_recursion(num - 1, num * product)
	
print(tail_recursion(5, 1))
print(tail_recursion(900, 1))
```
不过有[博客][1]指出python不支持尾递归，这点有待考证

## 匿名函数
在程序一次性使用，不需要定义函数名时，用匿名函数可以节省内存中变量定义空间，并且让程序更简洁，这是可以使用lambda表达式。
```
# 常规方法
list1 = [1,2,3,4,5]
list2 = []
for n in list1:
	if n > 2:
		list2.append(n)
print(list2)

# 函数式编程
def func(x):
	return x > 3

# filter返回的是一个迭代器
list3 = filter(func, list1)
print(list(list3))

# lambda表达式
print([item for item in filter(lambda x: x>3, list1)])
​````

  [1]: https://blog.csdn.net/qq_39521554/article/details/80112748
```