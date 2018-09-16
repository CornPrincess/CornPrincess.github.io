---
layout: post
title:  "Spring-基于xml的DI"
date:   2018-09-15 14:05:31 +0800
categories: Spring
tags: Spring IoC 基于xml的DI
---

* content
{:toc}

## 注入分类
Bean实例在**调用无参构造器**创建了空值对象后，就要对Bean对象的属性进行初始化。**初始化是由容器自动完成的，称为注入。根据注入方式的不同，常用的有两类：设值注入、构造注入。**

### 设值注入
设值注入是指，通过setter方法传入被调用者的实例。这种注入方式简单、直观，因而在Spring的依赖注入中大量使用。

代码：
```java
// 定义学生类
public class Student {
	private int age;
	private String name;
	private School school; // 对象属性，域属性
	
	public void setName(String name) {
		System.out.println("执行setName");
		this.name = name;
	}

	public void setAge(int age) {
		System.out.println("执行setAge");
		this.age = age;
	}

	public void setSchool(School school) {
		this.school = school;
	}

	@Override
	public String toString() {
		return "Student [age=" + age + ", name=" + name + ", school=" + school + "]";
	}
}

// 定义学校类
public class School {
	private String name;

	public void setName(String name) {
		this.name = name;
	}

	@Override
	public String toString() {
		return "School [name=" + name + "]";
	}
}

// 注册xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- bean definitions here -->
    
    <!--  注册student -->
    <bean id="myStudent" class="love.minmin.di01.Student">
    	<property name = "name" value="minmin"></property>
    	<property name = "age" value="18"></property>
    	<property name = "school" ref="mySchool"></property>
    </bean>
    
    
    <!--  注册school -->
    <bean id="mySchool" class="love.minmin.di01.School">
    	<property name = "name" value="NNU"></property>
    </bean>

</beans>

// 测试
public class MyTest {
	@Test
	public void test01() {
		String resource = "/love/minmin/di01/applicationContext.xml";
		ApplicationContext ac = new ClassPathXmlApplicationContext(resource);
		
		Student student = (Student) ac.getBean("myStudent");
		System.out.println(student);
		
		((ClassPathXmlApplicationContext) ac).close();
	}
}
```

### 构造注入
构造注入是指，在构造调用者实例的同时，完成被调用者的实例化。**即使用构造器设置依赖关系。**

代码：
```java
// 定义学生类
public class Student {
	private int age;
	private String name;
	private School school; // 对象属性，域属性
	
	public Student(int age, String name, School school) {
		super();
		this.age = age;
		this.name = name;
		this.school = school;
	}

	@Override
	public String toString() {
		return "Student [age=" + age + ", name=" + name + ", school=" + school + "]";
	}
}

// 定义school类
public class School {
	private String name;

	public void setName(String name) {
		this.name = name;
	}

	@Override
	public String toString() {
		return "School [name=" + name + "]";
	}
}

// 定义xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- bean definitions here -->
    
    <!--  注册student -->
    <bean id="myStudent" class="love.minmin.di02.Student">
    	<constructor-arg name="name" value="minmin"></constructor-arg>
    	<constructor-arg name="age" value="18"></constructor-arg>
    	<constructor-arg name="school" ref="mySchool"></constructor-arg>
    </bean>
    
    <!--  注册school -->
    <bean id="mySchool" class="love.minmin.di02.School">
    	<property name = "name" value="NNU"></property>
    </bean>

</beans>
```
注意在Student中可以不用定义无参构造器，因为构造注入底层直接调用了带参构造器，而且也不用setter方法。但在实际情况中这种方法很少用，因为一个类的成员变量会有很多个，这个时候带参构造器就很难写。

