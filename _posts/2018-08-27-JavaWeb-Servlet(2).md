﻿---
layout: post
title:  "JavaWeb-Servlet(2)"
date:   2018-08-27 17:23:31 +0800
categories: JavaWeb
tags: 转发与重定向 线程安全
---

* content
{:toc}

## 请求转发与重定向
### 请求转发
请求转发是指，资源1在服务器内部，直接跳转到资源2，所以请求转发也称为**服务器内跳转**。整个过程浏览器只发出一次请求，服务器只发出一次响应。所以，无论是资源1还是资源2，整个过程中，只存在一次请求，即用户所提交的请求。所以，无论是资源1 还是资源2，它们均可从这个请求中获取到用户提交请求时所携带的相关数据。


### 重定向
重定向是指，资源1需要访问资源2，但并未在服务器内直接访问，而是由服务器自动向浏览器发送一个响应，浏览器再自动提交一个新的请求，这个请求就是对资源 2 的请求。对于资源2的访问，是先跳出了服务器，跳转到了客户端浏览器，再跳回到了服务器。**所以重定向又称为服务器外跳转。**

#### 重定向中中文乱码问题
因为http协议底层用的是tcp协议，tcp协议利用字节流进行传输，所以要先利用UrlEncoder对放入URL中的变量进行编码，然后接受以后利用UrlDecode进行解码
```java
    // 解决重定向中URL乱码问题
	String encodeUsername = URLEncoder.encode(username, "UTF-8");
    // redirect
	response.sendRedirect("second?user=" + encodeUsername);
	
	String user = request.getParameter("user");
	// 解码
	user = URLDecoder.decode(user, "UTF-8");
```

## Servlet 的线程安全问题
### JVM 中可能存在线程安全问题的数据分析
#### 栈内存数据分析
栈内存是多例的，即JVM会为每个线程创建一个栈，所以其中的数据不是共享的。另外，方法中的局部变量存放在Stack的栈帧中，方法执行完毕，栈帧弹栈，局部变量消失。局部变量是局部的，不是共享的。所以栈内存中的数据不存在线程安全问题。
#### 堆内存数据分析
一个 JVM 中只存在一个堆内存，堆内存是共享。被创建出的对象是存放在堆内存的，而存放在堆内存中的对象，实际就是对象成员变量的值的集合。即**成员变量**是存放在堆内存的。堆内存中的数据是多线程共享的，也就是说，**堆内存中的数据是存在线程安全问题的。**
#### 方法区数据分析
一个 JVM 中只存在一个方法区。**静态变量与常量存放在方法区，方法区是多线程共享的。常量是不能被修改的量，所以常量不存在线程安全问题。静态变量是多线程共享的，所以静态变量存在线程安全问题。**

解决方法：
1.避免使用成员变量
2.若使用成员变量，可以使用synchronized方法进行串行化cao



