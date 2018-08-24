---
layout: post
title:  "JavaWeb-监听器Listener"
date:   2018-08-23 10:15:31 +0800
categories: JavaWeb
tags: 监听器 设计模式
---

* content
{:toc}

## Servlet规范里的监听器Listener
Servlet 规范中已经定义好了八个监听器接口，它们要监听的对象分别是`request`、`session`、`servletContext` 对象，触发监听器的事件是这三个对象的创建与销毁，它们的域属性空间中属性的添加、删除、修改，及 session 的钝化与活化操作。
在 JavaWeb 项目中使用监听器，需要在 web.xml 文件中对监听器进行注册。
 ![注册监听器][1]

### 1.ServletRequestListener
#### （1）定义监听器
```java
public class MyRequestListener implements ServletRequestListener {

	@Override
	public void requestDestroyed(ServletRequestEvent sre) {
		System.out.println("请求对象被销毁");
	}

	@Override
	public void requestInitialized(ServletRequestEvent sre) {
		System.out.println("请求对象被创建");
	}	
}
```
#### （2）注册监听器
```xml
<listener>
  	<listener-class>love.minmin.servletListener.MyRequestListener</listener-class>
</listener>
```
一下例子只展示监听器代码，注册部分省略
### 2.ServletRequestAttributeListener
该监听器用于完成对 request 域属性空间中属性的添加、修改、删除操作的监听。
```java
public class MyRequestAttributeListener implements ServletRequestAttributeListener {

	@Override
	public void attributeAdded(ServletRequestAttributeEvent srae) {
		System.out.println("add attribute: " + srae.getName() + "=" + srae.getValue());
	}

	@Override
	public void attributeRemoved(ServletRequestAttributeEvent srae) {
		System.out.println("remove attribute: " + srae.getName() + "=" + srae.getValue());
	}

	@Override
	public void attributeReplaced(ServletRequestAttributeEvent srae) {
		System.out.println("replace attribute: " + srae.getName() + "=" + srae.getValue());
	}	
}
```

### 3.HttpSessionListener
该监听器用于完成对Session对象的创建及销毁的监听。
```java
public class MyRequestListener implements ServletRequestListener {

	@Override
	public void requestDestroyed(ServletRequestEvent sre) {
		System.out.println("请求对象被销毁");
	}

	@Override
	public void requestInitialized(ServletRequestEvent sre) {
		System.out.println("请求对象被创建");
	}	
}
```

session在服务器销毁的两种方式
1. 在web.xml文件里这是seession持续时间，默认30分钟
```xml
<session-config>
  	<session-timeout>30</session-timeout>
</session-config>
```
2. 在jsp文件里通过invalidate方法销毁
```jsp
session.invalidate();
```

### 4.HttpSessionAttributeListener
该监听器用于完成对session域属性空间中属性的添加、修改、删除操作的监听。
```java
public class MyHttpSessionAttributeListener implements HttpSessionAttributeListener{

	@Override
	public void attributeAdded(HttpSessionBindingEvent se) {
		System.out.println("session attribute added: " + se.getName() + "=" + se.getValue());
	}

	@Override
	public void attributeRemoved(HttpSessionBindingEvent se) {
		System.out.println("session attribute removed: " + se.getName() + "=" + se.getValue());
	}

	@Override
	public void attributeReplaced(HttpSessionBindingEvent se) {
		System.out.println("session attribute replaced: " + se.getName() + "=" + se.getValue());
	}	
}
```

### 5.ServletContextListener
该监听器用于完成对ServletContext对象的创建及销毁的监听。不过需要注意，由于ServletContext在一个应用中只有一个，且是在服务器启动时创建。另外，ServletConetxt的生命周期与整个应用的相同，所以当项目重新部署，或Tomcat正常关闭（通过 stop service关闭，不能是terminate关闭）时，可以销毁 ServletContext。
```java
public class MyServletContextListener implements ServletContextListener {

	@Override
	public void contextInitialized(ServletContextEvent sce) {
		System.out.println("servletContext initialized");
	}

	@Override
	public void contextDestroyed(ServletContextEvent sce) {
		System.out.println("servletContext destroyed");
	}
}
```

### 6.ServletContextAttributeListener
```java
public class MyServletContextAttributeListener implements ServletContextAttributeListener{

	@Override
	public void attributeAdded(ServletContextAttributeEvent scae) {
		System.out.println("servletContext attribute added: " + scae.getName() + "=" + scae.getValue());
	}

	@Override
	public void attributeRemoved(ServletContextAttributeEvent scae) {
		System.out.println("servletContext attribute removed: " + scae.getName() + "=" + scae.getValue());
	}

	@Override
	public void attributeReplaced(ServletContextAttributeEvent scae) {
		System.out.println("servletContext attribute replaced: " + scae.getName() + "=" + scae.getValue());
	}
}
```
### 7.HttpSessionBindingListener
该监听器用于监听指定类型对象与Session的绑定与解绑，即该类型对象被放入到Session 域中，或从 Session 域中删除该类型对象，均会引发该监听中相应方法的执行。

它与HttpSessionAttributeListener的不同之处是，该监听器监听的是指定类型的对象在Session 域中的操作，而HttpSessionAttributeListener监听的是 Session域属性空间的变化，无论是什么类型的对象。

注意：
1. 该监听器是由实体类实现
2. 该监听器无需在 web.xml 中注册
```java
// 把当前类的对象绑定到Session时触发
	@Override
	public void valueBound(HttpSessionBindingEvent event) {
		System.out.println("student instance bind to session");
		System.out.println("getName: " + event.getName());
		System.out.println("getValue: " + event.getValue());
		System.out.println("----------------------------------");
	}

	// 把当前类的对象与Session时触发
	@Override
	public void valueUnbound(HttpSessionBindingEvent event) {
		System.out.println("student instance unbind with session");
		System.out.println("getName: " + event.getName());
		System.out.println("getValue: " + event.getValue());
		System.out.println("----------------------------------");
	}
```

```
<%
	Student stu = new Student("minmin", 18);
	session.setAttribute("stu", stu);
	session.removeAttribute("stu");
%>
```

### 8.HttpSessionActivationListener
该监听器用于监听在Session中存放的指定类型对象的钝化与活化。

钝化是指将内存中的数据写入到硬盘中，而活化是指将硬盘中的数据恢复到内存。当用户正在访问的应用或该应用所在的服务器由于种种原因被停掉，然后在短时间内又重启，此时用户在访问时Session 中的数据是不能丢掉的，在应用关闭之前，需要数据写入到硬盘，在重启后应可以立即重新恢复Session 中的数据。这就称为 Session 的钝化与活化。

那么 Session中的哪些数据能够钝化呢？只有存放在 JVM 堆内存中的实现了Serializable类的对象能够被钝化。**也就是说，对于字符串常量、基本数据类型常量等存放在JVM方法区中常量池中的常量，是无法被钝化的。**

对于监听 Session中对象数据的钝化与活化，需要注意以下几点：
1. 实体类除了要实现HttpSessionActivationListener 接口外，还需要实现 Serializable 接口。
2. 钝化指的是 Session中对象数据的钝化，并非是 Session 的钝化。所以Session中有几个可以钝化的对象，就会发生几次钝化。
3. HttpSessionActivationListener监听器是不需要在 web.xml 中注册的。


  [1]: http://static.zybuluo.com/xiaocorn/c9oozglcdsja9jqejyrrqrjh/image_1clkrqhtcc4h1fqh1bko1ifh8g49.png