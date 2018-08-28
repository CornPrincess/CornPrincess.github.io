---
layout: post
title:  "JavaWeb-Servlet(1)"
date:   2018-08-26 11:35:31 +0800
categories: JavaWeb
tags: Servlet 设计模式 HttpServletRequest HttpServletResponse 中文乱码问题
---

* content
{:toc}

## Servlet基础
宏观地讲，Servlet 是连接 Web 服务器与服务端 Java 程序的协议，是一种通信规范。这个规范是以一套接口的形式体现的。微观地讲，Servlet 是 Servlet 接口实现类的一个实例对象，是运行在服务器上的一段 Java
小程序，即 Server Applet，也就是 Servlet 这个单词的来历。Servlet 的主要功能是根据客户
端提交的请求，调用服务器端相关 Java 代码，完成对请求的处理与运算。
![Servlet][1]

### Servlet 生命周期
所谓 Servlet生命周期是指，Servlet对象的创建、Servlet 对象的初始化、Servlet对象服务的执行，及最终 Servlet 对象被销毁的整个过程。

Servlet 的整个生命周期过程的执行，均由Web服务器负责管理。即Servlet从创建到服务到销毁的整个过程中方法的调用，都是由Web服务器负责调用执行，程序员无法控制其执行流程。
![servlet生命周期][2]

 > Servlet 生命周期方法的执行流程：
（1）当请求发送到 Web 容器后，Web 容器会解析请求 URL，并从中分离出 Servlet 对应的URI。
（2）根据分离出的 URI，通过web.xml中配置的URI与Servlet的映射关系，找到要执行的Servlet，即找到用于处理该请求的 Servlet。
（3）若该 Servlet 不存在，则调用该Servlet的无参构造器、init()方法，实例化该Servlet。然后执行service()方法。
（4）若该 Servlet 已经被创建，则直接调用 service()方法。
（5）当 Web 容器被关闭，或该应用被关闭，则调用执行 destroy()方法，销毁 Servlet 实例。

### Servlet 特征
 > （1）Servlet 是单例多线程的。
（2）一个 Servlet 实例只会执行一次无参构造器与 init()方法，并且是在第一次访问时执行。
（3）用户每提交一次对当前 Servlet 的请求，就会执行一次 service()方法。
（4）一个 Servlet 实例只会执行一次 destroy()方法，在应用停止时执行。
（5）由于 Servlet 是单例多线程的，所以为了保证其线程安全性，一般情况下是不为Servlet类定义可修改的成员变量的。因为每个线程均可修改这个成员变量，会出现线程安全问题。
（6）默认情况下，Servlet 在 Web 容器启动时是不会被实例化的。

### 容器中的两个Map
当 Servlet 实例被创建好后被放在了哪里？web.xml中URI与Servlet的映射关系反映到内存中是以什么形式存在呢？

#### Map<String, Servlet>
当 Servlet 实例被创建好后，会将该Servlet实例的引用存放到一个 Map 集合中。该Map集合的 key 为 URI，而value则为Servlet实例的引用，即Map<String, Servlet>。当Web容器从用户请求中分离出 URI 后，会首先到这个Map中查找是否存在其所对应的 value。若存在，则直接调用其service()方法。若不存在，则需要创建该Servlet实例。

#### Map<String, String>
若请求的 Servlet实例不存在，Web容器又是根据什么创建这个Servlet实例的呢？在Web容器的内存中，还存在一个 Map 集合。该 Map 集合的key为 URI，而 value 则为 web.xml中配置的与之对应的 Servlet 的全限定性类名，即Map<String,String>。
当 Web 容器从用户请求中分离出URI后，到第一个 Map 中又没有找到其所对应的Servlet实例，则会马上查找这第二个Map，从中找到其所对应的类名，再根据反射机制，创建这个Servlet实例。然后再将这个创建好的 Servlet 的引用放入到第一个 Map 中。

这种情况下的servlet时序图
![servlet时序图][3]
两个map
![两个map][4]

## ServletContext对象
ServletContext对象：servlet容器在启动时会加载web应用，并为每个web应用创建唯一的servlet context对象，可以把ServletContext看成是一个Web应用的服务器端组件的共享内存，在ServletContext中可以存放共享数据。ServletContext对象是真正的一个全局对象，凡是web容器中的Servlet都可以访问。

这个 Servlet运行环境中都包含哪些具体的“环境”呢？即 ServletContext对象中都包含哪些具体的信息呢？不仅包含了web.xml文件中的配置信息还包含了当前应用中所有Servlet可以共享的数据可以这么说，ServeltContext可以代表整个应用。所以，ServletConetxt有另外一个名称：application。

## Servlet中的设计模式
在通过实现 Servlet接口来定义Servlet时存在一个很不方便的问题：有太多不需要的方法必须要实现。通常我们只关心service()方法，在service()方法中完成业务逻辑，但由于Servlet接口中还存在另外四个方法，所以也要必须实现。

### 缺省适配器设计模式
由于 Servlet中通常只使用service()方法，其它四个方法基本不用，但也需要实现，于是我们就想，能否定义一个通用的抽象类，让其实现 Service 接口，并以简单方式对service()以外的其它方法进行实现，即要么是空方法体，要么返回null，而将 service()方法声明为抽象方法。这样，以后再定义 Servlet时就只需要继承自这个通用的抽象类即可，无需再实现了Service 接口了。
代码实现：
```java
public abstract class MyGenericServlet implements Servlet, ServletConfig {
	private ServletConfig config;

	public void init(ServletConfig config) throws ServletException {
		this.config = config;
	}

	public ServletConfig getServletConfig() {
		return config;
	}
	
	// 抽象方法，专门让子类实现
	public abstract void service(ServletRequest req, ServletResponse res) throws ServletException, IOException; 
		
	
	public String getServletInfo() {
		return "";
	}

	public void destroy() {
		
	}

	public String getServletName() {
		return getServletConfig().getServletName();
	}

	public ServletContext getServletContext() {
		return getServletConfig().getServletContext();
	}

	public String getInitParameter(String name) {
		return getServletConfig().getInitParameter(name);
	}

	public Enumeration<String> getInitParameterNames() {
		return getServletConfig().getInitParameterNames();
	}
}
```
若`GenericServlet`的子类重写`init`方法，则子类必须调用父类的`init`方法，来初始化`config`参数。

**解决方法：模板设计模式**
在GenericServlet中添加无参init方法，专门用来让子类重写。

```java
// 更改部分GenericServlet代码
public void init(ServletConfig config) throws ServletException {
		this.config = config;
		// 调用无参方法
		this.init();
	}
	
	/**
	 * 专门让子类重写该方法
	 */
	public void init() {
		
	}  
	
public class SomeServlet extends MyGenericServlet {

	@Override
	public void init() {
		System.out.println("SomeServlet init method");
	}
}
```
![对象的创建过程][5]
在编译期检查GernericServlet中有没有init()方法，在运行时调用SomeServlet里的init()方法。

## HttpServletRequest
javax.servlet.http.HttpServletRequest是SUN制定的Servlet规范，是一个接口，表示请求，其父接口是javax.servlet.ServletRequest。“HTTP 请求协议”的完整内容都被封装到request对象中。
Apache 软件基金会开发的“Tomcat容器”对javax.servlet.http.HttpServletRequest接口进行了实现，其实现类完整类名是org.apache.catalina.connector.RequestFacade

HttpServletRequest实例对象是什么时候创建和销毁的呢？
当客户端浏览器将请求（字符序列）发送到服务器后，服务器会根据HTTP请求协议的格式对请求进行解析。同时，服务器会创建 HttpServletRequest 的实现类 RequestFacade的对象，即请求对象。然后再调用相应的set方法，将解析出的数据封装到请求对象中。此时HttpServletRequest实例就创建并初始化完毕了。也就是说，请求对象是由服务器创建。
当服务器向客户端发送响应结束后，HttpServletRequest 实例对象被服务器销毁。
一次请求对应一个请求对象，另外一次请求对应外一个请求对象，与之前的请求对象没有任何关系。HttpServletRequest 实例的生命周期很短暂。

### 中文乱码问题
#### 读取post请求参数的乱码问题:
在接收请求参数之前先通过request的setCharacterEncoding()方法，指定**请求体**的字符编码格式。
```java
// 设置请求体的编码方式
		request.setCharacterEncoding("UTF-8");
		
		String name = request.getParameter("username");
		String password = request.getParameter("password");
		
		System.out.println("username: " + name);
		System.out.println("password: " + password);
```

#### 读取get请求中的参数乱码问题
tomcat9已经解决该问题，若是之前的版本可以在conf/server.xml文件中，配置connector标签解决。<br>
![Connector配置][6]

#### 万能解决方法：
从数据在请求中的存放形式，到数据被 Tomcat 中的 Servlet 接收到后的存放形式，均是
由单个字节的形式存在，而在众多字符编码格式中，ISO8859-1 为单字节编码，所以，首先
以ISO8859-1 的形式先对单字节的数据进行编码，并将编码后的数据存放在字节数组中。然后，再将字节数组中的数据，按照指定的 UTF-8 格式进行解码，即变为了需要的 UTF-8 字符编码的数据，解决了中文乱码问题。
```java
String name = request.getParameter("username");
		String password = request.getParameter("password");
		
		// 编码
		byte[] bytes = name.getBytes("ISO8859-1");
		// 解码
		name = new String(bytes, "UTF-8");
		
		System.out.println("username: " + name);
		System.out.println("password: " + password);
```

## HttpServletResponse
### 中文乱码问题
若在 PrintWriter流中写入中文字符，那么，在客户端浏览器中将显示乱码。例如，本例中的username 在表单中填入汉字，则将显示乱码。只所以响应时会产生乱码，是因为HTTP协议中规定，默认响应体的字符编码为ISO-8859-1。所以，若要解决乱码问题，就需要修改响应体的默认编码。一般情况下，有两种方式可以修改：
### setContentType
HttpServletResponse的setContentType(“text/html;charset=utf-8”)方法，用于设置响应内容的MIME类型，其中可以指定MIME的字符编码。而MIME的字符编码，即响应体的字符编码。
### setCharacterEncoding
setCharacterEncoding(“utf-8”)方法，用于修改MIME 的字符编码，即修改响应体的字符编码。但使用 setCharacterEncoding()方法的前提是，之前必须要通过使用方法setContentType()方法设置响应内容的 MIME类型。否则setCharacterEncoding()方法不起作用。

**注意：**
**这些设置，必须在PrintWriter对象产生之前先设置，否则将不起作用。**




参考链接：[ServletConfig对象与ServletContext区别][7]


  [1]: http://static.zybuluo.com/xiaocorn/ililnfshvirp2nw58w41cvfx/image_1clq5o0ddr38cdk1c9a1s151h8g9.png
  [2]: http://static.zybuluo.com/xiaocorn/o5p67obiyf65vdks7qm1pgbd/image_1clqjv4kp14ll1prd1bcv16ja1ittm.png
  [3]: http://static.zybuluo.com/xiaocorn/yav0haxxreki3lytayf22ffr/image_1clqmltkq1a841m2cbcl13s81q7p13.png
  [4]: http://static.zybuluo.com/xiaocorn/vrayu3jcad5z7e2ehdh8ihjn/image_1clqn0k4smqg17eq126at571rr31g.png
  [5]: http://static.zybuluo.com/xiaocorn/j29bxoku65hqlx944oxala40/image_1clr58np71tpv1mfoo3ma2ppg1t.png
  [6]: http://static.zybuluo.com/xiaocorn/ilbmk3lg3wayfjreieamg3a6/image_1clt485h31o20db818311h6c1ihk19.png
  [7]: https://blog.csdn.net/wangqing84411433/article/details/71131608