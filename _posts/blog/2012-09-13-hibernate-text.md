---
layout:     post
title:      Hibernate
category: blog
description: Hibernate是一个开放源代码的对象关系映射框架，它对JDBC进行了非常轻量级的对象封装，使得Java程序员可以随心所欲的使用对象编程思维来操纵数据库。 Hibernate可以应用在任何使用JDBC的场合，既可以在Java的客户端程序使用，也可以在Servlet/JSP的Web应用中使用，最具革命意义的是，Hibernate可以在应用EJB的J2EE架构中取代CMP，完成数据持久化的重任。
---
# [{{ page.title }}][1]
2012-09-14 By {{ site.author_info }}

##核心接口

Hibernate的核心接口一共有6个，分别为:Session、SessionFactory、Transaction、Query、Criteria和Configuration。这6个核心接口在任何开发中都会用到。通过这些接口，不仅可以对持久化对象进行存取，还能够进行事务控制。

<img src="http://stacktrace.in/wp-content/uploads/2012/06/Hibernate_logo_a1.png" width="500px" height="150"/>

##ORM(对象关系映射)

<img src="http://blogs.learnnowonline.com/Portals/153597/images/ORM.jpeg" width="365px" height="440">

##ORM框架

*[Hibernate][]
*[PenJpa][]
*[mybatis][]

优点：

*缺乏乏味的代码
*更加面向对象的设计
*更好的移植性
*更好的性能

##开始使用Hibernate

*1.加入Hibernate jar包
*2.创建持久化类（pojo）
*3.创建映射文件（XXX.hbm.xml）
*4.创建Hibernate配置文件（hibernate.cfg.xml）
*5.测试运行

导入[jar][]包之后,创建持久化类(pojo):

* pojo:Plain Ordinary Java Object(无格式的Java对象)
* Hibernate对pojo的要求：

– 属性要有对应的get和set方法

– 要有无参数的默认构造方法

– 不要使用final进行修饰

步骤：

* 创建一个类.class ,随后创建一个xml文件，最好命名和类的名字一样，比如User.class,User.hbm.xml
* 在xml文件中添加映射信息：

	<?xmlversion="1.0"encoding="UTF--‐8"?>
	<!DOCTYPEhibernate--‐mappingPUBLIC"--‐//Hibernate/HibernateMappingDTD3.0//EN""http://hibernate.sourceforge.net/hibernate--‐mapping--‐3.0.dtd">
	<hibernate--‐mapping package="com.kaishengit.pojo">
	  <class name="User" table="t_user">
			<id name="id" column="id">
				<generator class="native"></generator>
			</id>
			<property name="userName" column="username">
			</property>
			
			<property name="userPwd">
			</property>
		</class>
	</hibernate-mapping>

* 创建Hibernate配置文件（即连接数据库）：

	<?xml version="1.0" encoding="UTF--‐8"?>
	<!DOCTYPE hibernate--‐configuration PUBLIC "--‐//Hibernate/Hibernate Configuration DTD 3.0//EN" "http://hibernate.sourceforge.net/hibernate--‐configuration--‐3.0.dtd">
	<hibernate-configuration>
		<session-factory>
			//连接数据库
			<property name="connection.driver_class">com.mysql.jdbc.Driver</property>
			<property name="connection.url">jdbc:mysql:///mydb</property>
			<property name="connection.username">root</property>//用户名
			<property name="connection.password">root</property>//密码
			
			<!-- 方言 -->
			<property name="dialect">org.hibernate.dialect.MySQLDialect</property>
			
			<!-- C3P0 （数据pool的配置）-->
			<property name="hibernate.c3p0.max_size">2</property>
			<property name="hibernate.c3p0.min_size">2</property>
			<property name="hibernate.c3p0.timeout">5000</property>
			<property name="hibernate.c3p0.max_statements">100</property>
			<!-- 检查连接池中所有空闲连接的间隔时间，单位为秒（要注意的是MySQL的自动关闭空闲超过8小时连接的机制） -->
			<property name="hibernate.c3p0.idle_test_period">3000</property>
			<!--当连 接池中的连接耗尽的时候c3p0一次同时获取的连接数. --> 
			<property name="hibernate.c3p0.acquire_increment">2</property>
			<!-- 每次都验证连接是否可用 -->
			<property name="hibernate.c3p0.validate">false</property>
			
			<property name="hibernate.show_sql">true</property>//显示sql
			<property name="current_session_context_class">thread</property>//本地jdbc事务配置
			
			<mapping resource="com/kaishengit/pojo/user.hbm.xml"/>//连接类的ORM
			
		</session-factory>
	</hibernate-configuration>

* 运行测试：

运行Hibernate save：


	User user = new User();
	user.setUserName("fan");
	user.setUserPwd("123");
	Configuration cfg = new Configuration().configure();
	SessionFactory factory = cfg.buildSessionFactory();
	Session session = factory.getCurrentSession();
	Transaction tran = session.beginTransaction();
	session.save(user);
	tran.commit();


运行Hibernate load ，delete:

	User user =(User) session.load(User.class,1);
	session.delete(user);


运行Hibernate Query：


	Session session = factory.getCurrentSession();
	session.beginTransaction();
	List<User> userList =session.createQuery("fromUser").list();
	for(User user:userList){
	System.out.println(user.getUserName());
	}


运行Hibernate Update:


	Session session = factory.getCurrentSession();
	session.beginTransaction();
	User user = (User) session.load(User.class,2);
	user.setUserName("Alex");
	session.update(user);
	session.getTransaction().commit();


##Hibernate持久化对象的生命周期

###持久化对象的生命周期(状态)

* 瞬态（自由态）:

持久化对象的自由态，指的是对象在内存中存在，但是在数据库中并没有数据与其关联。比如Student student = new Student()，这里的student对象就是一个自由态的持久化对象。

* 持久态:

持久态指的是持久化对象处于由Hibernate管理的状态，这种状态下持久化对象的变化将会被同步到数据库中。

* 托管（游离态）:

处于持久态的对象，在其对应的Session实例关闭后，此时对象迚入游离态。也就是说Session实例是持久态对象的宿主环境，一旦宿主环境失效，那么持久态对象迚入游离状态。

###区别：

游离态和自由态的区别就在于游离态对象可以再次与Session迚行关联而成为持久态对象。

自由态对象在数据库中没有数据与其对应，但是游离态对象在数据库中有数据与其对应，只不过当前对象不在Session环境中而已。从对象的是否有主键值可以做简单的判断。

在Hibernate中根据主键判断对象是自由态还是游离态只是判断的一个参考点，在Hibernate中还有更复杂的机制来判断一个对象的状态，比如对象的version等等。

* 三种状态的转换：

<img src="http://p.blog.csdn.net/images/p_blog_csdn_net/lenotang/hibernate3_ch3_1.gif"/>

load和get方法：

* 1.get和load方法都是利用对象的主键值获取相应的对象，并可以使对象处于持久状态。
* 2.load方法获取对象时不会立即执行查询操作，而是在第一次使用对象是再去执行查询操作。如果查询的对象在数据库中不存在，load方法返回值不会为null，在第一次使用时抛出org.hibernate.ObjectNotFoundException异常。
* 3.使用get方法获取对象时会立即执行查询操作，并且对象在数据库中不存在时返回null值。

save和persist方法：

* 1.save和persist方法都是将持久化对象保存到数据库中
* 2.sava方法成功执行后，返回持久化对象的ID
* 3.persist方法成功执行后，不会返回持久化对象的ID，persist方法是JPA中推荐使用的方法

save和update方法：

* 1.save方法是将自由态的对象迚行保存。
* 2.update方法是将游离态的对象迚行保存。

update和saveOrUpdate方法：

* 1.如果一个对象是游离态戒持久态，对其执行update方法后会将对象的修改同步到数据库中，如果该对象是自由态，则执行update方法是没有作用的。
* 2.在执行saveOrUpdate方法时该方法会自动判断对象的状态，如果为自由态则执行save操作，如果为游离态戒持久态则执行update操作。

update和merge方法：

* 1.如果持久化对象在数据库中存在，使用merge操作时迚行同步操作。如果对象在数据库不存在，merge对象则迚行保存操作。
* 2.如果对象是游离状态，经过update操作后，对象转换为持久态。但是经过merge操作后，对象状态依然是游离态。

saveOrUpdate和merge方法：

* saveOrUpdate方法和merge方法的区别在于如果session中存在两个主键值相同的对象，迚行saveOrUpdate操作时会有异常抛出。这时必须使用merge迚行操作。

clear方法和flush方法：

* clear方法是将Session中对象全部清除，当前在Session中的对象由持久态转换为游离态。flush方法则是将持久态对象的更改同步到数据库中。

<img src="http://jaypeeonline.net/images/blog_update.png" title="持续更新中...."/>


[nyflxp] http://nieyafei.tk  "Nyflxp"
[1]:    {{ page.url}}  ({{ page.title }})
[jar]		http://www.hibernate.org/
[Hibernate] http://www.hibernate.org/
[PenJpa]	http://openjpa.apache.org/
[mybatis]	https://code.google.com/p/mybatis/