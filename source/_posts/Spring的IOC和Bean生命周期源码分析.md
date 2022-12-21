---
title: Spring的IOC和Bean生命周期源码分析
top: false
cover: false
toc: true
mathjax: true
summary: Spring的IOC和Bean生命周期源码分析
date: 2022-12-21 20:25:34
password:
tags:
    - spring ioc
    - spring bean生命周期
    - java面试
categories:
    - java
---
这篇文章主要讲解 IOC 容器的创建过程，让你对整体有一个全局的认识，文章没有复杂嵌套的 debug 流程，相对来说比较简单。

不 BB，上文章目录。

![image_e1e6c5da.png](Spring的IOC和Bean生命周期源码分析/image_e1e6c5da.png)

## 1. 基础知识 

### 1.1 什么是 Spring IOC ？ 

IOC 不是一种技术，只是一种思想，一个重要的面向对象编程的法则，它能指导我们如何设计出松耦合、更优良的程序。

传统应用程序都是由我们在类内部主动创建依赖对象，从而导致类与类之间高耦合，难于测试。

有了 IOC 容器后，把创建和查找依赖对象的控制权交给了容器，由容器进行注入组合对象，所以对象与对象之间是松散耦合，便于测试和功能复用，整个体系结构更加灵活。

理解 IOC 的关键是要明确 “谁控制谁，控制什么，为何是反转（有反转就应该有正转了），哪些方面反转了”，我们浅析一下：

 * 谁控制谁，控制什么：

 * 传统 Java SE 程序设计，我们直接在对象内部通过 new 进行创建对象，是程序主动去创建依赖对象；

   IOC 是有专门一个容器来创建这些对象，即由 IOC 容器来控制对象的创建；

   谁控制谁？当然是 IOC 容器控制了对象；

   控制什么？主要控制了外部资源获取。

 * 为何反转，哪些方面反转：

 * 传统应用程序是由我们自己在对象中主动控制去直接获取依赖对象，也就是正转；

   反转则是由容器来帮忙创建及注入依赖对象；

   为何是反转？因为由容器帮我们查找及注入依赖对象，对象只是被动的接受依赖对象，所以是反转；

   哪些方面反转了？依赖对象的获取被反转了。

### 1.2 容器创建过程 

在看源码之前，首先搞清楚 Spring IOC 容器的创建过程，然后再深入源码。

IOC 容器如婚姻介绍所：

 *  男人 A 要求需要一个女人，中介从婚姻介绍所根据男人 A 给他介绍了如花；
 *  男人 B 要求介绍一个肤白貌美大长腿，中介从婚姻介绍所根据男人 B 给他介绍了女神。

![image_ed4f1468.png](Spring的IOC和Bean生命周期源码分析/image_ed4f1468.png)

从婚姻介绍所的例子可以大概知道 Spring IOC 是存储、定制、管理等功能的各种定制化的 bean 对象容器，下图是 Spring IOC 容器创建基本流程。

![image_6896031e.png](Spring的IOC和Bean生命周期源码分析/image_6896031e.png)

## 2. 核心知识 

### 2.1 相关对象 

#### 2.1.1 ApplicationContext 

ApplicationContext 接口是 BeanFactory 的子接口，也被称为 Spring 上下文，与 BeanFactory 一样，可以加载配置文件中定义的 bean，并进行管理。

它还加强了企业所需要的功能，如从属性文件中解析文本信息和将事件传递给所有指定的监视器，下图是 ApplicationContext 接口的继承关系。

![image_ec03e23f.png](Spring的IOC和Bean生命周期源码分析/image_ec03e23f.png)

ApplicationContext 接口主要的 5 个作用如表所示：

![image_921b4745.png](Spring的IOC和Bean生命周期源码分析/image_921b4745.png)

#### 2.1.2 BeanDefinitionReader 

```java
// 示例
ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
```

配置文件解析器，如将配置文件中的 bean 信息解析成对应的 BeanDefinition 对象。

![image_d2b17718.png](Spring的IOC和Bean生命周期源码分析/image_d2b17718.png)

xml 文件解析使用是就是BeanDefinitionReader 实现类 XmlBeanDefinitionReader。

#### 2.1.3 BeanFactoryPostProcessor 

可以修改 Spring 上下文中 BeanDefinition 信息。

如下图 BeanFactoryPostProcessor 的子类 PropertySourcesPlaceholderConfigurer 的作用可以为数据库连接池 $\{\} 占位符赋值等等。

![image_d00fcfb5.png](Spring的IOC和Bean生命周期源码分析/image_d00fcfb5.png)

#### 2.1.4 BeanFactory 

是所有 Bean 容器的根接口，定义了 spring 容器基本方法。

如使用 getBean(beanName,Class) 获取对象。

![image_8f9c91ca.png](Spring的IOC和Bean生命周期源码分析/image_8f9c91ca.png)

### 2.2 源码核心流程 

容器初始化的核心源码，都在 refresh() 方法中：

```java
public void refresh() throws BeansException, IllegalStateException {
        synchronized (this.startupShutdownMonitor) {
            //1:准备刷新上下文环境
            prepareRefresh();

            //2:获取初始化Bean工厂
            ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

            //3:对bean工厂进行填充属性
            prepareBeanFactory(beanFactory);

            try {
                //4:Spring开放接口 留给子类去实现该接口
                postProcessBeanFactory(beanFactory);

                //5:调用我们的bean工厂的后置处理器
                invokeBeanFactoryPostProcessors(beanFactory);

                //6:注册我们bean后置处理器
                registerBeanPostProcessors(beanFactory);

                //7:初始化国际化资源处理器
                initMessageSource();

                //8:初始化事件多播器
                initApplicationEventMulticaster();

                //9:这个方法同样也是留个子类实现,其中springboot也是从这个方法进行tomcat的启动
                onRefresh();

                //10:把我们的事件监听器注册到多播器上
                registerListeners();

                //11:实例化所有的非懒加载的单实例bean
                finishBeanFactoryInitialization(beanFactory);

                //12:最后刷新容器 发布刷新事件(Spring cloud eureka也是从这里启动的)
                finishRefresh();
            }

            catch (BeansException ex) {
                if (logger.isWarnEnabled()) {
                    logger.warn("Exception encountered during context initialization - " +
                            "cancelling refresh attempt: " + ex);
                }

                // Destroy already created singletons to avoid dangling resources.
                destroyBeans();

                // Reset 'active' flag.
                cancelRefresh(ex);

                // Propagate exception to caller.
                throw ex;
            }

            finally {
                // Reset common introspection caches in Spring's core, since we
                // might not ever need metadata for singleton beans anymore...
                resetCommonCaches();
            }
        }
    }
```

什么，内容不够精炼？那我再给大家上一个牛逼的思维导图：

![image_10ea4763.png](Spring的IOC和Bean生命周期源码分析/image_10ea4763.png)

## 3. 源码解析 

下面主要对 refresh() 的 12 个流程进行讲解。

### 3.1 prepareRefresh() 

准备刷新上下文环境：

```java
protected void prepareRefresh() {
        // Switch to active.
        this.startupDate = System.currentTimeMillis();
        this.closed.set(false);
        this.active.set(true);

        if (logger.isInfoEnabled()) {
            logger.info("Refreshing " + this);
        }

        /**
         * 初始化上下文环境
         */
        initPropertySources();

        /**
         * 用来校验我们容器启动必须依赖的环境变量的值
         */
        getEnvironment().validateRequiredProperties();

        /**
         * 创建一个早期事件监听器对象
         */
        if (this.earlyApplicationListeners == null) {
            this.earlyApplicationListeners = new LinkedHashSet<>(this.applicationListeners);
        }
        else {
            // Reset local application listeners to pre-refresh state.
            this.applicationListeners.clear();
            this.applicationListeners.addAll(this.earlyApplicationListeners);
        }

        /**
         * 创建一个容器用于保存早期待发布的事件集合 什么是早期事件了?
         * 就是我们的事件监听器还没有注册到事件多播器上的时候都称为早期事件
         */
        this.earlyApplicationEvents = new LinkedHashSet<>();
    }
```

### 3.2 ConfigurableListableBeanFactory() 

获取初始化的 Bean 的工厂：

```java
protected ConfigurableListableBeanFactory obtainFreshBeanFactory() {
        //刷新bean工厂()
        refreshBeanFactory();
        //返回之前容器准备工作的时候创建的的bean工厂也就是DefaultListableBeanFactory（很重要）
        ConfigurableListableBeanFactory beanFactory = getBeanFactory();
        if (logger.isDebugEnabled()) {
            logger.debug("Bean factory for " + getDisplayName() + ": " + beanFactory);
        }
        return beanFactory;
    }
```

```java
protected final void refreshBeanFactory() throws IllegalStateException {
        //由于BeanFactory只能刷新一次，多线程情况下可能导致线程安全问题，所有使用cas原子操作来保证
        if (!this.refreshed.compareAndSet(false, true)) {
            throw new IllegalStateException(
                    "GenericApplicationContext does not support multiple refresh attempts: just call 'refresh' once");
        }
        //指定Bean工厂的序列化Id
        this.beanFactory.setSerializationId(getId());
    }
```

### 3.3 prepareBeanFactory() 

对 Bean 工厂进行填充属性：

```java
protected void prepareBeanFactory(ConfigurableListableBeanFactory beanFactory) {
        //设置bean工厂的类加载器为当前application应用上下文的加载器
        beanFactory.setBeanClassLoader(getClassLoader());
        //为bean工厂设置SPEL表达式解析器对象StandardBeanExpressionResolver
        beanFactory.setBeanExpressionResolver(new StandardBeanExpressionResolver(beanFactory.getBeanClassLoader()));
        //为我们的bean工厂设置了一个propertyEditor属性资源编辑器对象(用于后面的给bean对象赋值使用)
        beanFactory.addPropertyEditorRegistrar(new ResourceEditorRegistrar(this, getEnvironment()));
        //注册ApplicationContextAwareProcessor后置处理器用来处理ApplicationContextAware接口的回调方法
        beanFactory.addBeanPostProcessor(new ApplicationContextAwareProcessor(this));
        /**
         * 当Spring将ApplicationContextAwareProcessor注册后，那么在invokeAwarelnterfaces方法中调用的Aware类已经不是普通的bean了 ，
         * 如ResourceLoaderAware、ApplicationEventPublisherAware、ApplicationContextAware等，那么当然需要在Spring做bean的依赖注入的时候忽略它们。
         * 这个就是ignoreDependencyInterface的作用
         */
        beanFactory.ignoreDependencyInterface(EnvironmentAware.class);
        beanFactory.ignoreDependencyInterface(EmbeddedValueResolverAware.class);
        beanFactory.ignoreDependencyInterface(ResourceLoaderAware.class);
        beanFactory.ignoreDependencyInterface(ApplicationEventPublisherAware.class);
        beanFactory.ignoreDependencyInterface(MessageSourceAware.class);
        beanFactory.ignoreDependencyInterface(ApplicationContextAware.class);
        /**
         * 注册了依赖解析，例如当注册BeanFactory.class的解析依赖后，
         * 当bean的属性注入的时候，一旦检测到属性为BeanFactory类型便会将beanFactory的实例注入进去。
         */
        beanFactory.registerResolvableDependency(BeanFactory.class, beanFactory);
        beanFactory.registerResolvableDependency(ResourceLoader.class, this);
        beanFactory.registerResolvableDependency(ApplicationEventPublisher.class, this);
        beanFactory.registerResolvableDependency(ApplicationContext.class, this);
        //注册了一个事件监听器探测器后置处理器接口
        beanFactory.addBeanPostProcessor(new ApplicationListenerDetector(this));
        if (beanFactory.containsBean(LOAD_TIME_WEAVER_BEAN_NAME)) {
            beanFactory.addBeanPostProcessor(new LoadTimeWeaverAwareProcessor(beanFactory));
            // Set a temporary ClassLoader for type matching.
            beanFactory.setTempClassLoader(new ContextTypeMatchClassLoader(beanFactory.getBeanClassLoader()));
        }
        if (!beanFactory.containsLocalBean(ENVIRONMENT_BEAN_NAME)) {
            beanFactory.registerSingleton(ENVIRONMENT_BEAN_NAME, getEnvironment());
        }
        if (!beanFactory.containsLocalBean(SYSTEM_PROPERTIES_BEAN_NAME)) {
            beanFactory.registerSingleton(SYSTEM_PROPERTIES_BEAN_NAME, getEnvironment().getSystemProperties());
        }
        if (!beanFactory.containsLocalBean(SYSTEM_ENVIRONMENT_BEAN_NAME)) {
            beanFactory.registerSingleton(SYSTEM_ENVIRONMENT_BEAN_NAME, getEnvironment().getSystemEnvironment());
        }
    }
```

### 3.4 postProcessBeanFactory() 

Spring 开放接口留给子类去实现该接口，主要用来改变 BeanFactory。

比如给 BeanFactory 添加一些自己的 BeanPostProcessor（Bean的后置处理器）

### 3.5 invokeBeanFactoryPostProcessors() 

调用我们的 bean 工厂的后置处理器，里面逻辑非常复杂，需要的同学可以和我联系，有详细讲解，就不扩展了。

### 3.6 registerBeanPostProcessors() 

注册我们 bean 后置处理器：

```java
/**
     * 往容器中注册了我们的bean的后置处理器
     * bean的后置处理器在什么时候进行调用？在bean的生命周期中
     * @param beanFactory
     * @param applicationContext
     */
    public static void registerBeanPostProcessors(
            ConfigurableListableBeanFactory beanFactory, AbstractApplicationContext applicationContext) {

        //去容器中获取所有的BeanPostProcessor的bean名称
        String[] postProcessorNames = beanFactory.getBeanNamesForType(BeanPostProcessor.class, true, false);

        /**
         * beanFactory.getBeanPostProcessorCount()获取的是已经添加在beanFactory的beanPostProcessors集合中的
         * postProcessorNames.length  beanFactory工厂中BeanPostProcessor个数 +1 又注册了BeanPostProcessorChecker的后置处理器
         */
        int beanProcessorTargetCount = beanFactory.getBeanPostProcessorCount() + 1 + postProcessorNames.length;
        beanFactory.addBeanPostProcessor(new BeanPostProcessorChecker(beanFactory, beanProcessorTargetCount));

        /**
         * 按照BeanPostProcessor实现的优先级接口来分离我们的后置处理器
         */
        //保存实现了priorityOrdered接口的
        List<BeanPostProcessor> priorityOrderedPostProcessors = new ArrayList<>();
        //容器内部的
        List<BeanPostProcessor> internalPostProcessors = new ArrayList<>();
        //实现了我们ordered接口的
        List<String> orderedPostProcessorNames = new ArrayList<>();
        //实现了我们任何优先级的
        List<String> nonOrderedPostProcessorNames = new ArrayList<>();
        //循环我们的bean定义(BeanPostProcessor)
        for (String ppName : postProcessorNames) {
            //若实现了PriorityOrdered接口的
            if (beanFactory.isTypeMatch(ppName, PriorityOrdered.class)) {
                //显示的调用getBean流程创建bean的后置处理器
                BeanPostProcessor pp = beanFactory.getBean(ppName, BeanPostProcessor.class);
                //加入到集合中
                priorityOrderedPostProcessors.add(pp);
                //判断是否实现了MergedBeanDefinitionPostProcessor
                if (pp instanceof MergedBeanDefinitionPostProcessor) {
                    //加入到集合中
                    internalPostProcessors.add(pp);
                }
            }
            //判断是否实现了Ordered
            else if (beanFactory.isTypeMatch(ppName, Ordered.class)) {
                orderedPostProcessorNames.add(ppName);
            }
            else {
                nonOrderedPostProcessorNames.add(ppName);
            }
        }

        //把实现了priorityOrdered注册到容器中
        sortPostProcessors(priorityOrderedPostProcessors, beanFactory);
        registerBeanPostProcessors(beanFactory, priorityOrderedPostProcessors);

        //处理实现Ordered的bean后置处理器
        List<BeanPostProcessor> orderedPostProcessors = new ArrayList<>();
        for (String ppName : orderedPostProcessorNames) {
            //显示调用getBean方法
            BeanPostProcessor pp = beanFactory.getBean(ppName, BeanPostProcessor.class);
            //加入到集合中
            orderedPostProcessors.add(pp);
            //判断是否实现了MergedBeanDefinitionPostProcessor
            if (pp instanceof MergedBeanDefinitionPostProcessor) {
                //加入到集合中
                internalPostProcessors.add(pp);
            }
        }
        //排序并且注册我们实现了Order接口的后置处理器
        sortPostProcessors(orderedPostProcessors, beanFactory);
        registerBeanPostProcessors(beanFactory, orderedPostProcessors);

        // 实例化我们所有的非排序接口的
        List<BeanPostProcessor> nonOrderedPostProcessors = new ArrayList<>();
        for (String ppName : nonOrderedPostProcessorNames) {
            //显示调用
            BeanPostProcessor pp = beanFactory.getBean(ppName, BeanPostProcessor.class);
            nonOrderedPostProcessors.add(pp);
            //判断是否实现了MergedBeanDefinitionPostProcessor
            if (pp instanceof MergedBeanDefinitionPostProcessor) {
                internalPostProcessors.add(pp);
            }
        }
        //注册我们普通的没有实现任何排序接口的
        registerBeanPostProcessors(beanFactory, nonOrderedPostProcessors);

        //注册MergedBeanDefinitionPostProcessor类型的后置处理器
        sortPostProcessors(internalPostProcessors, beanFactory);
        registerBeanPostProcessors(beanFactory, internalPostProcessors);

        //注册ApplicationListenerDetector应用监听器探测器的后置处理器
        beanFactory.addBeanPostProcessor(new ApplicationListenerDetector(applicationContext));
    }
```

### 3.7 initMessageSource() 

初始化国际化资源处理器：

```java
/**
     * Initialize the MessageSource.
     * Use parent's if none defined in this context.
     */
    protected void initMessageSource() {
        ConfigurableListableBeanFactory beanFactory = getBeanFactory();
        if (beanFactory.containsLocalBean(MESSAGE_SOURCE_BEAN_NAME)) {
            this.messageSource = beanFactory.getBean(MESSAGE_SOURCE_BEAN_NAME, MessageSource.class);
            // Make MessageSource aware of parent MessageSource.
            if (this.parent != null && this.messageSource instanceof HierarchicalMessageSource) {
                HierarchicalMessageSource hms = (HierarchicalMessageSource) this.messageSource;
                if (hms.getParentMessageSource() == null) {
                    // Only set parent context as parent MessageSource if no parent MessageSource
                    // registered already.
                    hms.setParentMessageSource(getInternalParentMessageSource());
                }
            }
            if (logger.isDebugEnabled()) {
                logger.debug("Using MessageSource [" + this.messageSource + "]");
            }
        }
        else {
            // Use empty MessageSource to be able to accept getMessage calls.
            DelegatingMessageSource dms = new DelegatingMessageSource();
            dms.setParentMessageSource(getInternalParentMessageSource());
            this.messageSource = dms;
            beanFactory.registerSingleton(MESSAGE_SOURCE_BEAN_NAME, this.messageSource);
            if (logger.isDebugEnabled()) {
                logger.debug("Unable to locate MessageSource with name '" + MESSAGE_SOURCE_BEAN_NAME +
                        "': using default [" + this.messageSource + "]");
            }
        }
    }
```

### 3.8 initApplicationEventMulticaster() 

初始化事件多播器：

```java
/**
     * 从bean工厂中获取或者直接显示的new一个事件多播器赋值给applicatoinContext对象的applicationEventMulticaster属性
     * 事件多播器采用典型的设计模式就是观察者模式 多播器作为的是一个被观察者
     * @see org.springframework.context.event.SimpleApplicationEventMulticaster
     */
    protected void initApplicationEventMulticaster() {
        //获取我们的bean工厂对象
        ConfigurableListableBeanFactory beanFactory = getBeanFactory();
        //判断容器中是否包含了applicationEventMulticaster事件多播器组件
        if (beanFactory.containsLocalBean(APPLICATION_EVENT_MULTICASTER_BEAN_NAME)) {
            //直接显示的调用我们的getBean获取出来赋值给我们的applicationContext对象
            this.applicationEventMulticaster =
                    beanFactory.getBean(APPLICATION_EVENT_MULTICASTER_BEAN_NAME, ApplicationEventMulticaster.class);
            if (logger.isDebugEnabled()) {
                logger.debug("Using ApplicationEventMulticaster [" + this.applicationEventMulticaster + "]");
            }
        }
        //容器中没有的话
        else {
            //直接new一个
            this.applicationEventMulticaster = new SimpleApplicationEventMulticaster(beanFactory);
            //并且注入到容器中
            beanFactory.registerSingleton(APPLICATION_EVENT_MULTICASTER_BEAN_NAME, this.applicationEventMulticaster);
            if (logger.isDebugEnabled()) {
                logger.debug("Unable to locate ApplicationEventMulticaster with name '" +
                        APPLICATION_EVENT_MULTICASTER_BEAN_NAME +
                        "': using default [" + this.applicationEventMulticaster + "]");
            }
        }
    }
```

### 3.9 onRefresh() 

这个方法很重要，同样也是留个子类实现，其中 Spring Boot 就是从这个方法进行 tomcat 的启动。

### 3.10 registerListeners() 

把我们的事件监听器注册到事件多播器上：

```java
protected void registerListeners() {
        //获取容器中所有的监听器对象
        for (ApplicationListener<?> listener : getApplicationListeners()) {
            //把监听器挨个的注册到我们的事件多播器上去
            getApplicationEventMulticaster().addApplicationListener(listener);
        }

        //获取bean定义中的监听器对象
        String[] listenerBeanNames = getBeanNamesForType(ApplicationListener.class, true, false);
        //把监听器的名称注册到我们的事件多播器上
        for (String listenerBeanName : listenerBeanNames) {
            getApplicationEventMulticaster().addApplicationListenerBean(listenerBeanName);
        }

        //在这里获取我们的早期事件
        Set<ApplicationEvent> earlyEventsToProcess = this.earlyApplicationEvents;
        this.earlyApplicationEvents = null;
        if (earlyEventsToProcess != null) {
            //通过多播器进行播发早期事件
            for (ApplicationEvent earlyEvent : earlyEventsToProcess) {
                getApplicationEventMulticaster().multicastEvent(earlyEvent);
            }
        }
    }
```

如何发布事件，通过事件多播器方法：multicastEvent()，进入方法。

```java
@Override
public void multicastEvent(ApplicationEvent event) {
    multicastEvent(event, resolveDefaultEventType(event));
}
```

```java
@Override
    public void multicastEvent(final ApplicationEvent event, @Nullable ResolvableType eventType) {
        ResolvableType type = (eventType != null ? eventType : resolveDefaultEventType(event));
        //从事件多播器中获取出所有的监听器
        for (final ApplicationListener<?> listener : getApplicationListeners(event, type)) {
            //判断多播器中是否支持异步多播的
            Executor executor = getTaskExecutor();
            if (executor != null) {
                //异步播发事件
                executor.execute(() -> invokeListener(listener, event));
            }
            else {//同步播发
                invokeListener(listener, event);
            }
        }
    }
```

```java
/**
     * Invoke the given listener with the given event.
     * @param listener the ApplicationListener to invoke
     * @param event the current event to propagate
     * @since 4.1
     */
    protected void invokeListener(ApplicationListener<?> listener, ApplicationEvent event) {
        ErrorHandler errorHandler = getErrorHandler();
        if (errorHandler != null) {
            try {
                doInvokeListener(listener, event);
            }
            catch (Throwable err) {
                errorHandler.handleError(err);
            }
        }
        else {
            doInvokeListener(listener, event);
        }
    }
```

doInvokeListener() 方法，Spring有一个特点，涉及到以 do 开头的方法都是真正干活的。

```java
 @SuppressWarnings({"rawtypes", "unchecked"})
 private void doInvokeListener(ApplicationListener listener, ApplicationEvent event) {
  try {
   listener.onApplicationEvent(event);
  }
  catch (ClassCastException ex) {
   String msg = ex.getMessage();
   if (msg == null || matchesClassCastMessage(msg, event.getClass())) {
    // Possibly a lambda-defined listener which we could not resolve the generic event type for
    // -> let's suppress the exception and just log a debug message.
    Log logger = LogFactory.getLog(getClass());
    if (logger.isTraceEnabled()) {
     logger.trace("Non-matching event type for listener: " + listener, ex);
    }
   }
   else {
    throw ex;
   }
  }
 }
```

### 3.11 finishBeanFactoryInitialization() 

实例化所有的非懒加载的单实例 bean，我们前几篇源码，其实都是讲的里面的方法，最重要的就是 getBean()。

### 3.12 finishRefresh() 

最后刷新容器发布刷新事件（Spring cloud eureka 也是从这里启动的）。

```java
protected void finishRefresh() {

        //初始化生命周期处理器
        initLifecycleProcessor();

        //调用生命周期处理器的onRefresh方法
        getLifecycleProcessor().onRefresh();

        //发布ContextRefreshedEvent事件
        publishEvent(new ContextRefreshedEvent(this));

        // Participate in LiveBeansView MBean, if active.
        LiveBeansView.registerApplicationContext(this);
    }
```

(1) 初始化生命周期处理器：initLifecycleProcessor() 方法。

```java
protected void initLifecycleProcessor() {
        //获取beanFactory工厂
        ConfigurableListableBeanFactory beanFactory = getBeanFactory();
        //判断容器中是否有lifecycleProcessor，有就直接从容器中拿
        if (beanFactory.containsLocalBean(LIFECYCLE_PROCESSOR_BEAN_NAME)) {
            this.lifecycleProcessor =
                    beanFactory.getBean(LIFECYCLE_PROCESSOR_BEAN_NAME, LifecycleProcessor.class);
            if (logger.isDebugEnabled()) {
                logger.debug("Using LifecycleProcessor [" + this.lifecycleProcessor + "]");
            }
        }
        //没有创建一个DefaultLifecycleProcessor，然后注册到容器中
        else {
            DefaultLifecycleProcessor defaultProcessor = new DefaultLifecycleProcessor();
            defaultProcessor.setBeanFactory(beanFactory);
            this.lifecycleProcessor = defaultProcessor;
            beanFactory.registerSingleton(LIFECYCLE_PROCESSOR_BEAN_NAME, this.lifecycleProcessor);
            if (logger.isDebugEnabled()) {
                logger.debug("Unable to locate LifecycleProcessor with name '" +
                        LIFECYCLE_PROCESSOR_BEAN_NAME +
                        "': using default [" + this.lifecycleProcessor + "]");
            }
        }
    }
```

(2) 调用生命周期处理器的 getLifecycleProcessor().onRefresh() 方法。

```java
@Override
public void onRefresh() {
    startBeans(true);
    this.running = true;
}
```

startBeans(true) 方法，主要的功能是找到 Spring 容器的 Lifecycle 类型的 Bean，然后调用 start() 去启动。

```java
private void startBeans(boolean autoStartupOnly) {
        Map<String, Lifecycle> lifecycleBeans = getLifecycleBeans();
        Map<Integer, LifecycleGroup> phases = new HashMap<Integer, LifecycleGroup>();
        for (Map.Entry<String, ? extends Lifecycle> entry : lifecycleBeans.entrySet()) {
            Lifecycle bean = entry.getValue();
            if (!autoStartupOnly || (bean instanceof SmartLifecycle && ((SmartLifecycle) bean).isAutoStartup())) {
                int phase = getPhase(bean);
                LifecycleGroup group = phases.get(phase);
                if (group == null) {
                    group = new LifecycleGroup(phase, this.timeoutPerShutdownPhase, lifecycleBeans, autoStartupOnly);
                    phases.put(phase, group);
                }
                group.add(entry.getKey(), bean);
            }
        }
        if (!phases.isEmpty()) {
            List<Integer> keys = new ArrayList<Integer>(phases.keySet());
            Collections.sort(keys);
            for (Integer key : keys) {
                phases.get(key).start();
            }
        }
    }
```

phases.get(key).start() 方法。

```java
  public void start() {
    if (this.members.isEmpty()) {
      return;
    }
    if (logger.isDebugEnabled()) {
      logger.debug("Starting beans in phase " + this.phase);
    }
    Collections.sort(this.members);
    for (LifecycleGroupMember member : this.members) {
      doStart(this.lifecycleBeans, member.name, this.autoStartupOnly);
    }
  }
```

```java
private void doStart(Map<String, ? extends Lifecycle> lifecycleBeans, String beanName, boolean autoStartupOnly) {
        Lifecycle bean = lifecycleBeans.remove(beanName);
        if (bean != null && bean != this) {
            String[] dependenciesForBean = getBeanFactory().getDependenciesForBean(beanName);
            for (String dependency : dependenciesForBean) {
                doStart(lifecycleBeans, dependency, autoStartupOnly);
            }
            //bean不在运行中并且(autoStartupOnly=false 或者 不是SmartLifecycle类型 或者 isAutoStartup() = true)
            if (!bean.isRunning() &&
                    (!autoStartupOnly || !(bean instanceof SmartLifecycle) || ((SmartLifecycle) bean).isAutoStartup())) {
                if (logger.isDebugEnabled()) {
                    logger.debug("Starting bean '" + beanName + "' of type [" + bean.getClass().getName() + "]");
                }
                try {
                    //调用生命周期Lifecycle Bean的start()方法
                    bean.start();
                }
                catch (Throwable ex) {
                    throw new ApplicationContextException("Failed to start bean '" + beanName + "'", ex);
                }
                if (logger.isDebugEnabled()) {
                    logger.debug("Successfully started bean '" + beanName + "'");
                }
            }
        }
    }
```

(3) 发布 ContextRefreshedEvent 事件：主要就是调用前面第八步创建的事件多播器的发布事件方法。

```java
protected void publishEvent(Object event, ResolvableType eventType) {
        Assert.notNull(event, "Event must not be null");
        if (logger.isTraceEnabled()) {
            logger.trace("Publishing event in " + getDisplayName() + ": " + event);
        }

        // Decorate event as an ApplicationEvent if necessary
        ApplicationEvent applicationEvent;
        if (event instanceof ApplicationEvent) {
            applicationEvent = (ApplicationEvent) event;
        }
        else {
            applicationEvent = new PayloadApplicationEvent<Object>(this, event);
            if (eventType == null) {
                eventType = ((PayloadApplicationEvent) applicationEvent).getResolvableType();
            }
        }

        // Multicast right now if possible - or lazily once the multicaster is initialized
        if (this.earlyApplicationEvents != null) {
            this.earlyApplicationEvents.add(applicationEvent);
        }
        else {
            getApplicationEventMulticaster().multicastEvent(applicationEvent, eventType);
        }

        // Publish event via parent context as well...
        if (this.parent != null) {
            if (this.parent instanceof AbstractApplicationContext) {
                ((AbstractApplicationContext) this.parent).publishEvent(event, eventType);
            }
            else {
                this.parent.publishEvent(event);
            }
        }
    }
```

## 4. 总结 

总结一下本文内容：

 *  讲述了 IOC 的基础知识，并通过婚介所的示例，讲解容器的创建过程；
 *  介绍容器创建的 4 个相关对象，以及 refresh() 的主流程；
 *  给出 refresh() 的 12 个主流程的核心源码，并附上详细注释。

大家主要掌握 IOC 容器创建的思想和过程，以及 refresh() 的 12 个主流程即可，至于里面每一步流程，如果深究下去，其实内容非常多，建议后续如果需要，再深入学习。