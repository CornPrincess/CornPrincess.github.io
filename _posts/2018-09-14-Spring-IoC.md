---
layout: post
title:  "Spring-IoC"
date:   2018-09-04 12:05:31 +0800
categories: Spring
tags: Spring IoC
---

* content
{:toc}

## Spring
Spring是一个容器，管理着整个应用程序中所有的bean的生命周期和依赖关系。这个框架主要用于降低耦合度，**对于主业务逻辑之间的采用的是IoC,对于系统业务逻辑与主业务逻辑之间采用的是AOP，**是一个分层的一站式轻量级开源框架。

![Spring Framework Runtime][1] <br>
图里面每个最小级别方块对应一个jar包。

## IoC
控制反转（IOC，Inversion of Control），是一个概念，是一种思想。指将传统上由程序代码直接操控的对象**调用权交给容器**，通过容器来实现对象的装配和管理。**控制反转就是对对象控制权的转移，从程序代码本身反转到了外部容器。**

IoC是一个概念，是一种思想，其实现方式多种多样。当前比较流行的实现方式有两种：依赖注入和依赖查找。依赖注入方式应用更为广泛。

**依赖查找**：Dependency Lookup，DL，容器提供回调接口和上下文环境给组件，程序代码则需要提供具体的查找方式。比较典型的是**依赖于JNDI系统的查找。**
**依赖注入**：Dependency Injection，DI，程序代码不做定位查询，这些工作由容器自行完成。

依赖注入DI是指程序运行过程中，若需要调用另一个对象协助时，无须在代码中创建被调用者，而是依赖于外部容器，由外部容器创建后传递给程序。

Spring的依赖注入对调用者与被调用者几乎没有任何要求，完全支持POJO之间依赖关系的管理。

依赖注入是目前最优秀的解耦方式。依赖注入让Spring的Bean之间以配置文件的方式组织在一起，而不是以硬编码的方式耦合在一起的。

### IoC程序举例
1. 定义接口与实现类
```java
public interface ISomeService {
	void doSome();
}

public class SomeServiceImp implements ISomeService {

	@Override
	public void doSome() {
		System.out.println("do some");
	}	
}
```

2. 创建Spring配置文件`applicationContext.xml`文件约束在`%SPRING_HOME%\docs\spring-framework-reference\html\xsd-configuration.html`文件中。

3. 定义`<bean>`标签

4. 定义测试类
```java
public class MyTest {
	
	@Test
	public void test01() {
		ISomeService service = new SomeServiceImp();
		service.doSome();
	}
	
	@Test
	public void test2() {
		// 创建容器对象 在类src下找
		ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
		// 从容器中获取对象
		ISomeService service = (ISomeService) ac.getBean("myService");
		service.doSome();
	}
	
	@Test
	public void test3() {
		// 创建容器对象 在文件系统中找
		ApplicationContext ac = new FileSystemXmlApplicationContext("D:/applicationContext.xml");
		// 从容器中获取对象
		ISomeService service = (ISomeService) ac.getBean("myService");
		service.doSome();
	}
	
	
	@Test
	public void test4() {
		BeanFactory factory = new XmlBeanFactory(new ClassPathResource("applicationContext.xml"));
		BeanFactory factory2 = new XmlBeanFactory(new FileSystemResource("applicationContext.xml"));
		ISomeService service = (ISomeService) factory.getBean("myService");
		service.doSome();
	}
}
```

注意ApplicationContext与BeanFactory的区别：创建bean的时机不同。
1. ApplicationContext容器，**会在容器对象初始化时，将其中的所有对象一次性全部装配好。**以后代码中若要使用到这些对象，只需从内存中直接获取即可。执行效率较高。但占用内存。
 缺点：占用系统资源
 优点：响应速度快
2. BeanFactory容器，对容器中对象的装配与加载采用延迟加载策略**，即在第一次调用getBean()时，才真正装配该对象。**
缺点：相对响应速度慢
优点：不多占用系统资源




  [1]: http://static.zybuluo.com/xiaocorn/03s04akdyk8efl34i5cr6vuc/image_1cmi1rfug18gk13oq1he2sp718cop.png