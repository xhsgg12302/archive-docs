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

            - ##### 03). prepareBeanFactory(beanFactory);

                > [?]
                ![](/.images/doc/framework/spring/00-spring-startup/ss-09.png ':size=60%')
                <br>准备/配置beanfactory为接下来的使用，配置一些标准的特性，比如上下文加载器和后置处理器。

            - ##### 04). postProcessBeanFactory(beanFactory);

            - ##### 05). invokeBeanFactoryPostProcessors(beanFactory);

            - ##### 06). registerBeanPostProcessors(beanFactory);

            - ##### 07). initMessageSource();

            - ##### 08). initApplicationEventMulticaster();

            - ##### 09). onRefresh();

            - ##### 10). registerListeners();

            - ##### 11). finishBeanFactoryInitialization(beanFactory);

            - ##### 12). finishRefresh();

    + ### 总结

* ## Reference

    + [Spring ioc 源码分析之--beanFactory.registerResolvableDependency(）方法](https://www.cnblogs.com/yangxiaohui227/p/13413980.html)