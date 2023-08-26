---
title: Spring
date: 2021-06-08 01:22:20
tags: spring
categories: 码
---

学spring过程中的一些笔记。



<!--more-->

# Spring

## 说点废话

#### 什么是spring

是一个web开发过程中使用到的框架，有解耦，管理对象创建等功能

#### 为什么是spring

因为spring好用且已经统治了web开发框架的领域，spring全家桶能一键解决所有问题。能用spring全家桶解决的事为什么要用spring以外的框架来搭配spring呢。 

#### 叨几句

web框架以前主流是ssh即spring+struts2+hibernate，struts2的出现解决了servlet单例模式的缺陷（线程安全问题），将项目改为非单例模式，但非单例模式运行效率低占用内存大。springmvc是strust2之后出现的框架。它回归了单例模式同时给出了单例模式缺陷的解决方案。struts2被爆出了大量安全漏洞也是其被springmvc代替的原因之一。

web开发框架由ssh演变成了ssm即spring+springmvc+mybatis。hibernate和mybatis都是orm框架，主要应用于数据库访问层，埋个坑之后再写个orm的日志。 springcloud也是spring家族下的框架，他打赢了dubbo。最大的原因还是spring的生态。

#### 基本准备

##### 选用工具

idea 2021.1

maven 3.6.3

mysql 8.0

spring 5.3.7





## 1. IOC（Inversion of Control）

控制反转：对象的初始化和赋值等一系列操作，由我们控制转为了spring控制。ioc中核心的步骤是DI。

### DI（Dependency Injection）

依赖注入：（容器）将对象注入到项目的具体地方。原理是反射。

所谓的容器就是一个环境，就像把项目放进一个水缸里，水缸里的环境（水）是容器，水中有被声明的各种会在项目开发中用上的东西（xml文件里创建的Bean）。  这些可能被用上的东西就不再需要我们操心了，我们把可能用上的东西扔进水里的过程叫做创建Bean（在xml文件里创建）。而这个水缸里的水很神奇能代替我们管理那些在水中的东西（被创建的bean），这也我们就不用管了，这叫IOC（控制反转）。当项目需要用到水里的某个东西即要使用到某个Bean时，水缸里的水帮我们把那个东西（Bean）给找出来并将它在它被使用到的地方实例化，这个行为称为DI（注入）。有了水缸里神奇的水帮忙，我们就可以专注于代码逻辑，创建和管理对象之类的活交给水缸（容器）干去咯

### Bean配置

封装是java的核心思想之一，所谓的bean就是将项目中可能多次用到的东西一个个单独打包好的东西，即封装成JavaBean，用豆子来比喻打包好的东西。DI就是指水将Bean在项目Bean被使用到的地方创建出来，但这个水是个瞎子，他不知道水里都有些什么Bean，这样它没办法帮程序员管理这些Bean。所以告诉水缸里的水，水里都有些什么Bean（配置Bean）是第一步。

开始前先试试实例化容器，看看spring环境是否搭建完成

实例化容器：

```java
    @Test
    public void test1() {
        //加载spring配置文件
        AbstractApplicationContext app = new ClassPathXmlApplicationContext(new String[]{"applicationContext1.xml"});
    }
```

#### 配置方式（创建Bean？实例化Bean?）

这里有些没搞明白，我的理解是在Spring配置文件中配置Bean应该叫做创建Bean。

##### 构造方法创建

```xml
<bean id="userDaoImpl" class="com.Darkra1Zzz.dao.impl.UserDaoImpl"/>
```

若类的构造方法被重载过，要注意无参构造方法是否有写，没有的话需要在bean内注入参数，这里用的是构造方法注入参数的方式进行注入

```java
    private String a;
    private UserDaoImpl(String a){
        System.out.println("hhh");
        this.a=a;
    }
    @Override
    public String toString() {
        return "UserDaoImpl{" +
                "a='" + a + '\'' +
                '}';
    }
```

```xml
<bean id="userDaoImpl" class="com.Darkra1Zzz.dao.impl.UserDaoImpl">
    <constructor-arg value="aaa"/>
</bean>
```

```java
    @Test
    public void test1() {
        //加载spring配置文件
        AbstractApplicationContext app = new ClassPathXmlApplicationContext(new String[]{"applicationContext1.xml"});
        UserDaoImpl impl=(UserDaoImpl)app.getBean("userDaoImpl");
        System.out.println(impl);
        app.close();
    }
```

运行结果：

hhh
UserDaoImpl{a='aaa'}

##### 使用静态工厂方法创建

```xml
<bean id="personService" class="cn.itcast.service.OrderFactory" factory-method="createOrder"/>
```

```java
public class OrderFactory {
    public static OrderServiceBean createOrder(){
        return new OrderServiceBean();
    }
}
```

##### 使用实例工厂方法创建

```xml
<bean id="personServiceFactory" class="cn.itcast.service.OrderFactory"/>
<bean id="personService" factory-bean="personServiceFactory" factory-method="createOrder"/>
```

```java
public class OrderFactory {
    public OrderServiceBean createOrder(){
        return new OrderServiceBean();
    }
}
```

第一种方式使用情况较多

#### 配置属性

##### 懒加载

项目初始化时spring容器会初始化所有的bean，但是有些bean并不是项目一运行就会被用到，所以没必要在项目初始化时就把他们初始化，浪费内存。懒加载可以使bean在项目要用到它时才被初始化。

在bean中lazy-init默认为false，实现懒加载需要将其改为true。

```xml
<bean id="userDaoImpl" class="com.Darkra1Zzz.dao.impl.UserDaoImpl" lazy-init="true"/>
```

##### 作用域

spring提供了bean在项目中作用域的几种选择，分别是 

singleton单例模式，prototype非单例模式，request，session，global session

默认为singleton

```xml
<bean id="userDaoImpl" class="com.Darkra1Zzz.dao.impl.UserDaoImpl" scope="singleton"/>
```

##### 初始化和销毁方法

```xml
<bean id="userBizImpl" class="com.Darkra1Zzz.biz.impl.UserBizImpl" init-method="start" destroy-method="stop"/>
```

### 依赖注入

#### 注入依赖的两种基本方法

这里主要说明注入如何注入参数和对象，注入集合的方式先欠着以后补

##### 构造方法

参考1.2.1.1中重载构造方法后注入参数的方式

##### set

```xml
<bean id="userBizImpl" class="com.Darkra1Zzz.biz.impl.UserBizImpl" >
    <property name="age" value="111"/>
    <property name="name" value="abc"/>
</bean>
```

注入参数

但是这种方式主要是用于注入类

```xml
<bean id="userDaoImpl" class="com.Darkra1Zzz.dao.impl.UserDaoImpl"/>
<bean id="userBizImpl" class="com.Darkra1Zzz.biz.impl.UserBizImpl" lazy-init="true" init-method="start" destroy-method="stop">
    <property name="age" value="111"/>
    <property name="name" value="abc"/>
    <property name="dao" ref="userDaoImpl"/>
</bean>
```

要注意Bean中注入类用的是ref，和普通参数不一样，**且Bean类中要含有注入类的get,set方法**

```java
//    UserDao dao=new UserDaoImpl();   
    private UserDao dao;
```

这样就可以不用再new类了，直接定义。

##### 注入依赖的两种方式

```xml
方式一
<bean id="orderDao" class="cn.itcast.service.OrderDaoBean"/>
<bean id="orderService" class="cn.itcast.service.OrderServiceBean">
    <property name="orderDao" ref="orderDao"/>
</bean>
方式二(使用内部bean,但该bean不能被其他bean引用)  不推荐使用
<bean id="orderService" class="cn.itcast.service.OrderServiceBean">
    <property name="orderDao">
        <!--没有id(name)属性，所以不能被其它引用-->
        <bean class=“cn.itcast.service.OrderDaoBean”/>
    </property>
</bean>
```



#### 依赖的第三种注入方法--注解

##### 手动注解

##### ![image-20210606202116881](image-20210606202116881.png)

![image-20210606204944703](image-20210606204944703.png)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd">
          <context:annotation-config/>
   <bean id="userDaoImpl2" class="com.Darkra1Zzz.dao.impl.UserDaoImpl2" />
   <bean id="userBizImpl2" class="com.Darkra1Zzz.biz.impl.UserBizImpl2"/>
</beans>
```

```java
@Resource(name = "userDaoImpl2")
private UserDao dao;//不需要
```

使用注解的方式，被注入的地方就不用加上注入类的get set方法了

注解加上name 就会对应配置文件中的id，如果没加name则默认根据type进行注入

所以当有多个同类型类时 应加上name作为区分，如果只有一个类型直接默认type就可以了

##### 自动注解

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd">
          <context:component-scan base-package="
          com.Darkra1Zzz.dao.impl,
          com.Darkra1Zzz.biz.impl,
          com.Darkra1Zzz.action"/>
</beans>
```

![image-20210607012713315](image-20210607012713315.png)



## 2. AOP (Aspect Oriented Programming)

面向切面：开发过程中横向考虑功能实现，我认为依旧是利用封装的思想，就像使用工具类。

好处：解耦，易扩，易复用。

### 代理模式

#### 静态代理

需要使用的时候new出来操作，需要程序员将代理实现的代码写出来

#### 动态代理

容器帮我们管所有代理，所谓动态，指的是需要做事的时候容器叫代理干，不需要的时候容器不叫代理干。

突出一个智能。

![image-20210607020444945](image-20210607020444945.png)

### 一些理论

以后再写吧

### 一些工具

Spring提供的一些工具，base on AOP的思想

#### JdbcTemplate

一个数据库访问层的工具，似乎可以配置连接池

![image-20210607124527629](image-20210607124527629.png)

![image-20210607145117933](image-20210607145117933.png)

### 事务

![image-20210607171927047](image-20210607171927047.png)

![image-20210607173832942](image-20210607173832942.png)





learn from 莫筱婷 













