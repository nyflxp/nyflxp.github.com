---
layout: post
title: Spring框架学习笔记
description: Spring是一个开源框架,它是为了解决企业应用开发的复杂性而创建的。Spring使用基本的JavaBean来完成以前只可能由EJB完成的事情。然而，Spring的用途不仅限于服务器端的开发。从简单性、可测试性和松耦合的角度而言，任何Java应用都可以从Spring中受益。
category: blog
---

# [{{ page.title }}][1]
2012-09-20 By {{ site.author_info }}

##spring简介

Spring是一个轻量级的控制反转(IoC)和面向切面(AOP)的容器框架。

* 轻量——从大小与开销两方面而言Spring都是轻量的。完整的Spring框架可以在一个大小只有1MB多的JAR文件里发布。并且Spring所需的处理开销也是微不足道的。此外，Spring是非侵入式的：典型地，Spring应用中的对象不依赖于Spring的特定类。
* 控制反转——Spring通过一种称作控制反转（IoC）的技术促进了松耦合。当应用了IoC，一个对象依赖的其它对象会通过被动的方式传递进来，而不是这个对象自己创建或者查找依赖对象。你可以认为IoC与JNDI相反——不是对象从容器中查找依赖，而是容器在对象初始化时不等对象请求就主动将依赖传递给它。
* 面向切面——Spring提供了面向切面编程的丰富支持，允许通过分离应用的业务逻辑与系统级服务（例如审计（auditing）和事务（transaction）管理）进行内聚性的开发。应用对象只实现它们应该做的——完成业务逻辑——仅此而已。它们并不负责（甚至是意识）其它的系统级关注点，例如日志或事务支持。
* 容器——Spring包含并管理应用对象的配置和生命周期，在这个意义上它是一种容器，你可以配置你的每个bean如何被创建——基于一个可配置原型（prototype），你的bean可以创建一个单独的实例或者每次需要时都生成一个新的实例——以及它们是如何相互关联的。然而，Spring不应该被混同于传统的重量级的EJB容器，它们经常是庞大与笨重的，难以使用。
* 框架——Spring可以将简单的组件配置、组合成为复杂的应用。在Spring中，应用对象被声明式地组合，典型地是在一个XML文件里。Spring也提供了很多基础功能（事务管理、持久化框架集成等等），将应用逻辑的开发留给了你。

<img width="400px" src="http://www.javawind.net/help/html/spring_ref_2.0/html/images/spring-overview.png"/>

##Spring管理Bean

<img src="http://static.springframework.org/images/spring2-bean-container-magic.png" width="400px;"/>

* 配置applicationContext.xml

  	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans-2.5.xsd">
		<bean id="user" class="com.bean.pojo.User"></bean>
	</beans>

class是对象的完全限定名，id是对象的名字（首字母小写）

* 测试

在text.java里面进行测试：

	BeanFactory factory = new XmlBeanFactory (new ClassPathResource("applicationContext.xml"));
	User user = (User) factory.getBean("user"); 
	user.sayHello();

这个BeanFactory现在已经不用了，但是我们也是要知道的，我们现在用的是ApplicationContext

	ApplicationContext ctx =new ClassPathXmlApplicationContext("applicationContext.xml");
	User usre = (User) ctx.getBean("user");
	usre.sayHello();

相对于BeanFactory，现在的ApplicationContext具有一些特性：

* 1.提供了文本解析工具，包括 I18N 国际化；
* 2.提供了载入文件资源的通用方法；
* 3.可以向注册为监听器的 Bean发送事件。

但是他们还是有区别的：

* BeanFactory延迟加载所有的 Bean，直到 getBean()方法被调用的时候 Bean才会被创建。
* ApplicationContext则会预载入单例 Bean，当调用 getBean()方法的时候， Bean已经被创建成功。	
	
* 配置Bean

	<bean id="user" class="com.bean.pojo.User"/> 
	<bean name="myUser" class="com.bean.pojo.User"/> 
	<bean class="com.bean.pojo.User"/>

id属性，name属性都是用来指定bean标识符。id 属性具有唯一性，并且是xml中真正的id属性，受到 xml解析器的验证功能，在使用中必须和java 中命名变量一样去命名 id的值，比如不能以数字开始等约束。但是 name 属性值则没有要求，如果愿意，可以赋给的值为“123”等，name 属性还可以使用”,”号来分割多个为该 bean指定的标识（别名）。name 属性并不是必须的，当不写 name 属性时，spring 容器会自动生成 name 属性值。那么在获取该 bean的时候，name 属性和 class 属性值相同。
	
<img src="http://jaypeeonline.net/images/blog_update.png" title="持续更新中...."/>
	
	
[nyflxp]:    http://nieyafei.tk  "nyflxp"
[2]: http://www.freehao123.com/dl-railsinstaller "RailsInstaller"
[1]:    {{ page.url}}  ({{ page.title }})