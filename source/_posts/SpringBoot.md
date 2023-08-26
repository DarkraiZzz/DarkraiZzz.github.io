---
title: SpringBoot
date: 2021-07-12 03:28:16
tags: spring
categories: 码
---

学习springboot过程的一些笔记，自用



<!--more-->

# SpringBoot



### 是什么

1. SpringBoot是一个集成了众多框架的大框架。由于各种框架技术的流行，开发中配置需要用到的框架成为较为笨重的一步。例如ssm架构，一个项目中出现了大量的xml和properties配置文件，学习ssm的过程中经常遇到关于配置的问题，一顿乱查之后发现一百篇教程有一百种配置方式，好像哪种都和自己目前的项目对不上号。麻烦。
2. 而SpringBoot以官方权威的身份出现，将配置步骤化繁为简，将项目所需要的框架统一集成好，不需要自己找适配版本和手动配置。突出一个方便。其提供多种框架集成配置方式，供使用者进行选择。
3. SpringBoot以“习惯优于配置”为主，提供的集成方式都是SpringBoot认为常见开发的习惯方式，而不是各种杂七杂八个人的配置方式

总结，SpringBoot就是一个帮你自动集成各种框架的大框架。



------------------

### 先让项目跑起来

新建项目

![image-20210705130946903](image-20210705130946903.png)

Artifact在使用maven的过程中也见到过，是指公司的名字

打包方式选用Jar

第一个SpringBoot项目只用到了Spring Web框架所以只需勾选Spring Web，本次学习需要用到的集成框架勾选情况如下

![image-20210705131303060](image-20210705131303060.png)



项目目录

![image-20210705131639114](image-20210705131639114.png)

 	1.	springboot项目没有webapp。这是因为springboot提倡前后端分离项目，但如果开发者需要前后端不分离，springboot也支持。
 	2.	有一个启动类，springboot内置了tomcat，这个启动类就是用来启动tomcat的，所以不需要配置tomcat。
 	3.	由上两点可知，jsp并不推荐作为前端技术被使用在spingboot项目中，因为jsp需要在服务器中被编译成java文件（jsp和servlet的关系），而前后端分离的项目，其前端是不带服务器的。springboot项目中前端主要使用Html。前后端交互主要通过ajax（ResponseBody）。





一个简单web项目的依赖（只勾选了Spring Web）

![image-20210705133139263](image-20210705133139263.png)



![image-20210705133128102](image-20210705133128102.png)

SpringBoot彩蛋

![image-20210705133112181](image-20210705133112181.png)



可以发现默认项目名称是空的，端口号是8080，配置文件的名字是application。这里体现了“习惯优于配置”

尽量不要改变SpringBoot提供的习惯。 为了少出点错 就尽量习惯它的习惯吧。

![image-20210705133321584](image-20210705133321584.png)

![image-20210705133056122](image-20210705133056122.png)

运行成功

配置上项目名，再次运行

![image-20210705133500881](image-20210705133500881.png)



![image-20210705133705300](image-20210705133705300.png)

将配置文件由properties改为yml

![image-20210705145512172](image-20210705145512172.png)

再次运行

![image-20210705145446220](image-20210705145446220.png)







![image-20210706022123373](image-20210706022123373.png)

启动类中的注解

@SpringBootApplication是个组合注解，进源码可以发现里面含有好多个注解。

![image-20210706022147803](image-20210706022147803.png)

其中

@SpringBootConfiguration   自动读取配置文件

@EnableAutoConfiguration   自动启动相关框架

@ComponentScan   自动扫描同级及其所有子文件



resources下的文件路径

![image-20210706023350022](image-20210706023350022.png)

static和templates都是项目创建初始化自动生成的，以前的web项目都有个public文件用于存放jsp。这里也体现了“习惯优于配置” 即SpringBoot不希望你将前端项目的文件放入到后端项目中。但是你可以自己创建一个public文件夹。即自己配置，压过SpringBoot给的习惯。

顾名思义public文件中东西是允许访问的，但templates里的不行，要通过控制层来访问，需要一个前端模板引擎

![image-20210706023335564](image-20210706023335564.png)

![image-20210706023801224](image-20210706023801224.png)![image-20210706023811227](image-20210706023811227.png)



![image-20210706023754288](image-20210706023754288.png)

![image-20210706023838916](image-20210706023838916.png)![image-20210706023847174](image-20210706023847174.png)

页面必须放在public里面

templates里放的是内嵌的 只能通过控制层做跳转。



![image-20210706024934784](image-20210706024934784.png)

![image-20210706024939539](image-20210706024939539.png)

导入thymeleaf模板引擎后（html是静态网页。需要一个东西来帮助进行前后端的数据交互，让他像jsp一样做事，这个东西就是thymeleaf） 通过控制层可以访问到templates里的东西了。



### thymeleaf

是什么

1.jsp文件是没办法像html用浏览器直接进行访问的 jsp是在html中加入java，其要先编译成java文件再编译成class文件  在开发过程中 前后端应彻底分离，前端不写java 后端不写html。 但只用html 无法在页面进行简单的逻辑判断。

2.让html拥有循环判断取值等功能的东西



![image-20210706233147822](image-20210706233147822.png)

![image-20210706233234005](image-20210706233234005.png)

控制层访问任意页面配置

![image-20210706233532468](image-20210706233532468.png)

注意加上头标签

1.资源的引用

![image-20210706234257550](image-20210706234257550.png)

![image-20210706234130956](image-20210706234130956.png)



![image-20210706234137223](image-20210706234137223.png)









2.参数传递

![image-20210707001416855](image-20210707001416855.png)

![image-20210707001425675](image-20210707001425675.png)





![image-20210707001606782](image-20210707001606782.png)

![image-20210707001545450](image-20210707001545450.png)

![image-20210707010246537](image-20210707010246537.png)

![image-20210707010236199](image-20210707010236199.png)

3.循环+判断

![image-20210707013717755](image-20210707013717755.png)

![image-20210707013700002](image-20210707013700002.png)

![image-20210707013529234](image-20210707013529234.png)





### 日志

配置见图

![image-20210707160213924](image-20210707160213924.png)![image-20210707160223964](image-20210707160223964.png)

![image-20210707160244306](image-20210707160244306.png)



### 注入

注入属性

![image-20210707160737044](image-20210707160737044.png)

![image-20210707160704057](image-20210707160704057.png)



![image-20210707162016198](image-20210707162016198.png)

![image-20210707161935571](image-20210707161935571.png)





![image-20210707163416736](image-20210707163416736.png)





不同开发环境配置

![image-20210707180501115](image-20210707180501115.png)



### 控制层优化

如果Controller中的方法全都是通过Ajax进行数据交互的，在类前加上@RestController注解，可以省去每一个方法前的@ResponseBody注解。前提是该类中所有方法都返回的是数据，而不是跳转页面。



get请求的发起方式有三种 get，a标签，地址栏

post请求的发起方式有三种 表单method=post,ajax{method:post}



get请求的测试方法很简单 在浏览器地址栏测试即可

但是post不行啊。 post得写界面，写表单。这也忒麻烦了叭！



### Postman

postman可以帮助解决这个问题

关于传值：

1.@PathVariable 对应占位符 @RequestParam 对应表单中的参数名  都 应用在Controller层

2.@Param主要应用在Dao层

![image-20210708013941724](image-20210708013941724.png)

```
@PostMapping("user")
 @RequestMapping(value = "user",method = RequestMethod.POST)
```

这两个是一个东西

```
@GetMapping("user")
@RequestMapping(value = "user",method = RequestMethod.GET)
```

这两个是一个东西

使用占位符时

```
@GetMapping("user/{name}/{age}")
public User getUser(@PathVariable String name,@PathVariable int age){
    return new User(name,age);
}
```

记得@PathVariable





### 跨域

<html>

	<head>
		<meta charset="utf-8" />
		<title></title>
		<script src="js/jquery-3.2.1.js" type="text/javascript"></script>
		<script type="text/javascript">
			function getinit(){
				$.post("http://localhost:7070/Darkra1Zzz/user",{
					name: "达克雷伊",
					age: 22
				},function(data){
					$('#myname').val(data.name);
					$('#myage').val(data.age);
				});
			}
		</script>
	</head>

​	<body onload="getinit()">
​		姓名：<input type="text" id="myname" />
​		年龄：<input type="text" id="myage" />
​	</body>
</html>

前端用ajax接收后端传的数据

```
@PostMapping("user")
public User getUser(User user){
    user.setName("后端"+user.getName());
    user.setAge(100+user.getAge());
    return user;
}
```

![image-20210708014441620](image-20210708014441620.png)

运行之后发现没接到数据，为什么呢

F12	

![image-20210708014523955](image-20210708014523955.png)

跨域问题

解决方式有两种

1. 通过爬虫的方式 用流来一点一点读
2. 加上运行跨域的注解

![image-20210708014736808](image-20210708014736808.png)

加上允许跨域注解

![image-20210708014802694](image-20210708014802694.png)

done!

前后分离正常有两种方式，ajax和vue





### Spring Data JPA

是什么

JPA（Java Persistence API）是一种数据库交互的接口，需要有遵循这个接口标准的东西来实现这个接口。hibernate就是其中一个能实现JPA接口的ORM框架。

##### *JPA-Hibernate

配置，编写实体类

![image-20210709014517970](image-20210709014517970.png)

![image-20210709014442341](image-20210709014442341.png) 

运行项目后，数据库表自动生成。不需要手动建表。

![image-20210709014916628](image-20210709014916628.png)

![image-20210709014743544](image-20210709014743544.png)





加上@Table注释可以规定表名

![image-20210709015113159](image-20210709015113159.png)

![image-20210711014349558](image-20210711014349558.png)

实体类中属性名上的注释@GeneratedValue 用于标注主键的生成策略，通过strategy 属性指定。默认情况下，JPA 自动选择一个最适合底层数据库的主键生成策略即通过表产生主键，会自动生成一个hibernate_sequence表 ,@Id 表示该属性是主键

```java
@GeneratedValue
```

默认

```java
@GeneratedValue(strategy = GenerationType.IDENTITY)
```

采用数据库ID自增长的方式来自增主键字段，Oracle 不支持这种方式



插一个spring知识点。

@Autowired和@Resource的区别

@Autowired是spring自己定义的注解 @Resource是J2EE规范定义的注解。

这两个spring都支持

@Autowired是按byType自动注入

@Resource可以byType也可以byName,默认是byName



##### *hibernate

hibernate的概念 数据持久化



测试addPerson方法

![image-20210709142604362](image-20210709142604362.png)

![image-20210709142613788](image-20210709142613788.png)

成功



Optional

这是个好东西  里面的isPresent方法可以代替!=null 且更高效 



##### *单表

######     单表的简单增删改查

![image-20210710012602115](image-20210710012602115.png)

![image-20210710012634156](image-20210710012634156.png)





######     单表排序+分页

![image-20210710164106763](image-20210710164106763.png)







##### *多表

######     多表关联

![image-20210711030516103](image-20210711030516103.png)

注意：外键名字要相同，图里命名为tid1 tid2是为了测试自动生成的数据库表是否能同时生成两个外键



```java
@ManyToOne
```

```
@OneToMany
```

这两个注解分别表示多对一和一对多关系

```
@JoinColumn
```

这个注解用来设置关联的外键

```
cascade = CascadeType.ALL
```

是@JoinColumn内的参数，表示设置级联关系，正常情况下不会采用

```
fetch = FetchType.EAGER
```

```
fetch = FetchType.LAZY
```

是@JoinColumn内的参数，表示抓取类型，EAGER为立即抓取（立即加载），LAZY为懒加载





写完实体类，接下来测试Dao。

基础的crud并不用写出Dao接口的实现类，JPA在底层提供了对应的实现类。

![image-20210711030946643](image-20210711030946643.png)

##### *一些报错

调用add方法时报错 （还未设置级联关系）

![image-20210711022311698](image-20210711022311698.png)

原因：不能增加学生时可以同时添加老师，因为学生类中有老师对象属性。但是反过来不行 因为老师类中没定义学生对象属性。hibernate关联关系一定设置了 就会默认存在双向绑定关系。一边进行的操作另一边也会进行。 

解决方案：在注解中设置关联级联关系

![image-20210711023349072](image-20210711023349072.png)

设置完之后可以添加了  奇怪的是再次打开数据库查看表设计却发现里面的设置并没有变为级联。





调用查询方法时报错

![image-20210711234128928](image-20210711234128928.png)![image-20210711234137071](image-20210711234137071.png)

陷入无限死循环

因为在学生类中做了对老师类的多对一关联关系，在老师类中做了对学生类的一对多关联关系

hibernate在查询时会自动将设置了关联关系的对象属性一起查询出来

所以查询学生时 因为学生类中有老师类 就会一起把老师类的属性查询出来 而老师类中又有学生类 就会又把学生类的属性查询出来  就这样一直循环 真够傻的。 

解决方案：在其中一个类的关联属性上加上@JsonIgnore注解  这也就不会在查询到该类时继续查询此对象的数据。

![image-20210711234454956](image-20210711234454956.png)





经典错误

![image-20210711021329645](image-20210711021329645.png)

我的习惯是一样的属性就算在不同的实体类中我也使用一样的名字，当时觉得属性都是通过对象名或者类名点出来的 所以不存在名字冲突，无法识别该属性属于哪个类的问题 ，同时还可以省一点脑力，要写属性名时无脑写 不用管哪个类 反正都一样。 现在遇到报错了。。。  

![image-20210711021616533](image-20210711021616533.png)

在用postman传参时感觉就不太对。

![image-20210711022917846](image-20210711022917846.png)

果然 它并不能根据前后顺序把同名属性隔开放进两个对象，而是”非常智能“的将同名属性装在数组里  可恶

接下来的例子中已经把不同实体类中的同名属性做了更改。



##### *JPA-hibernate缺点

不能同时支持多对一和一对多的关联查询 会造成死循环。所以多对多更不用想了。 

查询效率低，JPQL-封装的sql不太聪明的亚子。（下面有补充）





##### *JPQL

######     方法名规范

按照JPQL的方法命名规范对Dao接口中的方法进行命名，JPA可以识别方法并调用对应的底层实现类来实现该方法

![jpa方法规则](jpa方法规则.png)



举个例子

![image-20210712024026333](image-20210712024026333.png)

该方法符合JPQL命名规则 可以根据学生实体类中的专业属性和学生实体类中的老师属性中的老师性别属性来查询学生对象

![image-20210711170254792](image-20210711170254792.png)



但随着语句条件的增多 按JPQL命名规范写的方法名会而变得复杂，难以阅读  这个时候或许就需要自己来写**JPQL**。

JPQL语句主要以对象为对象，而不像SQL以表为对象。

例如

```
JPQL: from Student stu where stu.id=?1"
```

```
SQL: select stu.id from t_student stu where stu.id='1'
```

sql from的t_student是表名，而 JPQL from的Student是对象。 

JPQL1表示第一个问号  如果有第二个问好则要在后面跟上2，3同理

```
SQL:select * from t_student as stu,t_teacher as tea where stu.tid=tea.id and tea.tname='？'
```

```
JPQL:from Student stu where stu.teacher.tname=?1"
```

jpql只认对象 所以可以一直在对象后面点出该对象内含有的属性，且可以省略select *。

![image-20210712025610049](image-20210712025610049.png)

这样基本解决了简单的查询需求（分页模糊查询，分组统计查询，根据条件查询）

###### 缺点

JPA-Hibernate中的JPQL  修改和联表查询的效率较低  修改时会将一个对象的所有属性不论是否有更改都重新更新，查询时会将可以通过连接查询能解决的事分为好几个单表查询来解决。

例子

![image-20210712025144044](image-20210712025144044.png)

![image-20210712025122624](image-20210712025122624.png)

![image-20210712025108688](image-20210712025108688.png)

将可以一次完成sql查询的事分开进行了多次查询。笨



JPA-Hibernate支持原生SQL语句 效率会高一些 但需要自己手写原生SQL，麻烦。

用原生SQL语句时要注意 增加修改删除都要加上事务注释@Transactional和@Modifying

![image-20210712032216506](image-20210712032216506.png)









### JavaSE

补一下基础。。忘得差不多了

##### *uml



![image-20210712014242552](image-20210712014242552.png)

![image-20210712014300390](image-20210712014300390.png)



引自

https://www.cnblogs.com/huaxingtianxia/p/6590351.html



##### *多态

extends是继承，被继承的可以是普通类也可以是抽象类，implements是实现，被实现的只能是接口。 
一个类只能继承一个类，但可以实现多个接口。 

接口是特殊的抽象类。

接口只能被实现，抽象类只能被继承，普通类可以被继承但不能被实现。

当抽象类继承的父类是抽象类时可以选择性重写父类中的抽象方法，父类未被子类未重写的抽象方法依旧保持抽象方法（选择性继承，但只要类中还有抽象方法，就还是抽象类，可以继续被继承。孙子类可以继承爷爷类，可以跨级继承。但是私有方法和属性不能被继承）

extends继承父类的时候可以重写父类的方法，也可以调用父类的非私有方法；implements实现接口，必须实现接口的所有方法。

![image-20210712021252208](image-20210712021252208.png)

图引自

https://www.cnblogs.com/zxfei/p/10698594.html



关于注解的继承

https://blog.csdn.net/hcrw01/article/details/107991138



​    *实现接口 

必须实现接口的所有方法 （抽象类实现接口的情况还没遇到过 先埋个坑 以后再填）

​    *继承父类

如果父类是抽象类，可以重写父类的普通方法（如果有），也可以继续继承（把抽象方法放在子类中，会造成代码冗余。“为什么接口和抽象类需要选择性使用。”）父类的抽象方法，也可以实现（重写）父类的抽象方法（该方法就变成普通方法）。父类的抽象方法都必须在子类中被写出或被实现（可以选择性实现，剩下的放着），如果没有实现父类所有的抽象方法，子类就是抽象类，因为带有了被被实现的抽象方法。若希望子类是普通方法，则需要实现父类的所有抽象方法

如果父类是普通类，可以重写父类的普通方法，也可以不重写任何东西。子类怎么样都是普通类。



参考自

https://blog.csdn.net/weixin_35152781/article/details/113364504?utm_term=%E7%BB%A7%E6%89%BF%E6%8A%BD%E8%B1%A1%E7%B1%BB%E5%BF%85%E9%A1%BB%E5%AE%9E%E7%8E%B0%E6%89%80%E6%9C%89%E6%96%B9%E6%B3%95%E5%90%97&utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~all~sobaiduweb~default-1-113364504&spm=3001.4430



##### *sql

sql中 DESC 是descend 降序意思 。asc 是ascend 升序的意思。





  



​    learn from 莫筱婷 





