* ## Intro(Startup Process)

    > [!] `spring.version = 4.3.7.RELEASE`

    + ### 解析流程

        > [?] `1).` 以`ClassPathXmlApplicationContext`为例，新建一个应用上下文：`new ClassPathXmlApplicationContext("_framework/spring/res/factory-bean.xml");`
        <br><span style='padding-left:2.8em' />![](/.images/doc/framework/spring/00-spring-startup/ss-01.png ':size=70%')
        <br><br>`2).` 紧接着通过`super(parent)`调用父类构造器，一直往上传，直到`AbstractApplicationContext`,调用自己无参构造器`this();`后，使用`setParent(parent);`设置父属性。
        <br><span style='padding-left:2.8em' />在`this()`内部获取一个`PathMatchingResourcePatternResolver()`.
        <br><br>`3).` 然后是设置配置文件位置`setConfigLocations(configLocations);`,对于每一个配置文件，都会通过`getEnvironment()`获取环境变量`StandardEnvironment`对象解决(~配置文件~)配置文件路径中的占位符。
        <br><br>`4).` 接下来进入主题`AbstractApplicationContext#refresh();`
        <br><span style='padding-left:2.8em' />![](/.images/doc/framework/spring/00-spring-startup/ss-02.png ':size=70%x550px')

        **refresh()**

        > [?] `01).` `prepareRefresh();`
        <br><span style='padding-left:3.6em' />![](/.images/doc/framework/spring/00-spring-startup/ss-03.png ':size=70%')
        <br><span style='padding-left:3.6em' />对于刷新这个上下文做准备。记录启动时间，关闭状态，激活状态。
        <br><span style='padding-left:3.6em' />以及留给子类去初始化propertySource的方法`initPropertySources();`，
        <br><span style='padding-left:3.6em' />获取环境并校验通过`ConfigurablePropertyResolver#setRequiredProperties`设置的必需属性，<br><span style='padding-left:3.6em' />另外准备一个保存**应用事件**的set容器。
        <br><br>`02).` `obtainFreshBeanFactory();`
        <br><br>`03).` `prepareBeanFactory(beanFactory);`
        <br><br>`04).` `postProcessBeanFactory(beanFactory);`
        <br><br>`05).` `invokeBeanFactoryPostProcessors(beanFactory);`
        <br><br>`06).` `registerBeanPostProcessors(beanFactory);`
        <br><br>`07).` `initMessageSource();`
        <br><br>`08).` `initApplicationEventMulticaster();`
        <br><br>`09).` `onRefresh();`
        <br><br>`10).` `registerListeners();`
        <br><br>`11).` `finishBeanFactoryInitialization(beanFactory);`
        <br><br>`12).` `finishRefresh();`

    + ### 总结

* ## Reference