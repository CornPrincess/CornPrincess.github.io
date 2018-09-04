---
layout: post
title:  "JavaWeb-JSP"
date:   2018-08-31 22:26:31 +0800
categories: JavaWeb
tags: JSP
---

* content
{:toc}

## JSP的调用与运行原理
JSP，即 JavaServerPages，是Java服务器页面。JSP 技术是在传统的静态网页HTML文件中插入Java代码片断和 JSP 标签后形成的一种文件，其后缀名为.js使用 JSP 开发的Web应用是跨平台的，既能在Linux上运行，也能在其他操作系统上运行。

JSP本质是一个servlet，每个JSP页面在第一次被访问时，WEB容器会把请求交给JSP引擎(即一个JAVA程序)处理。JSP引擎先将JSP翻译成一个`_jspServlet`(实质也是一个servlet)，然后编译为class文件，然后按照servlet的调用方式进行调用。

 - 服务器会将jsp先翻译成servlet，这个servlet位于tomcat服务器`work`目录，这jsp类的父类是`org.apache.jasper.runtime.HttpJspBase`,这个HttpJspBase类继承自`HttpServlet`<br>
 ![jsp继承关系][1]
 - 向服务器发请求会调用servlet的`service`方法;同样地，访问jsp会调用这个JSP类的`_jspService`方法。
 - JSP中的**标签语言**会在`_jspService`方法中通过`out.write()`写出来；JSP中的**Java代码**会原封不动的搬到`_jspService`方法中。
 - 在_jspService方法中提前准备好了一些对象供JSP调用，如：`out`,`page`,`application`,`request`,`response`等等。

> java代码块：<% %>, 对应到jspservice方法中的代码
jsp声明语句块：<%! %>， 对应到jspservlet类中的代码。
jsp表达式块：<%= %>
被jsp引擎翻译到jspService()的out.write()方法中输出。

## JSP核心
### JSP内置对象
在`jspServlice()`方法中内置了九个对象<br>
![jsp内置对象][2]
### JSP指令(directive)
#### page指令
page指令用于设置当前JSP页面的相关信息。一个 JSP 文件中可以包含多个 page 指令。
1. `pageEncoding`属性用于设置当前JSP页面所使用的字符编码格式。即，用户在浏览器中通过右击查看编码所看到的编码格式。
2. `contentType`属性用于设置当前JSP页面呈现于用户浏览器中的内容类型，通常为`text/html`类型，即`html`格式的文本。
总结：
当内容类型为`text/html`时，使用`pageEncoding`属性与`contentType`属性效果是相同的。只有当内容类型不为`text/html`时，才专门使用`contentType`属性指定。在指定字符编码时，这两个属性一般不同时使用。
3. `errorPage`属性用于指定，当前页面运行过程中发生异常时所要跳转到的页面。
4. 当一个页面的page指令中设置`isErrorPage`的值为`true`时，表明当前页面为一个“错误处理页面”。默认`isErrorPage`的值为`false`。一旦一个页面page指令的`isErrorPage`属性被指定为了 true,在`_jspService()`方法中，多出了一个变量`exception`。这就是内置对象`exception`，可以在JSP的Java代码块、表达式块中直接使用的内置对象。
5. `session`属性用于指定当前页面中是否可以直接使用内置对象`session`。默认为`true`，可以看到session的创建，使用的是无参方法`getSession()`。若设置session属性的值为`false`，查看生成的Servlet代码，会发现根本就没有出现内置对象`session`。

#### include指令
被include指定包含的文件，可以是JSP动态页面文件，也可以是HTML静态页面文件。
 > <%@ include file=""%>
 JS 翻译引擎在翻译时，会将include指令所
指定的文件内容直接翻译到当前JSP对应的Servlet 中，形成一个.java文件。这就说明一个问题：这个包含操作是在编译之前完成的，是在编译之前由 JSP 翻译引擎完成的，不是在程序运行期完成的。这种包含是一种静态包含，称为静态联编。
由于在编译期就将这些文件合并为了一个 Servlet 文件，所以，整个过程就一个_jspService()方法。也就是说，这些文件之间是可以相互访问局部变量的。只要满足变量声明与使用的先后顺序即可。

### JSP动作(Action)
JSP动作是指，使用系统定义好的标签来完成本应由Java代码来完成的功能。JSP动作很多，但在实际开发时常用的就两个：`转发动作`与`包含动作`。底层使用的是`RequestDispatcher`的`forward()`与`include()`方法实现的。**而这两份种请求转发方式的本质区别是，标准输出流的开启时间不同。**forward()方式的标准输出流是在目标资源中开启的标准输出流，而include()方式的标准输出流则是在当前发出包含运作的页面中开启的。所以，forward()动作的发起页面中是无法向标准输出流中写入数据的；而include()动作的发起页面及目标页面中均可向标准输出流中写入数据。

注意：
使用include动作时，work目录下会出现两个java文件，这是在运行期间完成的，是在程序运行过程中，由`index_jsp`文件中的`_jspService()`方法通过`JspRuntimeLibrary`类的`include()`方法调用了`left_jsp`文件中的`_jspService()`方法。在运行期所执行的这种包含，称为`动态联编`。

应用场景：
1. 在静态联编与动态联编均可使用时**，一般使用静态联编。因为在程序运行时只存在一个Servlet，对资源的消耗较少，且不存在调用问题，执行效率较高。**
2. 若在两个文件间需要共享同一变量，此时只能使用静态联编。
3. 若在两个文件间存在同名变量，且不能混淆，此时只能使用动态联编。




  [1]: http://static.zybuluo.com/xiaocorn/v54ywhpf7nijcm3l2v2py4fm/image_1cm87689cl82qrf1oc21leoc6p9.png
  [2]: http://static.zybuluo.com/xiaocorn/xntzh9sbj5vthqyw99hj3d88/image_1cm88lbuf1jd51s3clb91aq329m.png