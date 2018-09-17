---
layout: post
title:  "Spring-AOP"
date:   2018-09-15 14:05:31 +0800
categories: Spring
tags: Spring AOP
---

* content
{:toc}

## AOP简介
AOP（Aspect Orient Programming），面向切面编程，是面向对象编程OOP的一种补充。面向对象编程是从**静态角度**考虑程序的结构，而面向切面编程是从**动态角度**考虑程序运行过程。

AOP底层，就是采用动态代理模式实现的。采用了两种代理：**JDK的动态代理**，与**CGLIB的动态代理**。
关于代理模式可参考之前写的博客：[Java动态代理][1]

面向切面编程，就是将**交叉业务逻辑**封装成切面，利用AOP容器的功能将切面织入到主业务逻辑中。所谓交叉业务逻辑是指，通用的、与主业务逻辑无关的代码，如**安全检查、事务、日志**等。

### AOP编程术语
1. Aspect （切面）：切面泛指交叉业务逻辑。如事务处理、日志处理就可以理解为切面。常用的切面有Advice（通知）与Advisor（顾问）。实际就是对主业务逻辑的一种增强。
2. Weaving （织入）：织入是指将切面代码插入到目标对象的过程。
3. JoinPoint （连接点）：连接点指可以被切面织入的方法。通常业务接口中的方法均为连接点。
4. PintCut (切入点):切入点指切面具体织入的方法。
注意：被标记为**final**的方法是**不能作为连接点与切入点的。因为最终的是不能被修改的，不能被增强的。**
5. Target（目标对象）：目标对象指将要被增强的对象。即包含主业务逻辑的类的对象。
6. Advice （通知）：通知是切面的一种实现，可以完成简单织入功能（织入功能就是在这里完成的）**通知定义了增强代码切入到目标代码的时间点**，是目标方法执行之前执行，还是之后执行等。通知类型不同，切入时间不同。
**切入点定义切入的位置，通知定义切入的时间。**
7. Advisor（顾问）：顾问是切面的另一种实现，能够将通知以更为复杂的方式织入到目标对象中，是**将通知包装为更复杂切面的装配器。**

### AOP编程环境搭建
AOP是由AOP联盟提出的一种编程思想，提出的一套编程规范。而Spring是AOP这套规范的一种实现.所以，需要导入AOP联盟的规范（接口）包:`aopalliance-1.0.jar`及Spring对其的实现包:`spring-aop-4.2.1.RELEASE.jar`。

## Advice 通知
通知（Advice），切面的一种实现，可以完成简单织入功能（织入功能就是在这里完成的）。常用通知有：前置通知、后置通知、环绕通知、异常处理通知。

### 前置通知
定义前置通知，需要实现`MethodBeforeAdvice`接口。该接口中有一个方法`before()`，会在目标方法执行之前执行。
前置通知的特点：
1. 在目标方法执行之前先执行。
2. **不改变目标方法的执行流程**，前置通知代码不能阻止目标方法执行。
3. **不改变目标方法执行的结果。**

通知的用法举例：
1. 定义目标类
```java
// 目标类
public class SomeServiceImpl implements ISomeService{
	// 目标方法
	@Override
	public void doFirst() {
		System.out.println("do first");
	}

	// 目标方法
	@Override
	public void doSecond() {
		System.out.println("do second");
	}
}
```

2. 通知类是指，实现了相应通知类型接口的类。当前，实现了这些接口，就要实现这些接口中的方法，而这些方法的执行，则是根据不同类型的通知，其执行时机不同。
前置通知：在目标方法执行之前执行
后置通知：在目标方法执行之后执行
环绕通知：在目标方法执行之前与之后均执行
异常处理通知：在目标方法执行过程中，若发生指定异常，则执行通知中的方法。
这里定义前置通知类。
```java
// 定义前置通知
public class MyMethodBeforeAdvice implements MethodBeforeAdvice{
	// 织入的具体方法在这里完成
	/*
	 * method：业务方法
	 * args[]: 业务方法参数
	 * target:目标对象
	 */
	
	@Override
	public void before(Method method, Object[] args, Object target) throws Throwable {
		// 目标方法的增强代码
		System.out.println("执行前置通知方法");
	}
}
```

3. 注册目标类,在Spring配置文件中注册目标对象Bean
```xml
<!--  配置目标对象 -->
<bean id="someService" class="love.minmin.services.SomeServiceImpl" />
```

4. 注册通知切面,即在Spring配置文件中注册定义的通知对象Bean。
```xml
<!--  配置切面：通知 -->
    <bean id="MyMethodBeforeAdvice" class="love.minmin.services.MyMethodBeforeAdvice" />
```

5. 注册代理工厂Bean类对象ProxyFactoryBean
```xml
<!-- 配置代理 -->
	<bean id="serviceProxy"
		class="org.springframework.aop.framework.ProxyFactoryBean">
		<!-- 目标类 -->
		<property name="target" ref="someService"></property>
		<!-- <property name="targetName" value="someService"></property> -->

		<!-- 接口 -->
		<!-- 设置目标对象所实现的业务接口，要求给出接口的全既定性类名。 
			此属性可以不进行设置，因为打开ProxyFactoryBean的源码， 
			可以看到其有个自动检测目标类的所有接口属性autodetectInterfaces， 默认值为true。
			即不设置也可以自动检测到。 当然，此时使用的是jdk的Proxy动态代理。 -->
		<property name="interfaces"
			value="love.minmin.services.ISomeService"></property>

		<!-- 切面 -->
		<property name="interceptorNames"
			value="MyMethodBeforeAdvice"></property>
	</bean>
```

6. 客户端访问动态代理对象
```java
@Test
public void test01() {
	String resource = "/love/minmin/services/applicationContext.xml";
	ApplicationContext ac = new ClassPathXmlApplicationContext(resource);
	
	// 客户端访问的是动态代理对象，而非原目标对象。因为代理对象可以将交叉业务逻辑按照通知类型，动态的织入到目标对象的执行中。
	ISomeService service = (ISomeService) ac.getBean("serviceProxy");
	service.doFirst();
	System.out.println("===============");
	service.doSecond();
}
```
对于测试类，需要注意，从容器中获取的是代理对象，而非目标对象。

### 后置通知
定义后置通知，需要实现接口`AfterReturningAdvice`。该接口中有一个方法`afterReturning()`，会在目标方法执行之后执行。
后置通知的特点：
1. 在目标方法执行之后执行。
2. 不改变目标方法的执行流程，后置通知代码不能阻止目标方法执行。
3. 不改变目标方法执行的结果。

代码举例：
1.将doSecond()方法返回值改为String类型
```java
// 目标类
public class SomeServiceImpl implements ISomeService{
	// 目标方法
	@Override
	public void doFirst() {
		System.out.println("do first");
	}

	// 目标方法
	@Override
	public String doSecond() {
		System.out.println("do second");
		return "abc";
	}
}
```

2. 定义后置通知切面
```java
// 后置通知，可以获取到目标方法的返回结果，但是无法改变目标方法的结果
public class MyAfterReturningAdvice implements AfterReturningAdvice{
	
	// 在目标方法执行之后执行
	// returnValue：目标方法执行结果
	@Override
	public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
		if(returnValue != null) {
			returnValue = returnValue.toString().toUpperCase();
		}
		System.out.println("执行后置通知： " + returnValue);
	}
}
```

3. 修改xml文件相关配置

### 环绕通知MethodInterceptor
定义环绕通知，需要实现MethodInterceptor接口。环绕通知，也叫方法拦截器，可以在目标方法调用之前及之后做处理，**可以改变目标方法的返回值，也可以改变程序执行流程。**

部分代码：
```java
public class MyMethodInterceptor implements MethodInterceptor{

	@Override
	public Object invoke(MethodInvocation invocation) throws Throwable {
		System.out.println("环绕执行通知： 目标执行方法之前");
		
		// 执行目标方法
		System.out.println("环绕执行通知： 目标执行方法之后");
		
		Object result = invocation.proceed();
		if(result != null) {
			result = result.toString().toUpperCase();
		}
		return result;
	}
}
```

### 异常通知ThrowsAdvice
定义异常通知，需要实现ThrowsAdvice接口。该接口的主要作用是，在目标方法抛出异常后，根据异常的不同做出相应的处理。当该接口处理完异常后，会简单地将异常再次抛出给目标方法。

不过，这个接口较为特殊，从形式上看，该接口中没有必须要实现的方法。但，这个接口却确实有必须要实现的方法afterThrowing()。这个方法重载了四种形式。由于使用时，一般只使用其中一种，若要都定义到接口中，则势必要使程序员在使用时必须要实现这四个方法。这是很麻烦的。**所以就将该接口定义为了标识接口（没有方法的接口）。**
在打开ThrowsAdvice源码后，上侧的注释部分可以看到四个方法， 但是最常用的时这个方法：
`public void afterThrowing(自定义的异常类 e)`

这里的参数e为，与具体业务相关的用户自定义的异常类对象。容器会根据异常类型的不同，自动选择不同的该方法执行。**这些方法的执行是在目标方法执行结束后执行的。**

代码举例：
1. 定义异常类的父类
```java
/*
 * 异常分类：
 * 1.运行时异常， 不进行处理也可以通过编译
 * 	继承自RuntimeException
 * 2.编译时异常： checked exception 不进行处理，无法通过编译
 * 继承自Exception
 */
public class UserException extends Exception{

	public UserException() {
		super();
	}

	public UserException(String message) {
		super(message);
	}
}
```

2. 定义异常类的子类
```java
/*
 * 异常分类：
 * 1.运行时异常， 不进行处理也可以通过编译
 * 	继承自RuntimeException
 * 2.编译时异常： checked exception 不进行处理，无法通过编译
 * 继承自Exception
 */
public class UserException extends Exception{

	public UserException() {
		super();
	}

	public UserException(String message) {
		super(message);
	}
}

public class PasswordException extends UserException{

	public PasswordException() {
		super();
	}

	public PasswordException(String message) {
		super(message);
	}
}
```

3. 定义业务接口。要抛出异常父类。
```java
// 业务接口
public interface ISomeService {
	// 业务方法
	boolean login(String name, String password) throws UserException;
}
```

4. 定义目标类。
```java
// 目标类
public class SomeServiceImpl implements ISomeService{

	@Override
	public boolean login(String name, String password) throws UserException {
		if(! "minmin".equals(name)) {
			throw new UserNameException("name wrong");
		}
		
		if(! "love".equals(password)) {
			throw new PasswordException("password wrong");
		}
		
		double a = 3/0;
		
		return true;
	}
}
```

5. 定义异常通知
```java
public class MyThrowsAdvice implements ThrowsAdvice{
	
	// 当目标方法抛出与指定类型的异常具有"is a"关系的异常时执行。
	public void afterThrowing(UserNameException ex) {
		System.out.println("name exception: " + ex.getMessage());
	}
	
	// 当目标方法抛出与指定类型的异常具有"is a"关系的异常时执行。
	public void afterThrowing(PasswordException ex) {
		System.out.println("password exception: " + ex.getMessage());
	}
	
	// 当目标方法抛出与指定类型的异常具有"is a"关系的异常时执行。
	public void afterThrowing(Exception ex) {
		System.out.println("exception: " + ex.getMessage());
	}
}
```

6. 配置xml文件并测试
```java
public class MyTest {
	
	@Test
	public void test01() throws UserException {
		String resource = "/love/minmin/aop04/applicationContext.xml";
		ApplicationContext ac = new ClassPathXmlApplicationContext(resource);
		
		ISomeService service = (ISomeService) ac.getBean("serviceProxy");
		
		service.login("minmin", "love");
		
		((ClassPathXmlApplicationContext) ac).close();
	}
}
```

### 通知其他方法
1. 若要给目标方法织入多个切面，则需要在配置代理对象的切面（interceptorNames）属性时，设定为list或者array，也可以使用简便写法，如下：
```xml
<property name="interceptorNames"
			value="MyAfterReturningAdvice, MyMethodBeforeAdvice">
			<!-- <list>
				<value>MyAfterReturningAdvice</value>
				<value>MyMethodBeforeAdvice</value>
			</list> -->
		</property>
```
 2. 若ProxyFactoryBean配置中target没有实现接口，则会自动采用cglib的方式进行动态代理

3. 若target实现了接口，但是想要使用cglib进行动态代理，可以在ProxyFactoryBean的配置中添加`proxyTargetClass`属性，将其设为true，或者指定`optimize`属性为true。


## Advisor 顾问
通知（Advice）是Spring提供的一种切面（Aspect）。但其功能过于简单：**只能将切面织入到目标类的所有目标方法中，无法完成将切面织入到指定目标方法中。**

顾问（Advisor）是Spring提供的另一种切面。其可以完成更为复杂的切面织入功能。PointcutAdvisor是顾问的一种，可以指定具体的切入点。顾问将通知进行了包装，会根据不同的通知类型，在不同的时间点，将切面织入到不同的切入点。

### 名称匹配方法切入点顾问
NameMatchMethodPointcutAdvisor，即名称匹配方法切入点顾问。容器可根据配置文件中指定的方法名来设置切入点。
其中对于切入点的指定可以使用通配符。

![名称匹配方法切入点顾问][2]

### 正则表达式方法切入点顾问
RegexpMethodPointcutAdvisor，即正则表达式方法顾问。容器可根据正则表达式来设置切入点。注意，与正则表达式进行匹配的对象是接口中的方法名，而非目标类（接口的实现类）的方法名，并且为全限定方法名。

[正则表达式][3]

## 自动代理生成器
前面代码中所使用的代理对象，均是由`ProxyFactoryBean`代理工具类生成的。而该代理工具类存在着如下缺点：
1. 一个代理对象只能代理一个Bean，即如果有两个Bean同时都要织入同一个切面，这时，不仅要配置这两个Bean，即两个目标对象，同时还要配置两个代理对象。
2. 在客户类中获取Bean时，使用的是代理类的id，而非我们定义的目标对象Bean的id。我们真正想要执行的应该是目标对象。从形式上看，不符合正常的逻辑。

Spring提供了自动代理生成器，用于解决`ProxyFactoryBean`的问题。常用的自动代理生成器有两个：
1. 默认advisor自动代理生成器
2. Bean名称自动代理生成器

需要注意的是，自动代理生成器均继承自Bean后处理器`BeanPostProcessor`。容器中所有Bean在初始化时均会自动执行Bean后处理器中的方法，故其无需id属性。所以自动代理生成器的Bean也没有id属性，客户类直接使用目标对象bean的id。

DefaultAdvisorAutoProxyCreator代理的生成方式是，将所有的目标对象与`Advisor`自动结合，生成代理对象。无需给生成器做任何的注入配置。注意，只能与`Advisor`配合使用。

相关xml配置：
```xml
<!-- 配置目标对象 -->
	<bean id="someService"
		class="love.minmin.aop02.SomeServiceImpl" />

	<!-- 配置切面：通知 -->
	<bean id="MyMethodBeforeAdvice"
		class="love.minmin.aop02.MyMethodBeforeAdvice" />
		
	<!-- 配置切面：顾问 -->
	<bean id="myAdvisor"
		class="org.springframework.aop.support.NameMatchMethodPointcutAdvisor">
		<property name="advice" ref="MyMethodBeforeAdvice"></property>
		<property name="mappedNames" value="doFirst"></property>
	</bean>

	<!-- 配置自动代理 -->
	<bean class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator" />
```

### Bean名称自动代理生成器
DefaultAdvisorAutoProxyCreator存在两个问题
1. 会为每一个目标对象织入所有匹配的Advisor，不具有选择性，不能选择advisor和目标对象
2. 切面只能是顾问Advisor。

BeanNameAutoProxyCreator的代理生成方式是，根据bean的id，来为符合相应名称的类生成相应代理对象，**且切面既可以是顾问Advisor又可以是通知Advice。**

相关xml配置：
```xml
<!-- 配置自动代理 -->
	<bean class="org.springframework.aop.framework.autoproxy.BeanNameAutoProxyCreator">
		<property name="beanNames" value="someService"></property>
		<property name="interceptorNames" value="myAdvisor"></property>
	</bean>
```
beanNames：指定要增强的目标类的id
interceptorNames：指定切面。可以是顾问Advisor，也可以是通知Advice。

## AspectJ对AOP的实现
对于AOP这种编程思想，很多框架都进行了实现。Spring就是其中之一，可以完成面向切面编程。然而，AspectJ是另一个框架，也实现了AOP的功能，且其实现方式更为简捷，使用更为方便，而且还支持注解式开发。所以，Spring又将AspectJ的对于AOP的实现也引入到了自己的框架中。

AspectJ中常用的通知有五种类型：
（1）前置通知
（2）后置通知
（3）环绕通知
（4）异常通知
（5）最终通知
**其中最终通知是指，无论程序执行是否正常，该通知都会执行。**类似于try..catch中的finally代码块。

### AspectJ基于注解的AOP实现
实现步骤：
1.定义业务接口与实现类
```java
// 业务接口
public interface ISomeService {
	// 业务方法
	void doFirst();
	// 业务方法
	String doSecond();
	// 业务方法
	String doThird();
	// 业务方法
	String doForth();
}

// 目标类
public class SomeServiceImpl implements ISomeService{
	// 目标方法
	@Override
	public void doFirst() {
		System.out.println("do first");
	}

	// 目标方法
	@Override
	public String doSecond() {
		System.out.println("do second");
		return "adc";
	}
	
	// 目标方法
	@Override
	public String doThird() {
		return "abc";
	}

	@Override
	public String doForth() {
		int a = 3/0;
		return null;
	}
}
```

2.定义切面POJO类,其中定义了若干普通方法，将作为不同的通知方法,作为切面出现。
``` java
@Aspect
public class MyAspect {
	@Before("execution(* *..ISomeService.doFirst())")
	public void beforeSome() {
		System.out.println("前置增强");
	}
	
	
	@Before("execution(* *..ISomeService.doFirst())")
	public void beforeSome(JoinPoint jp) {
		System.out.println("前置增强 切入点表达式： " + jp);
		System.out.println("前置增强 方法签名： " + jp.getSignature());
		System.out.println("前置增强 目标对象： " + jp.getTarget());
	}
	
	@AfterReturning(value="execution(* *..ISomeService.doSecond())", returning = "result")
	public void AfterSome(Object result) {
		System.out.println("后置增强");
		System.out.println(result);
	}
	
	@Around("execution(* *..ISomeService.doThird())")
	public Object myAround(ProceedingJoinPoint pjp) throws Throwable {
		System.out.println("环绕通知之前");
		
		Object result = pjp.proceed();
		System.out.println("环绕通知之后");
		System.out.println(result);
		if(result != null) {
			result = result.toString().toUpperCase();
		}
		
		return result;
	}
	
	@AfterThrowing(value="execution(* *..ISomeService.doForth())", throwing = "e")
	public void afterAThrowing(Throwable e) {
		System.out.println("异常通知： " + e.getMessage());
	}
	
	@After("myPointcut()")
	public void after() {
		System.out.println("最终方法");
	}
	
	// 定义一个PointCut，代表方法myPointcut
	@Pointcut("execution(* *..ISomeService.doForth())")
	private void myPointcut() {}
}
```

3.注册目标对象与POJO切面类
```xml
<!--  配置目标对象 -->
	<bean id="someService" class="love.minmin.aop01.SomeServiceImpl" />
	
	<!--  配置切面 -->
	<bean id="myAspect" class="love.minmin.aop01.MyAspect" />
```

4.注册AspectJ的自动代理
在定义好切面Aspect后，需要通知Spring容器，让容器生成“目标类 + 切面”的代理对象。这个代理是由容器自动生成的。只需要在Spring配置文件中注册一个基于aspectj的自动代理生成器，其就会自动扫描到@Aspect注解，并按通知类型与切入点，将其织入，并生成代理。

```xml
<!--  配置aspectj自动代理 -->
	<aop:aspectj-autoproxy />
```
`<aop:aspectj-autoproxy/>`的底层是由AnnotationAwareAspectJAutoProxyCreator实现的。从其类名就可看出，是基于AspectJ的注解适配自动代理生成器。

其工作原理是，<aop:aspectj-autoproxy/>通过扫描找到@Aspect定义的切面类，再由切面类根据切入点找到目标类的目标方法，再由通知类型找到切入的时间点。

### AspectJ基于XML的AOP实现
切面就是一个POJO类，而用于增强的方法就是普通的方法。通过配置文件，将切面中的功能增强织入到了目标类的目标方法中。

基于xml的AOP实现，去除了POJO类的注解，改而在xml文件中进行配置，这里记录xml配置的改变，其他文件同上文一样。

```xml
<!--  配置目标对象 -->
	<bean id="someService" class="love.minmin.aop02.xml.SomeServiceImpl" />
	
	<!--  配置切面 -->
	<bean id="myAspect" class="love.minmin.aop02.xml.MyAspect" />
	
	<!--  配置aop-->
	<aop:config>
		<!-- 定义切入点 -->
		<aop:pointcut expression="execution(* *..ISomeService.doFirst())" id="mydoFirstPointCut"/>
		<aop:pointcut expression="execution(* *..ISomeService.doSecond())" id="mydoSecondPointCut"/>
		<aop:pointcut expression="execution(* *..ISomeService.doThird())" id="mydoThirdPointCut"/>
		<aop:pointcut expression="execution(* *..ISomeService.doForth())" id="mydoForthPointCut"/>
	
		<!-- 定义切面 -->
		<aop:aspect ref="myAspect">
			<aop:before method="beforeSome" pointcut-ref="mydoFirstPointCut"/>
			<aop:before method="beforeSome(org.aspectj.lang.JoinPoint)" pointcut-ref="mydoFirstPointCut"/>
			<aop:after-returning method="AfterSome" pointcut-ref="mydoSecondPointCut" returning="result"/>
			<aop:around method="myAround" pointcut-ref="mydoThirdPointCut"/>
			<aop:after-throwing method="afterAThrowing" pointcut-ref="mydoForthPointCut" throwing="e"/>
			<aop:after method="after" pointcut-ref="mydoForthPointCut"/>
		</aop:aspect>
	</aop:config>
```


  [1]: https://cornprincess.github.io/2018/09/13/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F-%E4%BB%A3%E7%90%86%E6%A8%A1%E5%BC%8F/#%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86
  [2]: http://static.zybuluo.com/xiaocorn/4sfq927y7lisbndtqq49dlqv/image_1cngue1dm1kln1opc9ad16g3jvq9.png
  [3]: http://www.runoob.com/java/java-regular-expressions.html