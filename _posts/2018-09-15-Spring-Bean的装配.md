---
layout: post
title:  "Spring-Bean的装配"
date:   2018-09-15 10:05:31 +0800
categories: Spring
tags: Spring IoC Bean的装配
---

* content
{:toc}

## Bean的装配
Bean的装配，即Bean对象的创建。容器根据代码要求创建Bean对象后再传递给代码的过程，称为Bean的装配。

### 默认方式装配
代码通过getBean()方式从容器获取指定的Bean实例，容器首先会调用**Bean类的无参构造器，因为底层使用的是反射机制，反射中newInstance调用了无参构造器，若类中没有无参构造器则会报错**。创建空值的实例对象。
代码：
```
public interface ISomeService {
	void doSome();
}

public class SomeServiceImp implements ISomeService {
	private int age = 1;
	
	public SomeServiceImp() {
		System.out.println("constructor age: " + age);
	}
	
	public SomeServiceImp(int age) {
		super();
		this.age = age;
	}

	@Override
	public void doSome() {
		System.out.println("do some");
	}	
}

public class MyTest {
	
	@Test
	public void test01() {
		String resource = "/love/minmin/beanAssmble01/applicationContext.xml";
		ApplicationContext ac = new ClassPathXmlApplicationContext(resource);
		ISomeService service = (ISomeService) ac.getBean("myService");
		service.doSome();
	}
}
```

### 动态工厂Bean
有些时候，项目中需要通过工厂类来创建Bean实例，而不能像前面例子中似的，直接由Spring容器来装配Bean实例。使用工厂模式创建Bean实例，**就会使工厂类与要创建的Bean类耦合到一起。**

#### 将动态工厂Bean作为普通Bean使用
将动态工厂Bean作为普通Bean来使用是指，在配置文件中注册过动态工厂Bean后，测试类直接通过getBean()获取到工厂对象，再由工厂对象调用其相应方法创建相应的目标对象。配置文件中无需注册目标对象的Bean。因为目标对象的创建不由Spring容器来管理。

代码：
```java
// 定义工厂类
public class ServiceFactory {
	public ISomeService getSomeService() {
		return new SomeServiceImp(); 
	}
}

// 在applicationContext中配置工厂类
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- bean definitions here -->
    
    <!--  注册factory -->
    <bean id="myFactory" class="love.minmin.beanAssmble02.ServiceFactory" />

</beans>

// 测试
public class MyTest {
	
	@Test
	public void test01() {
		String resource = "/love/minmin/beanAssmble02/applicationContext.xml";
		ApplicationContext ac = new ClassPathXmlApplicationContext(resource);
		// 从Spring容器中获取factory
		ServiceFactory factory = (ServiceFactory) ac.getBean("myFactory");
		// 通过factory获取service
		ISomeService service = factory.getSomeService();
		service.doSome();
	}
}
```
这样做的缺点是，不仅工厂类与目标类耦合到了一起，测试类与工厂类也耦合到了一起。

#### 使用Spring的动态工厂Bean
Spring对于使用动态工厂来创建的Bean，有专门的属性定义。`factory-bean`指定相应的工厂Bean，由`factory-method`指定创建所用方法。此时配置文件中至少会有两个Bean的定义：工厂类的Bean，与工厂类所要创建的目标类Bean。而测试类中不再需要获取工厂Bean对象了，可以直接获取目标Bean对象。实现测试类与工厂类间的解耦。

代码实现：
```java
// 添加xml配置
<!--  注册factory -->
    <bean id="myFactory" class="love.minmin.beanAssmble02.ServiceFactory" />
    
<!--  注册service -->
<bean id="myService" factory-bean="myFactory" factory-method="getSomeService" />
    
// 修改测试类
@Test
	public void test2() {
		String resource = "/love/minmin/beanAssmble02/applicationContext.xml";
		ApplicationContext ac = new ClassPathXmlApplicationContext(resource);
		SomeServiceImp service = (SomeServiceImp) ac.getBean("myService");
		service.doSome();
	}
```

### 静态工厂Bean
静态工厂无需工厂实例，所以不再需要定义静态工厂`<bean/>`。而对于工厂所要创建的Bean，其不是由自己的类创建的，所以无需指定自己的类。但其是由工厂类创建的，所以需要指定所用工厂类。故class属性指定的是工厂类而非自己的类。当然，还需要通过factory-method属性指定工厂方法。

代码：
```java
// 定义静态工厂
public class ServiceFactory {
	public static ISomeService getSomeService() {
		return new SomeServiceImp(); 
	}
}

// 定义xmml文件
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- bean definitions here -->
    
    <!--  注册service -->
    <bean id="myService" class="love.minmin.beanAssmble03.ServiceFactory" factory-method="getSomeService" />

</beans>

// 定义测试类
public class MyTest {
	
	@Test
	public void test() {
		String resource = "/love/minmin/beanAssmble03/applicationContext.xml";
		ApplicationContext ac = new ClassPathXmlApplicationContext(resource);
		ISomeService service = (ISomeService) ac.getBean("myService");
		service.doSome();
	}
}
```

## 容器中Bean的作用域
当通过Spring容器创建一个Bean实例时，不仅可以完成Bean的实例化，还可以通过scope属性，为Bean指定特定的作用域。Spring支持5种作用域。
1. singleton：单态模式。即在整个Spring容器中，使用singleton定义的Bean将是单例的，只有一个实例。**默认为单态的。**
2. prototype：原型模式。即每次使用getBean方法获取的同一个`<bean />`的实例都是一个新的实例，容器初始化是不生成对象，在执行getBean时才创建对象，效果等同于BeanFactory。
3. request：对于每次HTTP请求，都将会产生一个不同的Bean实例。
5. global session：每个全局的HTTP session对应一个Bean实例。典型情况下，仅在使用portlet 集群时有效，多个Web应用共享一个session。一般应用中，global-session与session是等同的。



