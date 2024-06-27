* ## Intro(FactoryBean)

    > [?] `spring.version = 4.3.7.RELEASE`

    + ### 准备编码

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ```java
        // 创建普通bean
        @Data
        @NoArgsConstructor
        @AllArgsConstructor
        public class Tool {

            private int id;

        }

        // 创建工厂
        @Data
        public class ToolFactory implements FactoryBean<Tool> {

            private int factoryId;

            private int toolId;

            @Override
            public Tool getObject() throws Exception {
                return new Tool(toolId);
            }

            @Override
            public Class<?> getObjectType() {
                return Tool.class;
            }

            @Override
            public boolean isSingleton() {
                return false;
            }
        }
        ```
        <!-- div:right-panel-50 -->
        ```xml
        <!-- factory-bean.xml -->

        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

            <!-- FactoryBean work process -->
            <bean id="toolFB" class="_framework.spring.factory_bean.beans.ToolFactory">
                <property name="factoryId" value="9090" />
                <property name="toolId" value="2" />
            </bean>

        </beans>
        ```
        ```java
        public class SpringFactoryBeanTest {

            public static void main(String[] args) {

                ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("_framework/spring/res/factory-bean.xml");
                ToolFactory fb = applicationContext.getBean("&toolFB", ToolFactory.class);
                Tool tool1 = applicationContext.getBean(Tool.class);
                Tool tool2 = applicationContext.getBean(Tool.class);
                System.out.println();
                applicationContext.destroy();

            }

        }
        ```
        <!-- panels:end -->

    + ### 解析流程

        > [?] `1).` 初始化bean对象：在`refresh()`里面通过`obtainFreshBeanFactory()`方法创建扫描到beanDefinition的顶级工厂。然后通过方法`finishBeanFactoryInitialization(beanFactory);`实例化所有非懒加载的所有单例bean,此处就包含这个工厂bean对象。

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ```java
        @Override
	    public void preInstantiateSingletons() throws BeansException {
            if (this.logger.isDebugEnabled()) {
                this.logger.debug("Pre-instantiating singletons in " + this);
            }

            // Iterate over a copy to allow for init methods which in turn register new bean definitions.
            // While this may not be part of the regular factory bootstrap, it does otherwise work fine.
            List<String> beanNames = new ArrayList<String>(this.beanDefinitionNames);

            // Trigger initialization of all non-lazy singleton beans...
            for (String beanName : beanNames) {
                RootBeanDefinition bd = getMergedLocalBeanDefinition(beanName);
                if (!bd.isAbstract() && bd.isSingleton() && !bd.isLazyInit()) {
                    if (isFactoryBean(beanName)) {
                        // 此处进行工厂bean对象构造。就是类似于正常bean的创建。
                        final FactoryBean<?> factory = (FactoryBean<?>) getBean(FACTORY_BEAN_PREFIX + beanName);
                        boolean isEagerInit;
                        if (System.getSecurityManager() != null && factory instanceof SmartFactoryBean) {
                            isEagerInit = AccessController.doPrivileged(new PrivilegedAction<Boolean>() {
                                @Override
                                public Boolean run() {
                                    return ((SmartFactoryBean<?>) factory).isEagerInit();
                                }
                            }, getAccessControlContext());
                        }
                        else {
                            isEagerInit = (factory instanceof SmartFactoryBean &&
                                    ((SmartFactoryBean<?>) factory).isEagerInit());
                        }
                        if (isEagerInit) {
                            getBean(beanName);
                        }
                    }
                    else {
                        getBean(beanName);
                    }
                }
            }
            ...
        }
        ```

        <!-- div:right-panel-50 -->
        ![](/.images/doc/framework/spring/01-factory-bean/fb-01.png ':size=100%')
        <!-- panels:end -->

        > [?] `2).` 手动通过容器进行获取：例如`ToolFactory fb = applicationContext.getBean("&toolFB", ToolFactory.class);`注意要获取工厂对象得加工厂名前缀，不然的话，指定类型就是报错，没有指定ToolFactory.class类型的话就是获取到工厂对象产生的Tool对象。下面会通过代码解释。
        <br><br>首先通过getBean(名字，类型)调用的时候会进入到`doGetBean( final String name, final Class<T> requiredType, final Object[] args, boolean typeCheckOnly)` 方法。
        <br>方法内部通过`transformedBeanName(name);`将传入的带`&`符号前缀的name 转换成不带`&`的。
        <br>然后获取通过beanName获取在第一步中创建的工厂bean的实例。
        <br>发现实例非null之后进入到`getObjectForBeanInstance(sharedInstance, name, beanName, null);`这个方法里面。这个方法里面决定返回工厂对象ToolFactoryBean还是通过工厂创建的对象Tool。

        > [!CAUTION] `1).` 如果是`applicationContext.getBean("toolFB", ToolFactory.class);`这种情况，则下面方法最后判断之前获取到的是Tool对象，因为咱们指定了`ToolFactoryBean.class`类型。所以会报错。
        <br>`2).` 如果没有指定类型:`applicationContext.getBean("toolFB");` 则获取Tool对象，并不是工厂bean。

        ```java
        protected <T> T doGetBean(
			final String name, final Class<T> requiredType, final Object[] args, boolean typeCheckOnly)
			throws BeansException {

            final String beanName = transformedBeanName(name);
            Object bean;

            // Eagerly check singleton cache for manually registered singletons.
            Object sharedInstance = getSingleton(beanName);
            if (sharedInstance != null && args == null) {
                if (logger.isDebugEnabled()) {
                    if (isSingletonCurrentlyInCreation(beanName)) {
                        logger.debug("Returning eagerly cached instance of singleton bean '" + beanName +
                                "' that is not fully initialized yet - a consequence of a circular reference");
                    }
                    else {
                        logger.debug("Returning cached instance of singleton bean '" + beanName + "'");
                    }
                }
                bean = getObjectForBeanInstance(sharedInstance, name, beanName, null);
            }

            ...

            // Check if required type matches the type of the actual bean instance.
            if (requiredType != null && bean != null && !requiredType.isAssignableFrom(bean.getClass())) {
                try {
                    return getTypeConverter().convertIfNecessary(bean, requiredType);
                }
                catch (TypeMismatchException ex) {
                    if (logger.isDebugEnabled()) {
                        logger.debug("Failed to convert bean '" + name + "' to required type '" +
                                ClassUtils.getQualifiedName(requiredType) + "'", ex);
                    }
                    throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());
                }
            }
            return (T) bean;
        }
        ```

        > [?] `3).` 进入到`getObjectForBeanInstance`方法以后：
        <br><br>3.1 先判断 如果想获取工厂bean对象，但是获取到的示例类型并不属于FactoryBean的话报一个异常。
        <br>3.2 然后接着判断，如果是普通对象直接返回，或者name中包含`&`也直接返回。这就是获取factoryBean对象要加`&`的原因。
        <br>3.3 要继续往下走的话，就是属于工厂对象，但是name不包含前缀`&`.也就是通过`getObjectFromFactoryBean`获取factorybean对象要创建的对象。

        ```java
        protected Object getObjectForBeanInstance(
			Object beanInstance, String name, String beanName, RootBeanDefinition mbd) {

            // Don't let calling code try to dereference the factory if the bean isn't a factory.
            if (BeanFactoryUtils.isFactoryDereference(name) && !(beanInstance instanceof FactoryBean)) {
                throw new BeanIsNotAFactoryException(transformedBeanName(name), beanInstance.getClass());
            }

            // Now we have the bean instance, which may be a normal bean or a FactoryBean.
            // If it's a FactoryBean, we use it to create a bean instance, unless the
            // caller actually wants a reference to the factory.
            if (!(beanInstance instanceof FactoryBean) || BeanFactoryUtils.isFactoryDereference(name)) {
                return beanInstance;
            }

            Object object = null;
            if (mbd == null) {
                object = getCachedObjectForFactoryBean(beanName);
            }
            if (object == null) {
                // Return bean instance from factory.
                FactoryBean<?> factory = (FactoryBean<?>) beanInstance;
                // Caches object obtained from FactoryBean if it is a singleton.
                if (mbd == null && containsBeanDefinition(beanName)) {
                    mbd = getMergedLocalBeanDefinition(beanName);
                }
                boolean synthetic = (mbd != null && mbd.isSynthetic());
                object = getObjectFromFactoryBean(factory, beanName, !synthetic);
            }
            return object;
        }
        ```

        > [?] `4).` 通过`Tool tool1 = applicationContext.getBean(Tool.class);`获取需要通过工厂创建的bean对象解析。
        <br>通过debug会进入到`getBeanNamesForType(Tool.class)`方法。
        <br>并且在`isTypeMatch`中通过`getTypeForFactoryBean((FactoryBean<?>) beanInstance);`匹配到了。将刚才的beanName：`toolFB`返回。
        <br>然后在`resolveNamedBean`中通过getBean("toolFB", Tool.class, args),继续获取bean。此时就会进入到`3.3`的情况中。通过工厂bean实例创建对象，并且返回。

        ![](/.images/doc/framework/spring/01-factory-bean/fb-02.png ':size=100%')
        
        ```java
        private String[] doGetBeanNamesForType(ResolvableType type, boolean includeNonSingletons, boolean allowEagerInit) {
            List<String> result = new ArrayList<String>();

            // Check all bean definitions.
            for (String beanName : this.beanDefinitionNames) {
                // Only consider bean as eligible if the bean name
                // is not defined as alias for some other bean.
                if (!isAlias(beanName)) {
                    try {
                        RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName);
                        // Only check bean definition if it is complete.
                        if (!mbd.isAbstract() && (allowEagerInit ||
                                ((mbd.hasBeanClass() || !mbd.isLazyInit() || isAllowEagerClassLoading())) &&
                                        !requiresEagerInitForType(mbd.getFactoryBeanName()))) {
                            // In case of FactoryBean, match object created by FactoryBean.
                            boolean isFactoryBean = isFactoryBean(beanName, mbd);
                            BeanDefinitionHolder dbd = mbd.getDecoratedDefinition();
                            boolean matchFound =
                                    (allowEagerInit || !isFactoryBean ||
                                            (dbd != null && !mbd.isLazyInit()) || containsSingleton(beanName)) &&
                                    (includeNonSingletons ||
                                            (dbd != null ? mbd.isSingleton() : isSingleton(beanName))) &&
                                    isTypeMatch(beanName, type);
                            if (!matchFound && isFactoryBean) {
                                // In case of FactoryBean, try to match FactoryBean instance itself next.
                                beanName = FACTORY_BEAN_PREFIX + beanName;
                                matchFound = (includeNonSingletons || mbd.isSingleton()) && isTypeMatch(beanName, type);
                            }
                            if (matchFound) {
                                result.add(beanName);
                            }
                        }
                    }
                    ...
                }
            }
            ...
            return StringUtils.toStringArray(result);
        }
        ```

* ## Reference
    + https://www.jianshu.com/p/271cefdd708c