## 启动流程
* ### 流程图

    ![](/.images/doc/framework/springboot/process.png ':size=90%')
* ### 伪代码
    > main方法启动： `SpringApplication.run(SpringbootApplication.class, args);` <br>
    > new SpringApplication(sources).run(); <br>

    SpringApplication#init构造器中的初始化过程

    ```java
    private void initialize(Object[] sources) {
        if (sources != null && sources.length > 0) {
            this.sources.addAll(Arrays.asList(sources));
        }
        this.webEnvironment = deduceWebEnvironment();
        setInitializers((Collection) getSpringFactoriesInstances(
                ApplicationContextInitializer.class));
        setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
        this.mainApplicationClass = deduceMainApplicationClass();
    }
    ```

    SpringApplication#run
    ```java
    public ConfigurableApplicationContext run(String... args) {
        StopWatch stopWatch = new StopWatch();
        stopWatch.start();
        ConfigurableApplicationContext context = null;
        FailureAnalyzers analyzers = null;
        configureHeadlessProperty();
        SpringApplicationRunListeners listeners = getRunListeners(args);
        listeners.starting();
        try {
            ApplicationArguments applicationArguments = new DefaultApplicationArguments(
                    args);
            ConfigurableEnvironment environment = prepareEnvironment(listeners,
                    applicationArguments);
            Banner printedBanner = printBanner(environment);
            context = createApplicationContext();
            analyzers = new FailureAnalyzers(context);
            prepareContext(context, environment, listeners, applicationArguments,
                    printedBanner);
            refreshContext(context);
            afterRefresh(context, applicationArguments);
            listeners.finished(context, null);
            stopWatch.stop();
            if (this.logStartupInfo) {
                new StartupInfoLogger(this.mainApplicationClass)
                        .logStarted(getApplicationLog(), stopWatch);
            }
            return context;
        }
        catch (Throwable ex) {
            handleRunFailure(context, listeners, analyzers, ex);
            throw new IllegalStateException(ex);
        }
    }
    ```

## 自动装配

## Reference
* https://blog.csdn.net/weixin_44947701/article/details/124055713