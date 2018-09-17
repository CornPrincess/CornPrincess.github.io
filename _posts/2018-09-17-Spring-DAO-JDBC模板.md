---
layout: post
title:  "Spring-DAO-JDBC模板"
date:   2018-09-17 11:32:31 +0800
categories: Spring
tags: Spring DAO JDBC模板
---

* content
{:toc}

Spring与Dao部分，是Spring的两大核心技术IoC与AOP的典型应用体现：对于JDBC模板的使用，是IoC的应用，是将JDBC模板对象注入给了Dao层的实现类。

### Spring与JDBC模板
为了避免直接使用JDBC而带来的复杂且冗长的代码，Spring提供了一个强有力的模板类---`JdbcTemplate`来简化JDBC操作。并且，数据源`DataSource`对象与模板`JdbcTemplate`对象均可通过Bean的形式定义在配置文件中，充分发挥了依赖注入的威力。

代码举例：
1.首先除了Spring的基本包以及MySql驱动以外，还需要导入Spring-jdbc.jar和Spring-tx.jar两个jar包。

2.定于实体类Student
```java
public class Student {
	private Integer id;
	private String name;
	private int age;
	
	public Student() {
		super();
	}
	
	public Student(String name, int age) {
		super();
		this.name = name;
		this.age = age;
	}
	
	// getter and setter
	// toString()
```

2.定义数据库及表
![student表][1]

3.定义IStudentDao
```java
public interface IStudentDao {
	void insertStudent(Student student);
	
	void deleteById(int id);
	
	void updateStudent(Student student);
	
	List<String> selectAllStudentsNames();
	String selectStudentNameById(int id);
	
	List<Student> selectAllStudents();
	Student selectStudentById(int id);
}
```

4.初步定义StudentDaoImpl
这里仅仅定义一个StudentDaoImpl类实现了IStudentDao接口，但不具体写每个方法的方法实现。保持默认即可。后面会逐个通过Jdbc模板来实现。
```java
public class StudentDaoImpl implements IStudentDao{

	@Override
	public void insertStudent(Student student) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void deleteById(int id) {
		// TODO Auto-generated method stub
		
	}
	
	// .....
}
```

5.定义IStudentService
```java
public interface IStudentService {
	void addStudent(Student student);
	
	void removeById(int id);
	
	void modifyStudent(Student student);
	
	List<String> findAllStudentsNames();
	String findStudentNameById(int id);
	
	List<Student> findAllStudents();
	Student findStudentById(int id);
}
```

6.定义UserService
```java
public class StudentServiceImpl implements IStudentService{
	private IStudentDao dao;
	
	public void setDao(IStudentDao dao) {
		this.dao = dao;
	}

	@Override
	public void addStudent(Student student) {
		dao.insertStudent(student);
	}

	@Override
	public void removeById(int id) {
		dao.deleteById(id);
	}
	
	// .....
}
```

7.定义测试类MyTest
```java
public class MyTest {
	
	private IStudentService service;

	@Before
	public void init() {
		// 初始化，创建容器对象并创建service对象
		String resource = "applicationContext.xml";
		ApplicationContext ac = new ClassPathXmlApplicationContext(resource);
		service = (IStudentService) ac.getBean("");
	}
	
	@Test
	public void test01() {
		Student student = new Student("小斌", 21);
		service.addStudent(student);
	}
	
	//...
```

### 数据源配置
简单的测试框架搭建好后就可以进行数据源的配置，数据源直接以Bean的形式配置在Spring配置文件中。根据数据源的不同，其配置方式不同。下面主要讲解三种常用数据源的配置方式。
#### Spring默认的数据源DriverManagerDataSource
Spring默认的数据源为DriverManagerDataSource，其有一个属性DriverClassName，用于接收DB驱动，相关xml文件配置如下：
```xml
<bean id="myDataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
		<property name="url" value="jdbc:mysql://127.0.0.1:3306/spring_jdbc_test"></property>
		<property name="username" value="root"></property>
		<property name="password" value="***"></property>
	</bean>
```

#### DBCP数据源BasicDataSource
DBCP，DataBase Connection Pool，是apache下的项目，使用该数据源，需要导入org.apache.commons目录中dbcp与pool两个jar包。
```xml
<!--  注册数据源：DBCP -->
	<bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource">
		<property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
		<property name="url" value="jdbc:mysql://127.0.0.1:3306/spring_jdbc_test"></property>
		<property name="username" value="root"></property>
		<property name="password" value="***"></property>
	</bean>
```

#### C3P0数据源ComboPooledDataSource
使用C3P0数据源，需要导入com.mchange.c3p0的jar包。

相关配置：
```xml
<!--  注册数据源：c3p0-->
	<bean id="myDataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="com.mysql.jdbc.Driver"></property>
		<property name="jdbcUrl" value="jdbc:mysql://127.0.0.1:3306/spring_jdbc_test"></property>
		<property name="user value="root"></property>
		<property name="password" value="***"></property>
	</bean>
```

注意，如果导入c3p0包版本为0.9.1.2，不会发生问题，但是如果是较其新的版本则要再导入mchange-commons包，不然会报错`java.lang.NoClassDefFoundError: com/mchange/v2/ser/Indirector`

#### 从属性文件读取数据库连接信息
为了方便数据库的配置，一般将数据库配置文件放在src下面

```xml
jdbc.driver=com.mysql.jdbc.mysql
jdbc.url=jdbc:mysql://127.0.0.1:3306/spring_jdbc_test
jdbc.user=root
jdbc.password=***
```

该属性文件若要被Spring配置文件读取，其必须在配置文件中进行注册。注册方式有两种：`<bean/>`方式`<context>`方式，如果使用context方式，需要在Spring配置文件头部加入context的约束

```xml
<!--  注册属性方式一 -->
    <!-- <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    	<property name="location" value="classpath:jdbc.properties"></property>
    </bean> -->
    
    <!--  注册属性方式二 -->
    <context:property-placeholder location="classpath:jdbc.properties" />
```

整体xml文件配置如下：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"> 		
    <!-- bean definitions here -->
    
    <!--  注册属性方式一 -->
    <!-- <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    	<property name="location" value="classpath:jdbc.properties"></property>
    </bean> -->
    
    <!--  注册属性方式二 -->
    <context:property-placeholder location="classpath:jdbc.properties" />
    
	
	<!--  注册数据源：c3p0-->
	<!-- <bean id="myDataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="com.mysql.jdbc.Driver"></property>
		<property name="jdbcurl" value="jdbc:mysql://127.0.0.1:3306/spring_jdbc_test"></property>
		<property name="user" value="root"></property>
		<property name="password" value="***"></property>
	</bean> -->
	
	<bean id="myDataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="${jdbc.driver}"></property>
		<property name="jdbcUrl" value="${jdbc.url}"></property>
		<property name="user" value="${jdbc.user}"></property>
		<property name="password" value="${jdbc.password}"></property>
	</bean>
	
	<!--  注册数据源：DBCP -->
	<!-- <bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource">
		<property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
		<property name="url" value="jdbc:mysql://127.0.0.1:3306/spring_jdbc_test"></property>
		<property name="username" value="root"></property>
		<property name="password" value="***"></property>
	</bean> -->
	
	<!--  注册数据源：Spring默认 -->
	<!-- <bean id="myDataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
		<property name="url" value="jdbc:mysql://127.0.0.1:3306/spring_jdbc_test"></property>
		<property name="username" value="root"></property>
		<property name="password" value="***"></property>
	</bean> -->
	
	<!--  注册jdbcTemplate -->
	<bean id="myJdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="myDataSource"></property>
	</bean>
	
	<!--  注册dao -->
	<bean id="studentDao" class="love.minmin.dao.StudentDaoImpl">
		<property name="jdbcTemplate" ref="myJdbcTemplate"></property>
	</bean>
	
	<!--  注册service -->
	<bean id="studentService" class="love.minmin.services.StudentServiceImpl">
		<property name="dao" ref="studentDao"></property>
	</bean>
	
</beans>
```

注意：
Dao实现类继承自JdbcDaoSupport类，而JdbcDaoSupport类中有一个属性JdbcTemplate，用于接收JDBC模板。所以Dao实现类继承了JdbcDaoSupport类后，也就具有了JDBC模板属性。在配置文件中，只要将模板对象注入即可，即上面关于dao的配置。
但是再仔细查看JdbcDaoSupport类，发现其有一个dataSource属性，查看setDataSource()方法体可知，若JDBC模板为null，则会自动创建一个模板对象。
```java
/**
	 * Set the JDBC DataSource to be used by this DAO.
	 */
	public final void setDataSource(DataSource dataSource) {
		if (this.jdbcTemplate == null || dataSource != this.jdbcTemplate.getDataSource()) {
			this.jdbcTemplate = createJdbcTemplate(dataSource);
			initTemplateConfig();
		}
	}
```

故，在Spring配置文件中，对于JDBC模板对象的配置完全可以省去，而是在Dao实现类中直接注入数据源对象。这样会让系统自动创建JDBC模板对象。
即dao的配置文件可改为：
```xml
<!--  注册dao二 -->
	<bean id="studentDao" class="love.minmin.dao.StudentDaoImpl">
		<property name="dataSource" ref="myDataSource"></property>
	</bean>
```

### 对DB的CRUD操作
JdbcTemplate类中提供了对DB进行修改、查询的方法。Dao实现类使用继承自JdbcDaoSupport的getTemplate()方法，可以获取到JDBC模板对象。

对DB的增、删、改都是通过update()方法实现的。该方法常用的重载方法有两个：
public int update ( String sql)
public int update ( String sql, Object… args)

```java
@Override
	public void insertStudent(Student student) {
		String sql = "insert into student(name, age) values (?, ?)";
		this.getJdbcTemplate().update(sql, student.getName(), student.getAge());
	}

	@Override
	public void deleteById(int id) {
		String sql = "delete from student where id=?";
		this.getJdbcTemplate().update(sql, id);
	}

	@Override
	public void updateStudent(Student student) {
		String sql = "update student set name=?, age=? where id=?";
		this.getJdbcTemplate().update(sql, student.getName(), student.getAge(), student.getId());
	}
```

JDBC模板的查询结果均是以对象的形式返回。根据返回对象类型的不同，可以将查询分为两类：简单对象查询，与自定义对象查询。

#### 简单对象查询
常用的简单对象查询方法有：查询结果为单个对象的queryForObject()与查询结果为List的queryForList()。

pubic T queryForObject (String sql, Class<T> type, Object... args)
pubic List<T> queryForList (String sql, Class<T> type, Object... args)

```java
@Override
	public List<String> selectAllStudentsNames() {
		String sql = "select name from student";
		return this.getJdbcTemplate().queryForList(sql, String.class);
	}

	@Override
	public String selectStudentNameById(int id) {
		String sql = "select name from student where id=?";
		return this.getJdbcTemplate().queryForObject(sql, String.class, id);
	}
```

#### 自定义对象查询
常用的自定义对象查询方法有：查询结果为单个对象的queryForObject()与查询结果为List的query()。

pubic T queryForObject (String sql, RowMapper<T> m , Object... args)
pubic List<T> query (String sql, RowMapper<T > m, Object... args)

实现RowMapper接口：
```java
public class StudentRowMapper implements RowMapper<Student>{
	// rs:当查询出总的结果集时，框架回自动遍历这个结果集，每一次遍历一次数据，
	// 都会存放在rs中，这里rs代表的时一行的数据，并非所有查询结果，换个角度来说，只要能执行到
	// 这个方法，rs就不会为空
	
	// 实现将查询结果中当前行的数据包装为一个指定对象。
	@Override
	public Student mapRow(ResultSet rs, int rowNum) throws SQLException {
		Student student = new Student();
		student.setName(rs.getString("name"));
		student.setAge(rs.getInt("age"));
		student.setId(rs.getInt("id"));
		return student;
	}
}
```

实现service中的方法：
```java
@Override
	public List<Student> selectAllStudents() {
		String sql = "select * from student";
		return this.getJdbcTemplate().query(sql, new StudentRowMapper());
	}

	@Override
	public Student selectStudentById(int id) {
		String sql = "select * from student where id=?";
		return this.getJdbcTemplate().queryForObject(sql, new StudentRowMapper(), id);
	}
```

注意：JdbcTemplate对象是多例的，即系统会为每一个使用模板对象的线程（方法）创建一个JdbcTemplate实例，并且在该线程（方法）结束时，自动释放JdbcTemplate实例。所以在每次使用JdbcTemplate对象时，都需要通过getJdbcTemplate()方法获取，所以代码不能时一下形式：
```java
public class StudentDaoImpl extends JdbcDaoSupport implements IStudentDao{
	
	private JdbcTemplate jt;
	
	public StudentDaoImpl() {
		jt = new JdbcTemplate();
	}

	@Override
	public void insertStudent(Student student) {
		String sql = "insert into student(name, age) values (?, ?)";
		jt.update(sql, student.getName(), student.getAge());
	}
```






  [1]: http://static.zybuluo.com/xiaocorn/71fyoaokp2vsw8og93f4d3u6/image_1cnj9lbcl192g15p1ilj31meno9.png