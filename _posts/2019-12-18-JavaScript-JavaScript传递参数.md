---
layout: post
title:  "JavaScript传递参数"
date:   2019-12-18 23:50:12 +0800
categories: JavaScript
tags: JavaScript 
---

* content
{:toc}

> ECMAScript 中所有函数的参数都是按值传递。也就是说，把函数外部的值复制给函数内部的参数，就和把值从一个变量复制到另一个变量一样 ——《JavaScript高级程序设计（第三版）》

本篇博客将围绕书中这一知识点进行深入分析。

# 相关概念
> 首先要弄清楚一些概念：变量、值、值的类型、引用、对象。如果这些概念没搞清楚，就没办法讨论这个问题。
> - **变量**：变量就是一个定义在某个作用域的名字(name)，它是一个identifier。它绑定(bind)了一块内存。
> - **值**：值就是变量绑定的那块内存里面存放的数据。至于存放的数据的具体代表什么(含义是什么)，暂且不管。但是本质上就是一段二进制数。
> - **值的类型**：即变量绑定的那块内存里放的数据应该如何去解释，或者说如何解码。在JS中值的类型有很多种，但是可以归结成两类：基本类型和引用类型。基本类型就是说：变量绑定的那块内存里放的数据的含义就是它看起来的那个样子。引用类型就是说：变量绑定的那块内存里放的数据，并不是我真正想要的数据，它是一个元数据、一个地址，一个指向我真正想用的数据的位置的指针。
> - 引用：指向对象的指针
> - 对象：上面说的那些不能被变量直接绑定，而是要通过引用来间接绑定的数据就是对象。而且对象数据与非变量数据相比有一个特点，即它是有内部结构的，即属性。

ECMAScript 变量包含两种不同的数据类型的值：基本类型值和引用类型值，其中基本类型值是**按值访问**的，引用类型值**按引用访问**。

# 复制变量的值

## 复制基本类型的值

```JavaScript
let num1 = 5;
let num2 = num1;
```

复制后变量对象：
| 变量  |       值       |
| :---: | :------------: |
| num1  | 5 (Number类型) |
| num2  | 5 (Number类型) |

复制后，num1与num2中的值相互独立，不会相互影响。

## 复制引用类型的值

```JavaScript
var a = new Object();
var b = a;
a.name = 'Alice';
console.log(b.name); // Alice
```

复制后变量对象：
| 变量  |           值           |
| :---: | :--------------------: |
|   a   | reference (Object类型) |
|   b   | reference (Object类型) |

复制引用类型时，同样也是阿静存储在变量对象中的值复制一份放到新变量的空间，不同的是，复制的值实际上是一个指针，会指向同一个对象。，所在上述代码中对b对应的对象进行改变，a对应的对象也被影响到。

# 传递参数

弄清楚了上面的概念，我们再来理解`ECMAScript中所有函数的参数都是按值传递的`，还是看原书的示例代码

## 传递基本类型参数

```JavaScript
function addTen(num) {
    num += 10;
    return num;
}

var count = 20
var result = addTen(count);
alert(count);    //20
alert(result);   //10
```
可以看到，在基本类型中，count并没有因为addTen(count)而发送改变，证明参数确实是按值传递的，如果参数按引用传递，那么count也会跟着+10，变为30

## 传递引用类型参数

```JavaScript
function setName(obj) {
    obj.name = "Nicholas";
    obj = new Object();
    obj.name = "Alice"
}

var person = new Object();
setName(person);
alert(person.name);    //"Nicholas"
```

以上代码可以证明传递引用类型参数时也是按值传递的，因为在setName方法中，及时给obj赋值一个新对象但并没有改变入参person的引用。

# 对比其他语言

## Java
按值传递。

//  TODO

# 参考

[怎么理解 JavaScript访问变量有按值和按引用两种方式](https://segmentfault.com/q/1010000004970961)