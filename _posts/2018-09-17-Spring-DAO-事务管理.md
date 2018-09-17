---
layout: post
title:  "Spring-DAO-事务管理"
date:   2018-09-17 19:32:31 +0800
categories: Spring
tags: Spring DAO 事务管理
---

* content
{:toc}

事务原本是数据库中的概念，在Dao层。但一般情况下，需要将事务提升到业务层，即Service层。这样做是为了能够使用事务的特性来管理具体的业务。在Spring中通常可以通过以下三种方式来实现对事务的管理：
（1）使用Spring的事务代理工厂管理事务
（2）使用Spring的事务注解管理事务
（3）使用AspectJ的AOP配置管理事务

### Spring的事务管理API
Spring的事务管理，主要用到两个事务相关的接口。

#### [PlatformTransactionManager][1]
事务管理器是PlatformTransactionManager接口对象。其主要用于完成事务的提交、回滚，及获取事务的状态信息。

PlatformTransactionManager接口有两个常用的实现类：
1. DataSourceTransactionManager：使用JDBC或iBatis 进行持久化数据时使用。
2. HibernateTransactionManager：使用Hibernate进行持久化数据时使用。

Spring事务的默认回滚方式是：**发生运行时异常时回滚，发生受查异常时提交。**不过，对于受查异常，程序员也可以手工设置其回滚方式。

#### [TransactionDefinition][2]
事务定义接口TransactionDefinition中定义了事务描述相关的三类常量：事务隔离级别、事务传播行为、事务默认超时时限，及对它们的操作。

##### 定义了五个事务隔离级别常量
1. DEFAULT：采用DB默认的事务隔离级别。MySql的默认为REPEATABLE_READ； Oracle默认为READ_COMMITTED。
2. READ_UNCOMMITTED：读未提交。未解决任何并发问题。
3. READ_COMMITTED：读已提交。解决脏读，存在不可重复读与幻读。
4. REPEATABLE_READ：可重复读。解决脏读、不可重复读，存在幻读
5. SERIALIZABLE：串行化。不存在并发问题。

##### 定义了七个事务传播行为常量
所谓事务传播行为是指，**处于不同事务中的方法在相互调用时，执行期间事务的维护情况。**如，A事务中的方法doSome()调用B事务中的方法doOther()，在调用执行期间事务的维护情况，就称为事务传播行为。事务传播行为是加在方法上的。

1. REQUIRED：**指定的方法必须在事务内执行。**若当前存在事务，就加入到当前事务中；若当前没有事务，则创建一个新事务。**这种传播行为是最常见的选择，也是Spring默认的事务传播行为。**
如该传播行为加在doOther()方法上。若doSome()方法在执行时就是在事务内的，则doOther()方法的执行也加入到该事务内执行。若doSome()方法没有在事务内执行，则doOther()方法会创建一个事务，并在其中执行。
2. SUPPORTS：指定的方法支持当前事务，但若当前没有事务，也可以以非事务方式执行。
3. MANDATORY：指定的方法必须在当前事务内执行，若当前没有事务，则直接抛出异常。
4. REQUIRES_NEW：总是新建一个事务，若当前存在事务，就将当前事务挂起，直到新事务执行完毕。
5. NOT_SUPPORTED：指定的方法不能在事务环境中执行，若当前存在事务，就将当前事务挂起。
6. NEVER：指定的方法不能在事务环境下执行，若当前存在事务，就直接抛出异常。
7. NESTED：指定的方法必须在事务内执行。若当前存在事务，则在嵌套事务内执行；若当前没有事务，则创建一个新事务。

##### 定义了默认事务超时时限
常量TIMEOUT_DEFAULT定义了事务底层默认的超时时限，及不支持事务超时时限设置的none值。
注意，事务的超时时限起作用的条件比较多，且超时的时间计算点较复杂。**所以，该值一般就使用默认值即可。**

### 程序举例环境搭建
本例要实现模拟购买股票。存在两个实体：银行账户Account与股票账户Stock。当要购买股票时，需要从Account中扣除相应金额的存款，然后在Stock中增加相应的股票数量。而在这个过程中，可能会抛出一个用户自定义的异常。异常的抛出，将会使两个操作回滚。

1.创建实体类Account与Stock
```java
public class Account {
	private Integer id;
	private String aname;
	private double balance;
	//...
}

public class Stock {
	private Integer id;
	private String sname;
	private int count;
	// ....
}
```

2.创建数据库表
```sql
CREATE TABLE `account` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `aname` varchar(45) DEFAULT NULL,
  `balance` double DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `stock` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `sname` varchar(45) DEFAULT NULL,
  `count` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

3.定义service
```java
public interface IBuyStockService {
	void openAccount(String aname, double money);
	
	void openStock(String sname, int amount);
	
	void buyStock(String aname, double money, String sname, int amount);
}

public class BuyStockServiceImpl implements IBuyStockService{
	private IAccountDao aDao;
	private IStockDao sDao;
	
	public void setaDao(IAccountDao aDao) {
		this.aDao = aDao;
	}

	public void setsDao(IStockDao sDao) {
		this.sDao = sDao;
	}

	@Override
	public void openAccount(String aname, double money) {
		aDao.insertAccount(aname, money);
	}

	@Override
	public void openStock(String sname, int amount) {
		sDao.insertStock(sname, amount);
	}

	@Override
	public void buyStock(String aname, double money, String sname, int amount) {
		boolean isBuy = true;
		aDao.updateAccount(aname, money, isBuy);
		sDao.updateStock(sname,amount, isBuy);
	}
}
```

4.定义dao
```java
public interface IAccountDao {

	void insertAccount(String aname, double money);

	void updateAccount(String aname, double money, boolean isBuy);

}

public class AccountDaoImp extends JdbcDaoSupport implements IAccountDao{

	@Override
	public void insertAccount(String aname, double money) {
		String sql = "insert into account(aname, balance) values(?,?) ";
		this.getJdbcTemplate().update(sql, aname, money);
	}

	@Override
	public void updateAccount(String aname, double money, boolean isBuy) {
		String sql = "update account set balance=balance+? where aname=?";
		if (isBuy) {
			sql = "update account set balance=balance-? where aname=?";
		} 
		
		this.getJdbcTemplate().update(sql, money, aname);
	}
}

public interface IStockDao {

	void insertStock(String sname, int amount);

	void updateStock(String sname, int amount, boolean isBuy);

}

public class StockDaoImpl extends JdbcDaoSupport implements IStockDao{

	@Override
	public void insertStock(String sname, int amount) {
		String sql = "insert into stock(sname, count) values(?,?)";
		this.getJdbcTemplate().update(sql,sname, amount);
	}

	@Override
	public void updateStock(String sname, int amount, boolean isBuy) {
		String sql = "update stock set count=count-? where sname=?";
		if(isBuy) {
			sql = "update stock set count=count+? where sname=?";
		}
		this.getJdbcTemplate().update(sql, amount, sname);
	}
}
```

5.配置xml文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"> 		
    <!-- bean definitions here -->
    
    <!--  注册属性方式二 -->
    <context:property-placeholder location="classpath:jdbc.properties" />
	
	<bean id="myDataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="${jdbc.driver}"></property>
		<property name="jdbcUrl" value="${jdbc.url}"></property>
		<property name="user" value="${jdbc.user}"></property>
		<property name="password" value="${jdbc.password}"></property>
	</bean>
		
	<!--  注册dao二 -->
	<bean id="accountDao" class="love.minmin.dao.AccountDaoImp">
		<property name="dataSource" ref="myDataSource"></property>
	</bean>
	
	<bean id="stockDao" class="love.minmin.dao.StockDaoImpl">
		<property name="dataSource" ref="myDataSource"></property>
	</bean>
	
	<!--  注册service -->
	<bean id="buystockService" class="love.minmin.services.BuyStockServiceImpl">
		<property name="aDao" ref="accountDao"></property>
		<property name="sDao" ref="stockDao"></property>
	</bean>
	
</beans>
```

6.定义测试类
```java
public class MyTest {
	
	private IBuyStockService service;

	@Before
	public void init() {
		// 初始化，创建容器对象并创建service对象
		String resource = "applicationContext.xml";
		ApplicationContext ac = new ClassPathXmlApplicationContext(resource);
		service = (IBuyStockService) ac.getBean("buystockService");
	}
	
	@Test
	public void test01() {
		service.openAccount("minmin", 100);
		service.openStock("love", 0);
	}
	
	@Test
	public void test02() {
		service.buyStock("minmin", 50, "love", 1);
	}
}
```

测试结果成功，现在需要给buyStock添加抛出异常，并实现在service层的事务回滚操作。

1.自定义异常
```java
public class BuyStockException extends Exception {

	public BuyStockException() {
		super();
	}

	public BuyStockException(String message) {
		super(message);
	}
}
```

2.修改buyStock方法：
```java
@Override
	public void buyStock(String aname, double money, String sname, int amount) throws BuyStockException {
		boolean isBuy = true;
		aDao.updateAccount(aname, money, isBuy);
		if (1 == 1) {
			throw new BuyStockException("购买股票异常");
		}
		sDao.updateStock(sname,amount, isBuy);
	}
```

Spring通常有三种方法实现事务管理

### 使用Spring的事务代理工厂管理事务
该方式是，需要为目标类，即Service的实现类创建事务代理。事务代理使用的类是TransactionProxyFactoryBean，需要导入：aop联盟，及Spring对AOP实现的Jar包。该类需要初始化如下一些属性：
（1）transactionManager：事务管理器
（2）target：目标对象，即Service实现类对象
（3）transactionAttributes：事务属性设置

添加相关xml配置：
```xml
<!-- 配置事务管理器 -->
	<bean id="myTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="myDataSource"></property>
	</bean>
	
	<!-- 配置service的事务切面处理 -->
	<bean id="myServiceProxy" class="org.springframework.transaction.interceptor.TransactionProxyFactoryBean">
		<property name="transactionManager" ref="myTransactionManager"></property>
		<property name="target" ref="buystockService"></property>
		<property name="transactionAttributes">
			<props>
			<prop key="open*">ISOLATION_DEFAULT, PROPAGATION_REQUIRED</prop>
			<!--  
			通过“-异常”方式，可使发生指定的异常时事务回滚；这是的异常通常是受查异常
			通过“+异常”方式，可使发生指定的异常时事务提交。这是的异常通常是运行时异常
			-->
			<prop key="buyStock">ISOLATION_DEFAULT, PROPAGATION_REQUIRED, -BuyStockException</prop>
			</props>	
		</property>
	</bean>
```

### 使用Spring的事务注解管理事务
通过@Transactional注解方式，也可将事务织入到相应方法中。而使用注解方式，只需在配置文件中加入一个tx标签，以告诉spring使用注解来完成事务的织入。该标签只需指定一个属性，事务管理器。

@Transactional的所有可选属性如下所示：
1. propagation：用于设置事务传播属性。该属性类型为Propagation枚举，默认值为Propagation.REQUIRED。
2. isolation：用于设置事务的隔离级别。该属性类型为Isolation枚举，默认值为Isolation.DEFAULT。
3. readOnly：用于设置该方法对数据库的操作是否是只读的。该属性为boolean，默认值为false。
 timeout：用于设置本操作与数据库连接的超时时限。单位为秒，类型为int，默认值为-1，即没有时限。
4. rollbackFor：指定需要回滚的异常类。类型为Class[]，默认值为空数组。当然，若只有一个异常类时，可以不使用数组。
5. rollbackForClassName：指定需要回滚的异常类类名。类型为String[]，默认值为空数组。当然，若只有一个异常类时，可以不使用数组。
6. noRollbackFor：指定不需要回滚的异常类。类型为Class[]，默认值为空数组。当然，若只有一个异常类时，可以不使用数组。
7. noRollbackForClassName：指定不需要回滚的异常类类名。类型为String[]，默认值为空数组。当然，若只有一个异常类时，可以不使用数组。

注意的是，@Transactional若用在方法上，只能用于**public**方法上。对于其他非public方法，如果加上了注解@Transactional，虽然Spring不会报错，**但不会将指定事务织入到该方法中。因为Spring会忽略掉所有非public方法上的@Transaction注解。**
若@Transaction注解在类上，则表示该类上所有的方法均将在执行时织入事务。

xml相关配置
```xml
<!-- 配置事务注解驱动-->
	<tx:annotation-driven transaction-manager="myTransactionManager" />
```

添加注解
```java
@Transactional(isolation=Isolation.DEFAULT, propagation=Propagation.REQUIRED, rollbackFor=BuyStockException.class)
	@Override
	public void buyStock(String aname, double money, String sname, int amount) throws BuyStockException {
		boolean isBuy = true;
		aDao.updateAccount(aname, money, isBuy);
		if (1 == 1) {
			throw new BuyStockException("购买股票异常");
		}
		sDao.updateStock(sname,amount, isBuy);
	}
```

### 使用AspectJ的AOP配置管理事务
使用XML配置事务代理的方式的不足是，每个目标类都需要配置事务代理。当目标类较多，配置文件会变得非常臃肿。使用XML配置顾问方式可以自动为每个符合切入点表达式的类生成事务代理。

这里使用Spring的AspectJ方式将事务进行的织入，所以，这里除了前面导入的aop的两个Jar包外，还需要两个Jar包：AspectJ的Jar包，及Spring整合AspectJ的Jar包。

将前面service方法中的Transaction注解删除，并将xml文件做如下配置：
```xml
<!-- 配置事务管理器 -->
	<bean id="myTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="myDataSource"></property>
	</bean>
	
	<!-- 配置事务通知 -->
	<tx:advice id="myAdvice" transaction-manager="myTransactionManager">
		<tx:attributes>
			<!-- 这里指定的是：为每一个连接点指定索要应用的事务属性 -->
			<tx:method name="open*" isolation="DEFAULT" propagation="REQUIRED"/>
			<tx:method name="buyStock" isolation="DEFAULT" propagation="REQUIRED" rollback-for="BuyStockException"/>
		</tx:attributes>
	</tx:advice>
	
	<!-- AOP配置 -->
	
	<aop:config>
		<!-- 这里指定的是切入点，指定切入点之后相关的连接点才生效，顾问=通知+切入点 -->
		<!-- execution表达式里必须明确指明services层，不能写成"execution(* *..*.*.*(..))" -->
		<!-- 这样会造成service层与dao层的循环调用-->
		<aop:pointcut expression="execution(* *..services.*.*(..))" id="myPointCut"/>
		
		<aop:advisor advice-ref="myAdvice" pointcut-ref="myPointCut"/>
	</aop:config>
```


  [1]: https://docs.spring.io/spring-framework/docs/current/javadoc-api/
  [2]: https://docs.spring.io/spring-framework/docs/current/javadoc-api/