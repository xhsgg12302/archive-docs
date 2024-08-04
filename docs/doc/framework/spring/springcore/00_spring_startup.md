* ## Intro(Startup Process)

    > [!] `spring.version = 4.3.7.RELEASE`
    
    ![](/.images/doc/framework/spring/00-spring-startup/ss-001.png ':size=100% :align=center')

    > [!TIP] `1.)` `ApplicationContext`继承了`BeanFactory`接口，在内部实现中采用委托的方式调用正真的beanfactory.

    + ### 解析流程

        > [?] `1).` 以`ClassPathXmlApplicationContext`为例，新建一个应用上下文：`new ClassPathXmlApplicationContext("_framework/spring/res/factory-bean.xml");`
        <br><span style='padding-left:2.8em' />![](/.images/doc/framework/spring/00-spring-startup/ss-01.png ':size=70%')
        <br><br>`2).` 紧接着通过`super(parent)`调用父类构造器，一直往上传，直到`AbstractApplicationContext`,调用自己无参构造器`this();`后，使用`setParent(parent);`设置父属性。
        <br><span style='padding-left:2.8em' />在`this()`内部获取一个`PathMatchingResourcePatternResolver()`.
        <br><br>`3).` 然后是设置配置文件位置`setConfigLocations(configLocations);`,对于每一个配置文件，都会通过`getEnvironment()`获取环境变量`StandardEnvironment`对象解决(~配置文件~)配置文件路径中的占位符。
        <br><br>`4).` 接下来进入主题`AbstractApplicationContext#refresh();`
        <br><span style='padding-left:2.8em' />![](/.images/doc/framework/spring/00-spring-startup/ss-02.png ':size=70%x550px')

        * #### refresh()

            - ##### 01). prepareRefresh()

                > [?]
                ![](/.images/doc/framework/spring/00-spring-startup/ss-03.png ':size=59%') ![](/.images/doc/framework/spring/00-spring-startup/ss-04.png ':size=34%')
                <br>对于刷新这个上下文做准备。记录启动时间，关闭状态，激活状态。
                <br>以及留给子类去初始化propertySource的方法`initPropertySources();`，
                <br>获取环境并校验通过`ConfigurablePropertyResolver#setRequiredProperties`设置的必需属性，委托给 **AbstractPropertyResolver** 去执行校验。
                <br>另外准备一个保存**应用事件**的set容器。

            - ##### 02). obtainFreshBeanFactory()

                > [?]
                ![](/.images/doc/framework/spring/00-spring-startup/ss-05.png ':size=49%')
                <br>获取一个新的bean工厂。
                <br><br>`protected abstract void refreshBeanFactory() throws BeansException, IllegalStateException;`由子类去处理。
                <br>![](/.images/doc/framework/spring/00-spring-startup/ss-06.png ':size=60%')
                <br><br>通过`createBeanFactory()`方法new一个 **DefaultListableBeanFactory** bean工厂
                <br><br>`customizeBeanFactory(beanFactory);`根据需要设置bean定义覆盖和循环引用的标志。
                <br>![](/.images/doc/framework/spring/00-spring-startup/ss-07.png ':size=60%')

                + ###### 02.1). loadBeanDefinitions

                    > [?] `loadBeanDefinitions(beanFactory);`加载bean定义，是抽象类`AbstractRefreshableApplicationContext`中定义的方法，由子类`AbstractXmlApplicationContext`去实现。
                    <br>![](/.images/doc/framework/spring/00-spring-startup/ss-08.png ':size=60%')
                    <br><br>创建beanDefinitionReader，传递benafactory给父类`AbstractBeanDefinitionReader`初始化 **resourceLoader** 和 **environment** 属性。但是在后续步骤中，使用了applicationContext的环境覆盖了自己刚才初始化的，包括ResourceLoader也一样。<span style='color:blue'>并且在reader对象中保存当前beanfactory，方便后续进行操作。</span>
                    <br><br>**initBeanDefinitionReader(beanDefinitionReader)** ，初始化reader属性，比如设置validate=true.
                    <br>**loadBeanDefinitions(beanDefinitionReader)** ，使用reader从配置文件中加载beandefinitions到beanfactory中。

            - ##### 03). prepareBeanFactory(beanFactory)

                > [?]
                ![](/.images/doc/framework/spring/00-spring-startup/ss-09.png ':size=60%')
                <br>准备/配置beanfactory为接下来的使用，配置一些标准的特性，比如上下文加载器和后置处理器。
                <br>`1).` 增加两个默认的bean后置处理器`ApplicationContextAwareProcessor`,`ApplicationListenerDetector`。
                <br>`2).` 其中的**resolvableDependency**用来注册一些默认类型的对象。例如有ApplicationContext类型的注入，当在population的时候通过`findAutowireCandidates()`方法中就可以使用到刚才默认的这些值了。

            - ##### 04). postProcessBeanFactory(beanFactory)

                > [?]
                ![](/.images/doc/framework/spring/00-spring-startup/ss-10.png ':size=60%')
                <br>允许子类修改应用上下文中的内部beanfactory在初始化完成之后。所有的beandefinitions已经加载完成，但是并没有实例化。所以此处子类可以适当添加一些bean后置处理器。

            - ##### 05). invokeBeanFactoryPostProcessors(beanFactory)

                > [?]
                ![](/.images/doc/framework/spring/00-spring-startup/ss-11.png ':size=60%')
                <br>调用beanfactory的后置处理器。当前上下文通过`getBeanFactoryPostProcessors()`获取到为0，所以忽略。但是mvc或者boot就不会为0了。

            - ##### 06). registerBeanPostProcessors(beanFactory)

                > [?]
                ![](/.images/doc/framework/spring/00-spring-startup/ss-12.png ':size=60%')
                <br>注册bean的后置处理器。当前上下文也为0，所以忽略。

            - ##### 07). initMessageSource()

                > [?]
                ![](/.images/doc/framework/spring/00-spring-startup/ss-13.png ':size=60%')
                <br>主要用来消息国际化，此处会实例化一个叫`MessageSource`类型的bean对象（如果配置的话）。[【demo参考】](https://github.com/12302-bak/idea-test-project/blob/d6602307b3f5c275e806fd61724c5d40d1cee3d2/_0_base-learning/src/main/java/_framework/spring/i18n/SpringI18nTest.java)

            - ##### 08). initApplicationEventMulticaster()

            - ##### 09). onRefresh()

            - ##### 10). registerListeners()

            - ##### 11). finishBeanFactoryInitialization(beanFactory)

            - ##### 12). finishRefresh()

    + ### 总结

* ## Reference

    + [Spring ioc 源码分析之--beanFactory.registerResolvableDependency(）方法](https://www.cnblogs.com/yangxiaohui227/p/13413980.html)
    + [Spring容器的启动流程](https://blog.csdn.net/a745233700/article/details/113761271)
    + [和小伙伴们仔细梳理一下 Spring 国际化吧！从用法到源码！](https://blog.csdn.net/u012702547/article/details/134570847)