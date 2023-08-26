---
title: SpringBoot 补充
date: 2021-07-22 01:14:02
tags: spring
categories: 码
---

学习springboot过程的一些笔记，自用



<!--more-->

# SpringBoot（补充）

demo

```java
package com.darkra1zzz.entity;

import javax.persistence.*;

/**
 * @Author DarkraiZzz
 * @Time 2021/7/16 21:23
 * @Version 1.0
 */
@Entity
@Table(name = "t_logtype")
public class LogType {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)  //主键自增
    private int tid;
    private String tname;

    @Override
    public String toString() {
        return "LogType{" +
                "tid=" + tid +
                ", tname='" + tname + '\'' +
                '}';
    }
    public int getTid() {
        return tid;
    }

    public void setTid(int tid) {
        this.tid = tid;
    }

    public String getTname() {
        return tname;
    }

    public void setTname(String tname) {
        this.tname = tname;
    }
}
```

```java
package com.darkra1zzz.entity;

import javax.persistence.*;

/**
 * @Author DarkraiZzz
 * @Time 2021/7/16 21:23
 * @Version 1.0
 */
@Entity
@Table(name = "t_mylog")
public class MyLog {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)  //主键自增
    private int lid;
    private String ltitle;
    private String lmsg;
    @ManyToOne(fetch = FetchType.EAGER)
    @JoinColumn(name="tid")
    private LogType logType;


    @Override
    public String toString() {
        return "MyLog{" +
                "lid=" + lid +
                ", ltitle='" + ltitle + '\'' +
                ", lmsg='" + lmsg + '\'' +
                ", logType=" + logType +
                '}';
    }

    public int getLid() {
        return lid;
    }

    public void setLid(int lid) {
        this.lid = lid;
    }

    public String getLtitle() {
        return ltitle;
    }

    public void setLtitle(String ltitle) {
        this.ltitle = ltitle;
    }

    public String getLmsg() {
        return lmsg;
    }

    public void setLmsg(String lmsg) {
        this.lmsg = lmsg;
    }

    public LogType getLogType() {
        return logType;
    }

    public void setLogType(LogType logType) {
        this.logType = logType;
    }
}
```

```java
package com.darkra1zzz.controller;

import com.darkra1zzz.dao.LogTypeDao;
import com.darkra1zzz.dao.MyLogDao;
import com.darkra1zzz.entity.LogType;
import com.darkra1zzz.entity.MyLog;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.web.bind.annotation.*;

import javax.annotation.Resource;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * @Author DarkraiZzz
 * @Time 2021/7/16 21:46
 * @Version 1.0
 */
@RestController
@RequestMapping("log")
@CrossOrigin
public class LogController {
    @Resource
    private LogTypeDao logTypeDao;
    @Resource
    private MyLogDao myLogDao;
    @PostMapping("addlogtype")
    public LogType addLogType(LogType logType){
        return logTypeDao.save(logType);
    }
    @PostMapping("addlog")
    public MyLog addMyLog(MyLog myLog){
        System.out.println(myLog);
        return myLogDao.save(myLog);
    }
    @GetMapping("queryalllogtype")
    public List<LogType> queryAllLogType(){
        return logTypeDao.findAll();
    }
    @GetMapping("queryalllog")
    public Map<String,Object> queryalllog(int page){
        page--;
        Pageable pageable= PageRequest.of(page,2);
        Page<MyLog> myLogPage=myLogDao.findAll(pageable);
        Map<String,Object> map=new HashMap<>();
        map.put("countsize",myLogPage.getTotalElements());
        map.put("countpage",myLogPage.getTotalPages());
        map.put("page",++page);
        map.put("list",myLogPage.getContent());
        return map;
    }
}
```

```java
package com.darkra1zzz.dao;

import com.darkra1zzz.entity.LogType;
import org.springframework.data.jpa.repository.JpaRepository;

public interface LogTypeDao extends JpaRepository<LogType,Integer> {
}
```

```java
package com.darkra1zzz.dao;

import com.darkra1zzz.entity.MyLog;
import org.springframework.data.jpa.repository.JpaRepository;

public interface MyLogDao extends JpaRepository<MyLog,Integer> {
}
```

```html
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<script type="text/javascript" src="http://lib.sinaapp.com/js/jquery/2.0.3/jquery-2.0.3.min.js"></script>
		<script type="text/javascript">
			$(function(){
				initlogtype();
			});
			function initlogtype(){
				$.getJSON("http://localhost:7070/Darkra1Zzz/log/queryalllogtype",function(data){
					var str="";
					for(var i=0;i<data.length;i++){
						str+=`<option value="${data[i].tid}">${data[i].tname}</option>`;
					}
					$('#tid').html(str);
				});
			}
			function addtype(){
				$.ajax({
					type:"post",
					url:"http://localhost:7070/Darkra1Zzz/log/addlogtype",
					async:"true",
					data:{tname:$('#logtype').val()},
					dataType:'json',
					success:function(data){
						alert("成功");
						initlogtype();
					},
					error:function(){
						alert("失败");
					}
				});
			}
			
			function addlog(){
				$.ajax({
					type:"post",
					url:"http://localhost:7070/Darkra1Zzz/log/addlog",
					async:"true",
					data:$('#myform').serialize(),
					dataType:'json',
					success:function(data){
						alert(data);
					},
					error:function(){
						alert("失败");
					}
				});
			}
			
		</script>
	</head>
<body>
	<h1>添加类型</h1>
	日志类型:<input type="text" id="logtype" />
	<input type="button" value="添加类型" onclick="addtype()" />
	<br /><br /><br />
	<form  id="myform">
	<h1>添加日志</h1>
	<p>日志标题:<input type="text" name="ltitle" /></p>
	<p>日志内容:<input type="text" name="lmsg" /></p>
	<p>
		日志类型:
		<select name="logType.tid" id="tid">
		</select>
	</p>
	</form>
	<input type="button" value="添加日志" onclick="addlog()" />
</body>
</html>
```

-------------------

### 表单验证

常用的正则表达式

```
 约束注解名称       约束注解说明
@null             验证对象是否为空
@notnull          验证对象是否为非空
@asserttrue       验证 boolean 对象是否为 true
@assertfalse      验证 boolean 对象是否为 false
@min              验证 number 和 string 对象是否大等于指定的值
@max              验证 number 和 string 对象是否小等于指定的值
@decimalmin       验证 number 和 string 对象是否大等于指定的值，小数存在精度
@decimalmax       验证 number 和 string 对象是否小等于指定的值，小数存在精度
@size             验证对象（array,collection,map,string）长度是否在给定的范围之内
@digits           验证 number 和 string 的构成是否合法
@past             验证 date 和 calendar 对象是否在当前时间之前
@future           验证 date 和 calendar 对象是否在当前时间之后
@pattern          验证 string 对象是否符合正则表达式的规则
@Email            验证邮箱



实际例子：

@size (min=3, max=20, message="用户名长度只能在3-20之间")

@size (min=6, max=20, message="密码长度只能在6-20之间")

@pattern (regexp="[a-za-z0-9._%+-]+@[a-za-z0-9.-]+\\.[a-za-z]{2,4}", message="邮件格式错误")

@Length(min = 5, max = 20, message = "用户名长度必须位于5到20之间")

@Email(message = "比如输入正确的邮箱")

@NotNull(message = "用户名称不能为空")

@Max(value = 100, message = "年龄不能大于100岁")

@Min(value= 18 ,message= "必须年满18岁！" )

@AssertTrue(message = "bln4 must is true")

@AssertFalse(message = "blnf must is falase")

@DecimalMax(value="100",message="decim最大值是100")

@DecimalMin(value="100",message="decim最小值是100")

@NotNull(message = "身份证不能为空")

@Pattern(regexp="^(\\d{18,18}|\\d{15,15}|(\\d{17,17}[x|X]))$", message="身份证格式错误")
 
```

**导jar**

一个小错误。。

![image-20210719111605536](image-20210719111605536.png)

添加spring验证的pom依赖时 不小心把scope给复制进去了  怪不得一直获取不到。。

**实体类注解**

![image-20210719121227088](image-20210719121227088.png)

**加上参数注释**

找到需要用到表单验证的地方 加上参数注释

![image-20210719121423989](image-20210719121423989.png)

----------------

### Aop日志管理

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

```
package com.darkra1zzz.interceptor;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.*;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import org.springframework.web.context.request.RequestContextHolder;
import org.springframework.web.context.request.ServletRequestAttributes;

import javax.servlet.http.HttpServletRequest;

/**
 * @Author DarkraiZzz
 * @Time 2021/7/19 13:28
 * @Version 1.0
 */
@Aspect
@Component
public class LogAspect {
    private Logger logger= LoggerFactory.getLogger(LogAspect.class);

    @Pointcut("execution(* com.darkra1zzz.controller.*.*(..))")   //第一个*：返回值, 第二个*类名, 第三个*方法名, 第四个* 任意入参
    public void log(){
    }

    @Before("log()")
    public void logbefore(JoinPoint joinPoint){
        ServletRequestAttributes attributes= (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        HttpServletRequest request=attributes.getRequest();
        logger.info("url={}",request.getRequestURI());
        logger.info("method={}",request.getMethod());
        logger.info("method={}",request.getRemoteAddr());
        logger.info("class_method={}",joinPoint.getSignature().getDeclaringTypeName()+"----"+joinPoint.getSignature().getName()); //类名+方法名
        logger.info("args={}",joinPoint.getArgs());  //入参
    }

    @AfterReturning(pointcut = "log()",returning = "object")
    public void logAfterReturn(Object object){
        logger.info("response={}",object);
    }
}
```

-------------

### 异常处理

```java
package com.darkra1zzz.error;

import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;

/**
 * @Author DarkraiZzz
 * @Time 2021/7/19 15:54
 * @Version 1.0
 */
@ControllerAdvice
public class MyException {
    @ExceptionHandler(value = IllegalArgumentException.class)
    @ResponseBody
    public Result test(IllegalArgumentException e){
        return ResultUtil.error(1001,e.getMessage().toString());
    }

    @ExceptionHandler(value = ArithmeticException.class)
    @ResponseBody
    public Result test2(ArithmeticException e){
        return ResultUtil.error(1002,e.getMessage().toString());
    }

    @ExceptionHandler(value = Exception.class)
    @ResponseBody
    public Result test3(Exception e){
        return ResultUtil.error(1003,e.getMessage().toString());
    }
}
```

----------------------

### Mybatis逆向工程

需要了解：

- 正向工程：JPA 写好实体类后 自动生成数据库表

- 逆向工程：Mybatis 写好数据库表后 自动生成实体类

开整：

先创建一个**quickstart**项目

![image-20210721123248781](image-20210721123248781.png)

**pom**

这几个jar导入为了做逆向工程

```xml
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.7</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.25</version>
</dependency>
<dependency>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-core</artifactId>
    <version>1.4.0</version>
</dependency>
```

反向工程**配置**文件

在src根路径下新建xml文件

```xml
<?xml version='1.0' encoding='UTF-8'?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <context id="mybatisGenerator" targetRuntime="MyBatis3">
        <commentGenerator>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true" />
        </commentGenerator>
        <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://127.0.0.1:3306/springbootdemo?characterEncoding=utf-8&amp;serverTimezone=GMT%2B8&amp;allowMultiQueries=true"
                        userId="root"
                        password="123456">
            <property name="nullCatalogMeansCurrent" value="true"/>
        </jdbcConnection>

        <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和
            NUMERIC 类型解析为java.math.BigDecimal -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false" />
        </javaTypeResolver>

        <!-- targetProject:生成PO类的位置 -->
        <javaModelGenerator targetPackage="com.darkra1zzz.entity"
                            targetProject=".\src">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
            <!-- 从数据库返回的值被清理前后的空格 -->
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
        <!-- targetProject:mapper映射文件生成的位置 -->
        <sqlMapGenerator targetPackage="com.darkra1zzz.dao"
                         targetProject=".\src">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </sqlMapGenerator>
        <!-- targetPackage：mapper接口生成的位置 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.darkra1zzz.dao"
                             targetProject=".\src">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </javaClientGenerator>
        <!-- 指定数据库表 -->
        <table tableName="t_person" domainObjectName="Person" mapperName="PersonDao"></table>
        <table tableName="t_student" domainObjectName="Student" mapperName="StudentDao"></table>
        <table tableName="t_teacher" domainObjectName="Teacher" mapperName="TeacherDao"></table>
        <!-- 有些表的字段需要指定java类型
         <table schema="" tableName="">
            <columnOverride column="" javaType="" />
        </table> -->
    </context>
</generatorConfiguration>
```

需要注意更换的几个地方

- 数据库连接的信息
- 生成PO类的位置
- mapper映射文件生成的位置
- mapper接口生成的位置
- 指定数据库表 

**项目结构**

创建好项目结构，com.darkra1zzz.entity，com.darkra1zzz.dao

**启动类**

在启动类中加入以下代码

```java
List<String> warnings = new ArrayList<String>();    /**指向逆向工程配置文件*/
File configFile = new File("src/generatorConfig.xml");
ConfigurationParser parser = new ConfigurationParser(warnings);
Configuration config = parser.parseConfiguration(configFile);
DefaultShellCallback callback = new DefaultShellCallback(true);
MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
myBatisGenerator.generate(null);
```

启动后会发现dao类和实体类都自动生成了

![image-20210721125258528](image-20210721125258528.png)

观察项目结构会发现多出了奇怪的东西

每个实体类都额外多生成了一个Example类

它的作用是改和查时负责放限制条件的

下面有具体例子



**接下来打开原先项目，将项目框架改成springboot+mybatis**

**SpringBoot配置文件**

```xml
server:
  port: 7070
  servlet:
    context-path: /Darkra1Zzz
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/springbootdemo?characterEncoding=utf-8&serverTimezone=GMT%2B8
    username: root
    password: 123456
mybatis:
  mapper-locations: classpath:com/darkra1zzz/dao/*.xml
  type-aliases-package: com.darkra1zzz.entity
```

注意：与之前JPA不同的是 Mybatis和Spring同级，因为Mybatis属于外来工具，而JPA属于Spring自带的工具。

**导入Mybatis jar包**

```
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.4</version>
</dependency>
```

**启动类**

需要添加两个注释

@MapperScan("com.darkra1zzz.dao")

@EnableTransactionManagement

```java
@SpringBootApplication
@MapperScan("com.darkra1zzz.dao")
@EnableTransactionManagement
public class Bootdemo5Application {
    public static void main(String[] args) {
        SpringApplication.run(Bootdemo5Application.class, args);
    }
}
```

将原先和JPA有关的类全部删除

把逆向生成的实体类和dao复制进这个项目

#### 增删改查

**查询全部**

```java
@GetMapping("person")
public List<Person> findAll() {
    return personDao.selectByExample(null);  //不設任何查詢條件 表示查詢全部
}
```

**条件查询**

```java
@GetMapping("GreaterThanEqual/{age}")
public List<Person> findByAgeGreaterThanEqual(@PathVariable int age) {
    PersonExample personExample = new PersonExample();
    personExample.createCriteria().andAgeGreaterThanOrEqualTo(age);
    return personDao.selectByExample(personExample);
}
```

```java
@GetMapping("nameLike/{name}")
public List<Person> findByNameLike(@PathVariable String name) {
    PersonExample personExample = new PersonExample();
    personExample.createCriteria().andNameLike("%" + name + "%");
    return personDao.selectByExample(personExample);
}
```

**多条件查询**

```java
@GetMapping("and/{name}/{age}")
public List<Person> findByNameAndAge(@PathVariable String name, @PathVariable int age) {
    PersonExample personExample = new PersonExample();
    personExample.createCriteria().andNameEqualTo(name);
    personExample.createCriteria().andAgeEqualTo(age);
    return personDao.selectByExample(personExample);
}
```

**排序查询**

```java
@GetMapping("orderBy")
public List<Person> findOrderByAge(){
    PersonExample personExample = new PersonExample();
    personExample.setOrderByClause("age desc");
    return personDao.selectByExample(personExample);
}
```

**增**

```java
@PostMapping("add")
public boolean addPerson(Person person) {
    return personDao.insert(person) != 0;
}
```

**删**

```java
@PostMapping("deleteById/{id}")
public boolean deleteById(@PathVariable int id){
    return personDao.deleteByPrimaryKey(id)>0;
}

@PostMapping("deleteByName/{name}")
public boolean deleteById(@PathVariable String name){
	PersonExample personExample = new PersonExample();
	personExample.createCriteria().andNameEqualTo(name);
	return personDao.deleteByExample(personExample)>0;
}
```

**改**

```java
@PostMapping("updateById/{id}")
public boolean updateById(@PathVariable int id){
    Person person=new Person();
    person.setName("根据ID更新");
    person.setId(id);
    return personDao.updateByPrimaryKeySelective(person)>0;
}

@PostMapping("updateByName/{name}")
public boolean updateByName(@PathVariable String name){
    Person person=new Person();
    person.setPhone(123456789);
    person.setAge(99999);
    PersonExample personExample = new PersonExample();
    personExample.createCriteria().andNameEqualTo(name);
    return personDao.updateByExampleSelective(person,personExample)>0;
}
```

#### 分页

**导入分页插件**

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.3.1</version>
</dependency>
```

**配置**

```xml
server:
  port: 7070
  servlet:
    context-path: /Darkra1Zzz
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/springbootdemo?characterEncoding=utf-8&serverTimezone=GMT%2B8
    username: root
    password: 123456
mybatis:
  mapper-locations: classpath:com/darkra1zzz/dao/*.xml
  type-aliases-package: com.darkra1zzz.entity
pagehelper:
  helperDialect: mysql
  reasonable: true
  supportMethodsArguments: true
  params: count=countSql
```

在需要分页的地方前加上

**PageHelper.startPage(pageNum,pageSize);**

两个例子

```java
//加上分页的排序
@GetMapping("orderBy2")
public List<Person> findOrderByAge2(){
    PersonExample personExample = new PersonExample();
    personExample.setOrderByClause("age desc");
    PageHelper.startPage(1,3);
    return personDao.selectByExample(personExample);
}
```

```java
@GetMapping("GreaterThanEqual/{age}")
public List<Person> findByAgeGreaterThanEqual(@PathVariable int age) {
    PersonExample personExample = new PersonExample();
    personExample.createCriteria().andAgeGreaterThanOrEqualTo(age);
    PageHelper.startPage(1,3);
    return personDao.selectByExample(personExample);
}
```

-----------------------------

### Swagger

**导包**

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
```

**配置**

```java
package com.darkra1zzz.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

/**
 * @Author DarkraiZzz
 * @Time 2021/7/22 0:08
 * @Version 1.0
 */
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .pathMapping("/")
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.darkra1zzz"))
                .paths(PathSelectors.any())
                .build().apiInfo(new ApiInfoBuilder()
                        .title("SpringBoot整合Swagger")
                        .description("SpringBoot整合Swagger，详细信息......")
                        .version("1.0")
                        .contact(new Contact("darkra1","com.darkra1zzz","darkra1zzz@qq.com"))
                        .build());
    }
}
```

**访问地址：**http://localhost:7070/Darkra1Zzz/swagger-ui.html#/

-----------------------

### druid连接池

**导包**

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.6</version>
</dependency>
```

**配置**

```yml
server:
  port: 7070
  servlet:
    context-path: /Darkra1Zzz
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/springbootdemo?characterEncoding=utf-8&serverTimezone=GMT%2B8
    username: root
    password: 123456
    type: com.alibaba.druid.pool.DruidDataSource
    max-active: 50
    initial-size: 10
    max-wait: 60000
    min-idle: 10
    time-between-eviction-runs-millis: 60000
    min-evictable-idle-time-millis: 300000
    validation-query: select 1 from dual
    test-on-borrow: false
    test-on-return: false
    test-while-idle: true
    pool-prepared-statements: false
    filters: stat,wall,slf4j
mybatis:
  mapper-locations: classpath:com/darkra1zzz/dao/*.xml
  type-aliases-package: com.darkra1zzz.entity
pagehelper:
  helperDialect: mysql
  reasonable: true
  supportMethodsArguments: true
  params: count=countSql
```

```java
package com.darkra1zzz.config;

import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.support.http.StatViewServlet;
import com.alibaba.druid.support.http.WebStatFilter;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;
/**
 * @Author DarkraiZzz
 * @Time 2021/7/22 0:48
 * @Version 1.0
 */
@Configuration
public class MyConfiguration {
    // StatViewServlet 配置管理页面，主要是管理数据库连接
    @Bean
    public ServletRegistrationBean DruidStatViewServlet() {
        ServletRegistrationBean srb = new ServletRegistrationBean(new StatViewServlet(), "/druid/*");
        // 添加管理页面的初始化参数：initParams
        // 配置白名单，即允许访问druid管理页面的ip
        srb.addInitParameter("allow", "127.0.0.1");
        // 配置黑名单，即不允许访问druid管理页面的ip
        srb.addInitParameter("deny", "192.168.101.15");
        // 配置登陆的用户名和密码
        srb.addInitParameter("loginUsername", "admin");
        srb.addInitParameter("loginPassword", "123456");
        // 配置是否可以重置,点击重置会重置页面，我们这里不希望，当然你要true也可以，直接不配置也行
        srb.addInitParameter("resetEnble", "false");
        return srb;
    }
    // webStatFilter 配置管理web请求的功能的过滤器，不但会管链接，也可以管请求
    @Bean
    public FilterRegistrationBean druidStatFilter() {
        FilterRegistrationBean frb = new FilterRegistrationBean(new WebStatFilter());
        frb.addUrlPatterns("/*");
        // 不是所有的web请求都监控，去掉一些静态的请求和我自己druid的请求
        frb.addInitParameter("exclusions","*.js,*.gif,*.jpg,*.png,*.css,/druid/*");
        return frb;
    }
    @Bean(destroyMethod = "close",initMethod = "init")
    @ConfigurationProperties(prefix="spring.datasource")
    public DataSource druidDataSource(){  //import javax.sql.DataSource;
        return new DruidDataSource();
    }


}
```

注：

```java
@Bean(destroyMethod = "close",initMethod = "init")
```

这行的报错不用理会 直接运行项目即可

**访问地址：**http://localhost:7070/Darkra1Zzz/druid/login.html

---------------------

### FAQ

报错提示找不到xml文件，可能是pom中没有为项目配置默认xml路径，可在build中加上

```xml
<resources>
    <resource>
        <directory>src/main/java</directory>
        <includes>
            <include>**/*.xml</include>
        </includes>
    </resource>
</resources>
```

