---
layout: post
title:  "JavaWeb-Servlet3.0异步"
date:   2018-09-04 12:05:31 +0800
categories: JavaWeb
tags: Servlet3.0异步
---

* content
{:toc}

## Servlet3.0异步
这里的异步处理，是指服务端的异步处理，与AJAX是没有关系的。AJAX 是客户端的异步处理。
![servlet3.0异步][1]
Servlet 是单例多线程的。当一个请求到达服务器后，服务器会马上为该请求创建一个相应的 Servlet 线程，为该请求服务。那么，一个请求就一定会有一个Servlet线程为之服务吗？答案是否定的。服务器会为每一个Servlet实例创建一个Servlet线程池，而线程池中该Servlet实例的线程对象并不是“取之不尽”的，而是有上限的。当达到该上限后，再有请求要访问该Servlet，那么该请求就只能等待了。只有当又有了空闲的Servlet线程对象后才能为该请求分配 Servlet 线程对象。

对于Servlet 来说，其最典型的工作一般分为三步：
 1. 接收请求提交参数
 2. 根据提交参数调用Servic层代码对参数进行运算
 3. 接收到Service层的运算结果后，将结果响应给客户端

其中第二步有可能是一个耗时运算。在 Service 层代码对参数进行运算过程中，Servlet处于阻塞状态，等待着运算结果。如果在Service层代码运算过程中，将Servlet线程释放回Servlet线程池，当 Service运算结果出来后，再给出用户响应，这样 Servlet 线程就不会被长时间占用了。而Servlet3.0 的异步处理,即使用`异步Servlet与异步子线程`所完成的异步处理**，解决的就是这个问题所以每一个请求最好不要长时间占有Servlet线程。**

一个被占用的Servlet线程对象，什么时候会被释放呢？当服务器给出客户端响应后马上就会释放与该请求相关联的Servlet线程，即将Servlet线程放回到Servlet 线程池，然后，销毁请求request与响应response对象。**所以对于耗时的操作，我们可以使用异步Servlet，让该Servlet只负责启动这个耗时运算线程即可。该Servlet完成启动任务后即可被释放，即该请求不再占用 Servlet线程。**

## Servlet 异步的实现
从 Servlet3.0开始，Servlet可以进行异步处理。所谓Servlet的异步处理，是指当一个具有耗时算的请求到达服务器后，Servlet仍会创建子线程去完成耗时运算，但Servlet不会等待子线程运算完毕后，再将该 Servlet线程释放，而是仅仅让当前 Servlet 完成对子线程的启动任务后直接释放，返回到Servlet线程池，让其它请求来使用。
1. 定义异步线程
```java
public class ComputerThread implements Runnable{
	private AsyncContext async;
	
	public ComputerThread(AsyncContext async) {
		super();
		this.async = async;
	}

	int sum = 0;
	@Override
	public void run() {
		try {
			PrintWriter writer = async.getResponse().getWriter();
			writer.println("subthread start<br>");
			for(int i=0; i<10; i++) {
				System.out.println(i);
				sum = sum + i;
				Thread.sleep(1000);
			}
			writer.print(sum + "<br>");
			writer.println("subthread end<br>");
			// 通知主线程执行完毕 底层执行销毁异步线程
			async.complete();
		} catch (InterruptedException | IOException e) {
			e.printStackTrace();
		}
	}
}
```

2. 定义异步Servlet
```java
@WebServlet(value="/some", asyncSupported=true)
public class SomeServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		response.setContentType("text/html;charset=UTF-8");
		PrintWriter writer = response.getWriter();
		writer.println("main thread start <br>");
		
		// 获取异步上下文对象
		AsyncContext startAsync = request.startAsync();
		// 开启异步线程
		startAsync.start(new ComputerThread(startAsync));
		
		writer.print("main thread end <br>");
	}
}
```
运行结果
> main thread start 
 main thread end 
subthread start
 45
subthread end

仔细查看运行结果可以看出，Servlet主线程的执行与子线程的执行是异步完成的，而非同步完成：先输出的是 Servlet 主线程中的所有数据，说明 Servlet 主线程是直接运行结束的。

## 异步应用举例
Servlet的异步处理的应用，一般是将异步子线程的处理结果存放到`Session域`或`ServletContext域`中，让用户通过访问另一个指定页面来查看运算结果。
Servlet 的异步处理的最典型应用是：用户在某站点注册完毕后，需要到自己的邮箱中点击确认链接。下面的例子就是模拟这个需求的：当用户注册完毕后，提示用户进入邮箱查看注册结果。用户打开邮箱后，可以查看到注册结果。

1. 异步Servlet
```java
@WebServlet(value="/register", asyncSupported=true)
public class RegisterServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    public RegisterServlet() {
        super();
    }

	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		PrintWriter writer = response.getWriter();
		writer.println("main thread start!");
		
		// 获取异步上下文对象
		AsyncContext async = request.startAsync();
		
		// 设置超时
		async.setTimeout(2000);
		
		// 开启异步上下文对象
		async.start(new ComputeThread(async));
		
		writer.println("main thread end!");
		writer.println("please check your email");
	}
	
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		this.doPost(req, resp);
	}
}
```

2. 异步线程
```java
public class ComputeThread implements Runnable{
	private AsyncContext async;
	
	public ComputeThread(AsyncContext async) {
		super();
		this.async = async;
	}

	@Override
	public void run() {
		HttpServletRequest request = (HttpServletRequest) async.getRequest();
		HttpSession session = request.getSession();
		
		session.setAttribute("test", "test");
		
		// 开始耗时运算
		int sum = 0;
		try {
			for(int i=0; i<10; i++) {
				System.out.println(i);
				sum = sum + i;
				Thread.sleep(1000);
			}
			
			// 对运算结果进行分析
			String message = "sorry, your register is fail"; 
			if(sum == 45) {
				message = "bingo!";
			}
			
			session.setAttribute("message", message);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
}
```




  [1]: http://static.zybuluo.com/xiaocorn/j9ofjjm9we63vr5o77ml63xf/image_1cmhlbaqi1ce8qf21gjj74bupo19.png