## spring 是什么
> Spring是一个轻量级的IoC和AOP容器框架。是为Java应用程序提供基础性服务的一套框架，目的是用于简化企业应用程序的开发，它使得开发者只需要关心业务需求。

## spring 优点
> 低入侵，减少代码耦合，支持一些通用任务（鉴权，日志，事务等）的集中管理，对其他一些框架提供集成支持

## spring IOC 理解
* IOC
    > IOC(Inversion of control. 控制反转)，将bean对象的控制权交由spring去管理。
    > 其中包括bean对象的创建与销毁，bean对象之间的依赖关系等。简单来讲，就是对象不用我们去new了，而是框架帮我们使用反射的方式去创建。
    > 用户只需要在使用的时候注入即可。
* DI
    > DI (dependency inject ,依赖注入)，程序运行期间如果发现对象需要依赖其他的对象。就会通过反射的方式自动处理依赖关系。
    > 反射的功能就是在程序运行期间动态的生成对象，执行对象的方法，改变对象的属性等功能。

## spring AOP 理解
> AOP 面向切面编程。作为面向对象的一种补充，用于将那些与业务无关，但却对多个对象产生影响的公共行为和逻辑，抽取并封装为一个可重用的模块，这个模块被命名为“切面”（Aspect），减少系统中的重复代码，降低了模块间的耦合度，提高系统的可维护性。可用于权限认证、日志、事务处理。
> 编程中使用cglib和jdk两种动态代理来实现。

## BeanFactory 和 ApplicationContext区别
* MessageSource
    > 用于消息资源国际化
* ResourcePatternResolver 
    > 在上一篇中我们介绍了Resource接口，它的出现是为了解决spring方便访问各种配置信息。
    > 但Resource接口有一个问题，就是如果我们要访问同一个路径下的所有符合条件的配置，如果用Resource接口则会比较麻烦，因为它不支持通配符方式的文件读取，所以要将读取的配置文件名称全部写出来才可以。
    > spring为了解决这个问题，于是新定义了一个新接口，并实现了该接口特有的特性，也就是支持类似[Ant风格](https://ant.apache.org/manual/dirtasks.html#patterns)的通配符
* ApplicationEventPublisher
    > 时间发布器
* EnvironmentCapable
    > 提供环境处理能力，getEnvironment()返回的 Environment 不可配置。所以在ConfigurableApplicationContext重新定义getEnvironment()并缩小签名以返回一个ConfigurableEnvironment。在此之前 环境对象 是只读的。

## spring 容器启动流程
* 启动流程
    <details><summary>代码示例</summary>

    ```java
    @Override
    public void refresh() throws BeansException, IllegalStateException {
        synchronized (this.startupShutdownMonitor) {
            // Prepare this context for refreshing.
            // 准备这个上下文以便刷新，设置它的启动日期和活动标志，并执行PropertySource属性源的任何初始化。
            prepareRefresh();

            // Tell the subclass to refresh the internal bean factory.
                // DefaultListableBeanFactory beanFactory = createBeanFactory();
                // beanFactory.setSerializationId(getId());
                // customizeBeanFactory(beanFactory);
                // loadBeanDefinitions(beanFactory);
                    // 如果开启包扫描，会默认加载6个bd,(根据环境不同，可能数量有变，比如还可能有EJB的) 参见下图
                    // class org.springframework.context.annotation.ConfigurationClassPostProcessor
                    // class org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor
                    // class org.springframework.beans.factory.annotation.RequiredAnnotationBeanPostProcessor
                    // class org.springframework.context.annotation.CommonAnnotationBeanPostProcessor
                        // CommonAnnotationBeanPostProcessor 是根据 jsr250 加载的。
                        // private static final boolean jsr250Present = ClassUtils.isPresent("javax.annotation.Resource", AnnotationConfigUtils.class.getClassLoader());
                    // class org.springframework.context.event.DefaultEventListenerFactory
                    // class org.springframework.context.event.EventListenerMethodProcessor
                // return beanFactory;
            // 一般来说ApplicationContext自己没有实现BeanFactory相关方法，而是委托给自己刚才实例化的属性 beanFactory 来实现BeanFactory效果的。
            ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

            // Prepare the bean factory for use in this context.
            // 初始化 beanFactory 相关属性。
            prepareBeanFactory(beanFactory);

            try {
                // Allows post-processing of the bean factory in context subclasses.
                // 让子类继续处理 beanFactory 增加或修改属性。比如WebApplicationContext
                    // beanFactory.addBeanPostProcessor(new ServletContextAwareProcessor(this.servletContext, this.servletConfig));
                    // beanFactory.ignoreDependencyInterface(ServletContextAware.class);
                    // beanFactory.ignoreDependencyInterface(ServletConfigAware.class);
                    // WebApplicationContextUtils.registerWebApplicationScopes(beanFactory, this.servletContext);
                    // WebApplicationContextUtils.registerEnvironmentBeans(beanFactory, this.servletContext, this.servletConfig);
                postProcessBeanFactory(beanFactory);

                // Invoke factory processors registered as beans in the context.
                // 调用在上下文中已注册为bean的工厂处理器，来增加或修改属性。
                // 比如ConfigurationClassPostProcessor。创建了一个org.springframework.context.annotation.internalConfigurationAnnotationProcessor对象。
                // 且给beanFactory的属性：beanPostProcessors 增加了一个 ImportAwareBeanPostProcessor。
  
                // 1027追加注释 
                // 内部逻辑先找 实现了BeanDefinitionRegistryPostProcessor接口的 实例化然后按顺序进行接口调用。因为这个接口继承自BeanFactoryPostProcessor.所以还会对这个接口也进行调用。
                // 然后是其余的BeanFactoryPostProcessor 这个接口 实例化后再按照顺序进行接口调用
                invokeBeanFactoryPostProcessors(beanFactory);

                // Register bean processors that intercept bean creation.
                // 向容器中实例化BeanPostProcessor，并追加到beacFactory的属性：beanPostProcessors中。
  
                // 1027追加注释 
                // 和上面调用一样，不过这次找的是BeanPostProcessor接口，另外会追加属性 到 beanPostProcessors中。因为这个接口的功能是对Bean的创建进行拦截处理实例用的。而不是修改beanDefinition用的。
                registerBeanPostProcessors(beanFactory);

                // Initialize message source for this context.
                initMessageSource();

                // Initialize event multicaster for this context.
                initApplicationEventMulticaster();

                // Initialize other special beans in specific context subclasses.
                // 对于EmbedWebApplicationContext 调用 createEmbeddedServletContainer();
                onRefresh();

                // Check for listener beans and register them.
                registerListeners();

                // Instantiate all remaining (non-lazy-init) singletons.
                finishBeanFactoryInitialization(beanFactory);

                // Last step: publish corresponding event.
                finishRefresh();
            }
        // ...
        }
    }
    ```
    </details>

    ![](/.images/doc/framework/spring/xmlac-load-bd.png ':size=65%') 

## spring bean生命周期 [repo](https://github.com/xhsgg12302/idea-test-project/tree/master/_0_base-learning/src/main/java/_framework/spring/bean_lifecycle)

<table>
	<tr>
	    <td >step</td>
	    <td>invoke</td>
	    <td>invoke</td>
	    <td>output</td>  
	</tr >
	<tr >
	    <td>createBeanInstance</td>
        <td>-</td>
        <td>-</td>
	    <td> Book initializing </td>
	</tr>
    <tr >
	    <td>populateBean</td>
        <td>-</td>
        <td>-</td>
	    <td> setBookName: Book name has set.  </td>
	</tr>
    <tr >
	    <td rowspan="8">initializeBean</td>
        <td rowspan="2"> invokeAwareMethods</td>
        <td> BeanNameAware </td>
	    <td> BeanNameAware.setBeanName() invoke  </td>
	</tr>
    <tr >
        <td> BeanFactoryAware </td>
	    <td> BeanFactoryAware.setBeanFactory() invoke  </td>
	</tr>
	<tr>
	    <td rowspan="3">applyBeanPostProcessorsBeforeInitialization</td>
        <td>ApplicationContextAwareProcessor</td>
        <td>ApplicationContextAware.setApplicationContext() invoke</td>
	</tr>
	<tr>
        <td>MyBeanPostProcessor  implements BeanPostProcessor</td>
        <td>MyBeanPostProcessor.postProcessBeforeInitialization invoke</td>
	</tr>
	<tr>
        <td>CommonAnnotationBeanPostProcessor</td>
        <td>@PostConstruct</td>
	</tr>
	<tr>
        <td rowspan="2">invokeInitMethods</td>
	    <td>initializingBean</td>
        <td>initializingBean.afterPropertiesSet() invoke</td>
	</tr>
	<tr>
	    <td>invokeCustomInitMethod</td>
        <td>define constructor invoke</td>
	</tr>
	<tr>
	    <td>applyBeanPostProcessorsAfterInitialization</td>
	    <td>-</td>
        <td>MyBeanPostProcessor.postProcessAfterInitialization invoke</td>
	</tr>
    <tr>
        <td>using</td>
        <td colspan="3"></td>
    </tr>
    <tr>
        <td rowspan="3">destroy</td>
        <td>-</td>
        <td>@predestroy</td>
        <td>-</td>
    </tr>
    <tr>
        <td>-</td>
        <td>DisposableBean.destroy() invoke</td>
        <td>-</td>
    </tr>
    <tr>
        <td>-</td>
        <td>define destroy invoke</td>
        <td>-</td>
    </tr>
</table>



## spring 循环依赖

## spring 自动装配

## spring 自动刷新值


## Reference
* [Spring Bean的作用域(Scope)的原理及源码解析](https://blog.csdn.net/qq_27529917/article/details/78468731)
* [Spring为什么使用三级缓存而不是两级解决循环依赖问题？](https://www.51cto.com/article/747437.html)
* [@Scope注解的proxyMode的作用以及如何影响IoC容器的依赖查找](https://blog.csdn.net/m0_43448868/article/details/111643397)