---
title: 不推荐使用@Autowired注解
top: false
cover: false
toc: false
mathjax: true
date: 2022-11-04 17:13:35
password:
summary: Spring和IDEA都不推荐使用@Autowired 注解
tags:
    - ioc
    - java
categories:
    - java
---
大家在使用IDEA开发的时候有没有注意到过一个提示，在字段上使用Spring的依赖注入注解`@Autowired`后会出现如下警告  


> Field injection is not recommended (字段注入是不被推荐的)

但是使用`@Resource`却不会出现此提示

网上文章大部分都是介绍两者的区别，没有提到为什么，当时想了好久想出了可能的原因，今天来总结一下

## Spring常见的DI方式 

 *  构造器注入：利用构造方法的参数注入依赖
 *  Setter注入：调用Setter的方法注入依赖
 *  字段注入：在字段上使用`@Autowired/Resource`注解

### @Autowired VS @Resource 

事实上，他们的基本功能都是通过注解实现依赖注入，只不过`@Autowired`是`Spring`定义的，而`@Resource`是`JSR-250`定义的。大致功能基本相同，但是还有一些细节不同：

 *  依赖识别方式：`@Autowired`默认是byType可以使用`@Qualifier`指定Name，`@Resource`默认ByName如果找不到则ByType
 *  适用对象：`@Autowired`可以对构造器、方法、参数、字段使用，`@Resource`只能对方法、字段使用
 *  提供方：`@Autowired`是Spring提供的，`@Resource`是JSR-250提供的

## 各种DI方式的优缺点 

参考Spring官方文档，建议了如下的使用场景：

 *  构造器注入：强依赖性（即必须使用此依赖），不变性（各依赖不会经常变动）
 *  Setter注入：可选（没有此依赖也可以工作），可变（依赖会经常变动）
 *  Field注入：大多数情况下尽量少使用字段注入，一定要使用的话， @Resource相对@Autowired对IoC容器的耦合更低

## Field注入的缺点 

 *  不能像构造器那样注入不可变的对象
 *  依赖对外部不可见，外界可以看到构造器和setter，但无法看到私有字段，自然无法了解所需依赖
 *  会导致组件与IoC容器紧耦合（这是最重要的原因，离开了IoC容器去使用组件，在注入依赖时就会十分困难）
 *  导致单元测试也必须使用IoC容器，原因同上
 *  依赖过多时不够明显，比如我需要10个依赖，用构造器注入就会显得庞大，这时候应该考虑一下此组件是不是违反了单一职责原则

### 为什么IDEA只对@Autowired警告 

Field注入虽然有很多缺点，但它的好处也不可忽略：那就是太方便了。使用构造器或者setter注入需要写更多业务无关的代码，十分麻烦，而字段注入大幅简化了它们。并且绝大多数情况下业务代码和框架就是强绑定的，完全松耦合只是一件理想上的事，牺牲了敏捷度去过度追求松耦合反而得不偿失。

> 那么问题来了，为什么IDEA只对@Autowired警告，却对@Resource视而不见呢？

个人认为，就像我们前面提到过的：@Autowired是Spring提供的，它是特定IoC提供的特定注解，这就导致了应用与框架的强绑定，一旦换用了其他的IoC框架，是不能够支持注入的。

而 @Resource是JSR-250提供的，它是Java标准，我们使用的IoC容器应当去兼容它，这样即使更换容器，也可以正常工作。