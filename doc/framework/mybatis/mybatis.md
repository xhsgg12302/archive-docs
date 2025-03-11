
* ## Intro(MyBatis | underlying)

    + ### 要点

        > [!ATTENTION] 1. mapper 接口定义的语句怎么生成的? configuration解析后会生成一个 MappedStatement{id:""}. 并且将ID 和 ms 放入 configuration 中的 mappedStatements 中。
        <br>2. 执行顺序是什么样的？
        <br>3. 缓存是如何处理的？
        <br>4. 拦截器原理。

    + ### Mapper接口的实现

        > [?] 通过`MapperRegistry`里面的 map 类型的 knowMappers 根据 type 获取到 MapperProxyFactory ,进而调用`return mapperProxyFactory.newInstance(sqlSession);`返回一个 mapper 接口代理对象。
        <br> 上述代理对象中使用的 InvocationHandler 叫 `MapperProxy`，里面 invoke() 的实现是将方法包装成一个`MapperMethod` mm 存放在`MapperMethodInvoker` mmi 里面，并会进行缓存，然后调用 mmi 里面的 invoke，进而执行到 mm.execute()方法，内部通过执行器拿到结果返回。

    + ### 拦截器实现

        > [?] [插件文档](https://mybatis.org/mybatis-3/zh_CN/configuration.html#plugins)
        <br><br>对于拦截器来说，主要在以下接口工作：`Executor`，`ParameterHandler`，`ResultSetHandler`，`StatementHandler`。
        <br><br>涉及的主要类有，实现了 [JDK 代理](/doc/advance/README.md#jdk) 中 **InvocationHandler** 的 `Plugin`，以及 `Interceptor`接口。
        <br><br>大致调用流程：
        <br>`1.` 创建对象：`executor = new CachingExecutor(executor);`
        <br>`2.` 将所有拦截器进行插件化：`executor = (Executor) interceptorChain.pluginAll(executor);`内部实现如下：
        <br><span style='padding-left:2em'>`2.1.` 在 **Plugin** 代码中第 13 行，将 executor 对象，以及拦截器对象进行包装。如果拦截器注解签名方法中需要拦截的类在目标对象上的话，返回代理对象。如果不是，则返回target就行。
        <br><span style='padding-left:2em'>`2.2.` 返回的代理对象是实现了目标对象上面的接口，此处也就是 executor，所以可以强转。
        <br><span style='padding-left:2em'>`2.3.` 因为在 **InterceptorChain** 第六行循环中，需要注意第一次循环后返回的target可能是代理对象，下一次循环中可能返回代理对象 *去代理* 代理对象的情况。如下图：
        <br><span style='padding-left:5em'> ![](/.images/doc/framework/mybatis/underlying/u-interceptor-01.png ':size=70%')
        <br><br>`3.` 使用对象：内部的执行流程可以画图表示，涉及 JDK 代理。
        <br><span style='padding-left:2em'>如果在外部调用刚才插件化的代理对象的话，则首先调用到 **invoke()** 方法中，然后根据签名map 判断是否当前方法是拦截器需要拦截的方法，根据判断结果选择是进入拦截器拦截方法 **intercept(Invocation invocation)**，还是直接调用目标对象（此处的目标对象有可能是还是一个代理对象）。
        <br><span style='padding-left:5em'> ![](/.images/doc/framework/mybatis/underlying/u-interceptor-02.png ':size=80%')

        <!-- panels:start -->
        <!-- div:left-panel-50 -->

        ```java {6,20-22}
        public class InterceptorChain {
            private final List<Interceptor> interceptors = new ArrayList<>();

            public Object pluginAll(Object target) {
                for (Interceptor interceptor : interceptors) {
                    target = interceptor.plugin(target);
                }
                return target;
            }

            public void addInterceptor(Interceptor interceptor) { interceptors.add(interceptor); }

            public List<Interceptor> getInterceptors() { return Collections.unmodifiableList(interceptors); }
        }

        public interface Interceptor {

            Object intercept(Invocation invocation) throws Throwable;

            default Object plugin(Object target) {
                return Plugin.wrap(target, this);
            }

            default void setProperties(Properties properties) { // NOP }

        }
        ```       
        <!-- div:right-panel-50 -->
        ```java {13,26} [data-cc:395px]
        public class Plugin implements InvocationHandler {

            private final Object target;
            private final Interceptor interceptor;
            private final Map<Class<?>, Set<Method>> signatureMap;

            private Plugin(Object target, Interceptor interceptor, Map<Class<?>, Set<Method>> signatureMap) {
                this.target = target;
                this.interceptor = interceptor;
                this.signatureMap = signatureMap;
            }

            public static Object wrap(Object target, Interceptor interceptor) {
                Map<Class<?>, Set<Method>> signatureMap = getSignatureMap(interceptor);
                Class<?> type = target.getClass();
                Class<?>[] interfaces = getAllInterfaces(type, signatureMap);
                if (interfaces.length > 0) {
                    return Proxy.newProxyInstance(
                        type.getClassLoader(),
                        interfaces,
                        new Plugin(target, interceptor, signatureMap));
                }
                return target;
            }
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                try {
                    Set<Method> methods = signatureMap.get(method.getDeclaringClass());
                    if (methods != null && methods.contains(method)) {
                        return interceptor.intercept(new Invocation(target, method, args));
                    }
                    return method.invoke(target, args);
                } catch (Exception e) {
                    throw ExceptionUtil.unwrapThrowable(e);
                }
            }
            ...
        }
        ```
        <!-- panels:end -->

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ```java [data-file:ExamplePlugin.java]
        @Intercepts(
            {@Signature(
                type= Executor.class,
                method = "update",
                args = {MappedStatement.class,Object.class}
            )}
        )
        public class ExamplePlugin implements Interceptor {

            private Properties properties = new Properties();

            @Override
            public Object intercept(Invocation invocation) throws Throwable {
                // implement pre-processing if needed
                Object returnObject = invocation.proceed();
                // implement post-processing if needed
                return returnObject;
            }

            @Override
            public void setProperties(Properties properties) {
                this.properties = properties;
            }
        }
        ```
        <!-- div:right-panel-50 -->
        ```java [data-file:PageInterceptor.java]
        @Intercepts(
            @Signature(
                type = Executor.class, 
                method = "query", 
                args = { MappedStatement.class, Object.class, RowBounds.class, ResultHandler.class }
            )
        )
        public class PageInterceptor implements Interceptor {
            private static final List<ResultMapping> EMPTY_RESULTMAPPING = new ArrayList<ResultMapping>(0);

            // 数据库方言
            private String dialect = "mysql";

            @SuppressWarnings({ "rawtypes", "unchecked" })
            @Override
            public Object intercept(Invocation invocation) throws Throwable {
                final Object[] args = invocation.getArgs();
                RowBounds rowBounds = (RowBounds) args[2];
                if (rowBounds == null || rowBounds == RowBounds.DEFAULT) {
                    return invocation.proceed();
                }
                ...
            }
        }
        ```
        <!-- panels:end -->

* ## Reference

    + https://drive.google.com/file/d/1V8Dtucqzq0P_djVE9ccV5MLGLpWWGSNA/view?usp=sharing