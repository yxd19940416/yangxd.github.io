---
title: java中高级面试题总结
top: false
cover: false
toc: true
mathjax: true
date: 2022-11-07 09:52:39
password:
summary: java中高级面试题总结
tags: 
    - java面试
categories:
    - java
---


目录

[前言][Link 1]

[Part 1 java基础][Part 1 java]

[1.1. JDK的基本的类库][1.1. JDK]

[1.2. java虚拟机相关知识点][1.2. java]

[1.4. Unsafe类][1.4. Unsafe]

[1.5. Session、cookie区别][1.5. Session_cookie]

[1.6. 权限问题][1.6.]

[1.7. 安全问题][1.7.]

[1.8 HashMap实现原理][1.8 HashMap]

[1.9. HashMap和HashTable的区别][1.9. HashMap_HashTable]

[1.9. ArrayList和LinkedList的区别][1.9. ArrayList_LinkedList]

[1.10 设计模式 ][1.10 _]

[1.12 NIO][]

[Part 2 多线程问题][Part 2]

[多线程问题（必问）][Link 2]

[Concurrenthashmap原理分析有用][Concurrenthashmap]

[Volitile关键字的作用][Volitile]

[Part 3 Spring与ORM映射框架][Part 3 Spring_ORM]

[面试题集锦：][Link 3]

[3.1. Spring作用域][3.1. Spring]

[3.2. SpringMVC面试题][3.2. SpringMVC]

[SpringMVC的流程][SpringMVC]

[SpringMVC与Struts2的区别][SpringMVC_Struts2]

[过滤器与拦截器区别][Link 4]

[4.4. Mybatis面试题][4.4. Mybatis]

[\#\{\}和$\{\}的区别是什么？][Link 5]

[Xml映射文件中，除了常见的select|insert|updae|delete标签之外，还有哪些标签？][Xml_select_insert_updae_delete]

[Mybatis批量插入数据][Mybatis]

[Part 4 SpringBoot相关][Part 4 SpringBoot]

[Springboot面试题集锦：][Springboot]

[Springboot优点][Springboot 1]

[springboot核心注解][springboot]

[Spring Boot 支持哪些日志框架][Spring Boot]

[Part 5 数据库相关][Part 5]

[面试题集锦：][Link 3]

[什么是数据库约束,常见的约束有哪几种?][Link 6]

[乐观锁和悲观锁][Link 7]

[什么是事务][Link 8]

[事务四大特性][Link 9]

[事务的并发问题][Link 10]

[事务的隔离级别][Link 11]

[事务的传播行为][Link 12]

[sql编写以及优化（必问）][sql]

[Part 5 其它问题][Part 5 1]

[怎么设计一个高并发系统（比如100万人同时抢票）][100]

[Spring cloud（有些公司的项目用不到这个）][Spring cloud]

[java性能优化][java]

[java8的新特性][java8]

[redis相关问题][redis]

[linux常用命令][linux]

# 前言 

这篇文章是我近期面试遇到的一些问题（技术类），做的一个总结。

注意：

1.有些问题没有标准答案，需要读者朋友在网上查找。

2.有些问题答案太长，三言两语道不尽，直接给了链接，可以复制链接去搜索。

3.有些技术涉及到很多问题，我给了总结的链接之后，又给大家列出了我面试遇到的题目

# Part 1 java基础 

## 1.1. JDK的基本的类库 

存放位置：jre/lib/rt.jar（这个问题很简单，但是很多人答不上来！）

常用的 Java 核心包 (Java Core Package)

java.lang Java 编程语言的基本类库（Object、String、StringBuffer、StringBuilder、System、Runtime、Throwable、Exception、Error、Thread、Math等）

java.io 通过数据流、对象序列以及文件系统实现的系统输入、输出

java.util 集合类、时间处理模式、日期时间工具等各类常用工具包

其它还有

java.sql 访问和处理来自于 Java 标准数据源数据的类

java.test 以一种独立于自然语言的方式处理文本、日期、数字和消息的类和接口

java.math简明的整数算术以及十进制算术的基本函数

## 1.2. java虚拟机相关知识点 

[https://shuhao.blog.csdn.net/article/details/113524369][https_shuhao.blog.csdn.net_article_details_113524369]

## 1.4. Unsafe类 

参考：[https://www.cnblogs.com/thomas12112406/p/6510787.html][https_www.cnblogs.com_thomas12112406_p_6510787.html]

Unsafe类使Java拥有了像C语言的指针一样操作内存空间的能力，同时也带来了指针的问题

## 1.5. Session、cookie区别 

1、cookie数据存放在客户端，session数据放在服务器上。

2、cookie不是很安全，别人可以分析存放在本地的cookie并进行cookie欺骗，考虑到安全应当使用session。

3、session会在一定时间内保存在服务器上，当访问增多，会比较占用你服务器的性能，考虑性能应当使用cookie。

4、不同浏览器对cookie的数据大小限制不同，个数限制也不相同。

5、可以考虑将登陆信息等重要信息存放为session，不重要的信息可以放在cookie中。

## 1.6. 权限问题 

参考：[https://blog.csdn.net/Y0Q2T57s/article/details/82921270][https_blog.csdn.net_Y0Q2T57s_article_details_82921270]

## 1.7. 安全问题 

参考：[https://blog.csdn.net/wojiushiwo945you/article/details/77920165][https_blog.csdn.net_wojiushiwo945you_article_details_77920165]

shiro：[https://www.cnblogs.com/WUXIAOCHANG/p/10886534.html][https_www.cnblogs.com_WUXIAOCHANG_p_10886534.html]

[https://blog.csdn.net/qq\_37254736/article/details/99350029][https_blog.csdn.net_qq_37254736_article_details_99350029]

## 1.8 HashMap实现原理 

参考：[https://shuhao.blog.csdn.net/article/details/108733952][https_shuhao.blog.csdn.net_article_details_108733952]

## 1.9. HashMap和HashTable的区别 

1.HashMap继承了AbstractMap，HashTable继承Dictionary抽象类，两者均实现Map接口

2.HashMap和Hashtable的底层实现都是数组+链表结构实现、

3.Hashtable是线程安全，HashMap是非线程安全

HashMap的性能会高于Hashtable，我们平时使用时若无特殊需求建议使用HashMap，在多线程环境下若使用HashMap需要使用Collections.synchronizedMap()方法来获取一个线程安全的集合（Collections.synchronizedMap()实现原理是Collections定义了一个SynchronizedMap的内部类，这个类实现了Map接口，在调用方法时使用synchronized来保证线程同步

4.两者计算hash的方法不同：

Hashtable计算hash是直接使用key的hashcode对table数组的长度直接进行取模

HashMap计算hash对key的hashcode进行了二次hash，以获得更好的散列值，然后对table数组长度取摸

5.HashMap可以使用null作为key，不过建议还是尽量避免这样使用。HashMap以null作为key时，总是存储在table数组的第一个节点上。而Hashtable则不允许null作为key

6.HashMap的初始容量为16，Hashtable初始容量为11，两者的填充因子默认都是0.75

7.HashMap扩容时是当前容量翻倍即:capacity2，Hashtable扩容时是容量翻倍+1即:capacity2+1

## 1.9. ArrayList和LinkedList的区别 

1、ArrayList和LinkedList可想从名字分析，它们一个是Array(动态数组)的数据结构，一个是Link(链表)的数据结构，此外，它们两个都是对List接口的实现。

前者是数组队列，相当于动态数组；后者为双向链表结构，也可当作堆栈、队列、双端队列

2、当随机访问List时（get和set操作），ArrayList比LinkedList的效率更高，因为LinkedList是线性的数据存储方式，所以需要移动指针从前往后依次查找。

3、当对数据进行增加和删除的操作时(add和remove操作)，LinkedList比ArrayList的效率更高，因为ArrayList是数组，所以在其中进行增删操作时，会对操作点之后所有数据的下标索引造成影响，需要进行数据的移动。

4、从利用效率来看，ArrayList自由性较低，因为它需要手动的设置固定大小的容量，但是它的使用比较方便，只需要创建，然后添加数据，通过调用下标进行使用；而LinkedList自由性较高，能够动态的随数据量的变化而变化，但是它不便于使用。

5、ArrayList主要控件开销在于需要在lList列表预留一定空间；而LinkList主要控件开销在于需要存储结点信息以及结点指针信息。

## 1.10 设计模式  

参考：[http://c.biancheng.net/design\_pattern/][http_c.biancheng.net_design_pattern]

## 1.12 NIO 

[Java中的IO与NIO面试题][Java_IO_NIO]

# Part 2 多线程问题 

## 多线程问题（必问） 

多线程的面试题太多了，下面的三个链接总结的很全面很详细：

[https://www.cnblogs.com/Jansens520/p/8624708.html][https_www.cnblogs.com_Jansens520_p_8624708.html]

[https://www.cnblogs.com/wangjintao-0623/p/9727234.html][https_www.cnblogs.com_wangjintao-0623_p_9727234.html]

[https://blog.csdn.net/zl1zl2zl3/article/details/81868173][https_blog.csdn.net_zl1zl2zl3_article_details_81868173]

## Concurrenthashmap原理分析有用 

HashTable容器在竞争激烈的并发环境下表现出效率低下的原因，是因为所有访问HashTable的线程都必须竞争同一把锁，那假如容器里有多把锁，每一把锁用于锁容器其中一部分数据，那么当多线程访问容器里不同数据段的数据时，线程间就不会存在锁竞争，从而可以有效的提高并发访问效率，这就是ConcurrentHashMap所使用的锁分段技术，首先将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问。

## Volitile关键字的作用 

参考：[https://www.cnblogs.com/xd502djj/p/9873067.html][https_www.cnblogs.com_xd502djj_p_9873067.html]

# Part 3 Spring与ORM映射框架 

## 面试题集锦： 

[https://blog.csdn.net/qq\_34337272/article/details/90955736][https_blog.csdn.net_qq_34337272_article_details_90955736]

## 3.1. Spring作用域 

singleton：单例模式，当spring创建applicationContext容器的时候，spring会欲初始化所有的该作用域实例，加上lazy-init就可以避免预处理；

prototype：原型模式，每次通过getBean获取该bean就会新产生一个实例，创建后spring将不再对其管理；

====下面是在web项目下才用到的===

request：搞web的大家都应该明白request的域了吧，就是每次请求都新产生一个实例，和prototype不同就是创建后，接下来的管理，spring依然在监听；

session：每次会话，同上；

global session：全局的web域，类似于servlet中的application。

## 3.2. SpringMVC面试题 

面试题集锦：

[https://blog.csdn.net/a745233700/article/details/80963758][https_blog.csdn.net_a745233700_article_details_80963758]

## SpringMVC的流程 

![image_0aae936f.png](http://markdown.liangtengyu.com:9999/images//image_0aae936f.png)![image_b8bfb6ea.png](http://markdown.liangtengyu.com:9999/images//image_b8bfb6ea.png)

## SpringMVC与Struts2的区别 

（1）springmvc的入口是一个servlet即前端控制器（DispatchServlet），而struts2入口是一个filter过虑器（StrutsPrepareAndExecuteFilter）。

（2）springmvc是基于方法开发(一个url对应一个方法)，请求参数传递到方法的形参，可以设计为单例或多例(建议单例)，struts2是基于类开发，传递参数是通过类的属性，只能设计为多例。

（3）Struts采用值栈存储请求和响应的数据，通过OGNL存取数据，springmvc通过参数解析器是将request请求内容解析，并给方法形参赋值，将数据和视图封装成ModelAndView对象，最后又将ModelAndView中的模型数据通过reques域传输到页面。Jsp视图解析器默认使用jstl

## 过滤器与拦截器区别 

①过滤器只能在容器初始化时被调用一次，在action的生命周期中，而拦截器可以多次被调用

②过滤器可以对几乎所有的请求起作用，拦截器只能对action请求起作用。

③过滤器不能访问action上下文、值栈里的对象，而拦截器可以访问。

④过滤器依赖于servlet容器，而拦截器不依赖于servlet容器。

⑤过滤器是基于函数回调，而拦截器是基于java的反射机制的。

⑥过滤器不能获取IOC容器中的各个bean，而拦截器可以，这点很重要，在拦截器里注入一个service，可以调用业务逻辑。

## 4.4. Mybatis面试题 

面试题集锦：

[https://blog.csdn.net/a745233700/article/details/80977133][https_blog.csdn.net_a745233700_article_details_80977133]

## \#\{\}和$\{\}的区别是什么？ 

\#\{\}是预编译处理，$\{\}是字符串替换。

Mybatis在处理\#\{\}时，会将sql中的\#\{\}替换为?号，调用PreparedStatement的set方法来赋值；

Mybatis在处理$\{\}时，就是把$\{\}替换成变量的值。

使用\#\{\}可以有效的防止SQL注入，提高系统安全性。

## Xml映射文件中，除了常见的select|insert|updae|delete标签之外，还有哪些标签？ 

trim | where | set | foreach | if | choose | when | otherwise | bind

## Mybatis批量插入数据 

可以使用foreach标签

# Part 4 SpringBoot相关 

## Springboot面试题集锦： 

[https://blog.csdn.net/ThinkWon/article/details/104397299][https_blog.csdn.net_ThinkWon_article_details_104397299]

[https://www.jianshu.com/p/14ef39ed8ad3][https_www.jianshu.com_p_14ef39ed8ad3]

[https://segmentfault.com/a/1190000016686735][https_segmentfault.com_a_1190000016686735]

## Springboot优点 

内置Tomcat、jetty，简化配置配置，restful风格

## springboot核心注解 

SpringbootApplication，包含3个配置springbootConfiguration、enableAutoConfiguration、componentScan

## Spring Boot 支持哪些日志框架 

Spring Boot支持Java Util Logging,Log4j2,Lockback作为日志框架，如果你使用starters启动器，Spring Boot将使用Logback作为默认日志框架。无论使用哪种日志框架，Spring Boot都支持配置将日志输出到控制台或者文件中

# Part 5 数据库相关 

## 面试题集锦： 

[https://www.cnblogs.com/wenxiaofei/p/9853682.html][https_www.cnblogs.com_wenxiaofei_p_9853682.html]

## 什么是数据库约束,常见的约束有哪几种? 

数据库约束用于保证数据库表数据的完整性（正确性和一致性）。可以通过定义约束\\索引\\触发器来保证数据的完整性。

总体来讲,约束可以分为:

主键约束：primary key；

外键约束：foreign key；

唯一约束：unique；

检查约束：check；

空值约束：not null；

默认值约束：default；

## 乐观锁和悲观锁 

参考：[https://blog.csdn.net/qq\_34337272/article/details/81072874][https_blog.csdn.net_qq_34337272_article_details_81072874]

## 什么是事务 

事务是对数据库中一系列操作进行统一的回滚或者提交的操作，主要用来保证数据的完整性和一致性。

## 事务四大特性 

原子性（Atomicity）: 原子性是指事务包含的所有操作要么全部成功，要么全部失败回滚，因此事务的操作如果成功就必须要完全应用到数据库，如果操作失败则不能对数据库有任何影响。

一致性（Consistency）: 事务开始前和结束后，数据库的完整性约束没有被破坏。比如A向B转账，不可能A扣了钱，B却没收到。

隔离性（Isolation）: 隔离性是当多个用户并发访问数据库时，比如操作同一张表时，数据库为每一个用户开启的事务，不能被其他事务的操作所干扰，多个并发事务之间要相互隔离。同一时间，只允许一个事务请求同一数据，不同的事务之间彼此没有任何干扰。比如A正在从一张银行卡中取钱，在A取钱的过程结束前，B不能向这张卡转账。

持久性（Durability）: 持久性是指一个事务一旦被提交了，那么对数据库中的数据的改变就是永久性的，即便是在数据库系统遇到故障的情况下也不会丢失提交事务的操作。

## 事务的并发问题 

1、脏读：事务A读取了事务B更新的数据，然后B回滚操作，那么A读取到的数据是脏数据

2、不可重复读：事务 A 多次读取同一数据，事务 B 在事务A多次读取的过程中，对数据作了更新并提交，导致事务A多次读取同一数据时，结果因此本事务先后两次读到的数据结果会不一致。

3、幻读：幻读解决了不重复读，保证了同一个事务里，查询的结果都是事务开始时的状态（一致性）。

例如：事务T1对一个表中所有的行的某个数据项做了从“1”修改为“2”的操作 这时事务T2又对这个表中插入了一行数据项，而这个数据项的数值还是为“1”并且提交给数据库。而操作事务T1的用户如果再查看刚刚修改的数据，会发现还有跟没有修改一样，其实这行是从事务T2中添加的，就好像产生幻觉一样，这就是发生了幻读。

## 事务的隔离级别 

1. 读未提交：另一个事务修改了数据，但尚未提交，而本事务中的SELECT会读到这些未被提交的数据脏读

2. 不可重复读：事务 A 多次读取同一数据，事务 B 在事务A多次读取的过程中，对数据作了更新并提交，导致事务A多次读取同一数据时，结果因此本事务先后两次读到的数据结果会不一致。

3. 可重复读：在同一个事务里，SELECT的结果是事务开始时时间点的状态，因此，同样的SELECT操作读到的结果会是一致的。但是，会有幻读现象

4. 串行化：最高的隔离级别，在这个隔离级别下，不会产生任何异常。并发的事务，就像事务是在一个个按照顺序执行一样

## 事务的传播行为 

1.PROPAGATION\_REQUIRED：如果当前没有事务，就创建一个新事务，如果当前存在事务，就加入该事务，该设置是最常用的设置。

2.PROPAGATION\_SUPPORTS：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就以非事务执行。

3.PROPAGATION\_MANDATORY：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就抛出异常。

4.PROPAGATION\_REQUIRES\_NEW：创建新事务，无论当前存不存在事务，都创建新事务。

5.PROPAGATION\_NOT\_SUPPORTED：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。

6.PROPAGATION\_NEVER：以非事务方式执行，如果当前存在事务，则抛出异常。

7.PROPAGATION\_NESTED：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与PROPAGATION\_REQUIRED类似的操作。

## sql编写以及优化（必问） 

参考：

[https://blog.csdn.net/qq\_38789941/article/details/83744271][https_blog.csdn.net_qq_38789941_article_details_83744271]

# Part 5 其它问题 

## 怎么设计一个高并发系统（比如100万人同时抢票） 

[https://blog.csdn.net/java\_leejin/article/details/98096274][https_blog.csdn.net_java_leejin_article_details_98096274]

## Spring cloud（有些公司的项目用不到这个） 

面试题集锦：

[https://blog.csdn.net/hjq\_ku/article/details/89504229][https_blog.csdn.net_hjq_ku_article_details_89504229]

[https://blog.csdn.net/oldshaui/article/details/90675149][https_blog.csdn.net_oldshaui_article_details_90675149]

![image_63a70387.png](http://markdown.liangtengyu.com:9999/images//image_63a70387.png)![image_4b7e356a.png](http://markdown.liangtengyu.com:9999/images//image_4b7e356a.png)

## java性能优化 

[http://www.wityx.com/post/424\_1\_1.html][http_www.wityx.com_post_424_1_1.html]

## java8的新特性 

[https://www.cnblogs.com/onetwo/p/8526374.html][https_www.cnblogs.com_onetwo_p_8526374.html]

## redis相关问题 

[https://www.cnblogs.com/jasontec/p/9699242.html][https_www.cnblogs.com_jasontec_p_9699242.html]

## linux常用命令 

[https://blog.csdn.net/qq\_26230421/article/details/98359696][https_blog.csdn.net_qq_26230421_article_details_98359696]

这些技术都会的话就可以上战场了
