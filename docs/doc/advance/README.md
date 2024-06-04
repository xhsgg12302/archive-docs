* ## 类加载器

    > [!] 类加载器从 JDK 1.0 就出现了，最初只是为了满足 Java Applet（已经被淘汰） 的需要。后来，慢慢成为 Java 程序中的一个重要组成部分，赋予了 Java 类可以被动态加载到 JVM 中并执行的能力。

    + ### 定义

        <!-- panels:start -->
        <!-- div:left-panel-40 -->
        > [?] 1). 类加载器是一个负责加载类的对象。`ClassLoader`是一个抽象类。给定一个类的二进制名称，类加载器尝试定位或者生成构成类定义的数据。一个典型的策略是：转换一个名字到一个文件然后读取名字对应的类文件从文件系统。
        <br><br>2). 每个类对象都包含一个定义它的类加载器`ClassLoader`的引用。
        <br><br>3). 数组类的类对象不是通过类加载器创建的。但会根据java运行时的需要自动创建（也就是JVM自己创建）。对于数组的`Class.getClassLoader()`返回的类加载器和它加载它元素类型的加载器一样。如果元素是`primitive type`原始类型(有别于复合类型)，则数组类没有类加载器。
        <br><br>4). 应用程序实现 ClassLoader 的子类，以扩展 Java 虚拟机动态加载类的方式。
        <br><br>5). `ClassLoader`使用委托模式查找类或资源(文本，图像，配置文件，视频等)通过`getResource`，每一个`ClassLoader`都关联一个父加载器。当请求查找一个类或资源的时候。在它自己加载之前会先委托父加载器去加载。JVM内建的加载器叫作`bootstrap class loader`, 它自己没有父加载器，但可以作为换一个父加载器实例。
        <br><br>6). 通常，JVM虚拟机加载类从本地文件系统已平台相关的方式。例如：在UNIX系统中。JVM加载类通过定义的`CLASSPATH`环境变量。
        <br><br>7). 然而，一些类获取不是来源于文件，而是来自诸如网络的情况，或者被一个应用程序构造生成。`defineClass`转换一个字节数组到类的实例。可以使用`Class.newInstance`创建新定义的类的实例。
        <br><br>8). 类加载器创建的对象的方法和构造函数可以引用其他类。为了确定引用的类，Java 虚拟机调用最初创建该类的类加载器的 loadClass 方法。[参考](https://stackoverflow.com/questions/61711187/what-does-the-methods-and-constructors-of-objects-created-by-a-class-loader-may) , [代码实现](https://github.com/12302-bak/idea-test-project/tree/learning/_0_base-learning/src/main/java/_jvm/classLoader/load_reference)
        <!-- div:right-panel-59 -->
        ```java
        /**
         * A class loader is an object that is responsible for loading classes. The
         * class ClassLoader is an abstract class.  Given the binary name of a class, a class loader should attempt to
         * locate or generate data that constitutes a definition for the class.  A
         * typical strategy is to transform the name into a file name and then read a
         * "class file" of that name from a file system.
         *
         * Every Class object contains a  reference to the ClassLoader that defined it.
         *
         * <p> <tt>Class</tt> objects for array classes are not created by class
         * loaders, but are created automatically as required by the Java runtime.
         * The class loader for an array class, as returned by {@link
         * Class#getClassLoader()} is the same as the class loader for its element
         * type; if the element type is a primitive type, then the array class has no
         * class loader.
         *
         * <p> Applications implement subclasses of <tt>ClassLoader</tt> in order to
         * extend the manner in which the Java virtual machine dynamically loads
         * classes.
         *
         * <p> Class loaders may typically be used by security managers to indicate
         * security domains.
         *
         * <p> The <tt>ClassLoader</tt> class uses a delegation model to search for
         * classes and resources.  Each instance of <tt>ClassLoader</tt> has an
         * associated parent class loader.  When requested to find a class or
         * resource, a <tt>ClassLoader</tt> instance will delegate the search for the
         * class or resource to its parent class loader before attempting to find the
         * class or resource itself.  The virtual machine's built-in class loader,
         * called the "bootstrap class loader", does not itself have a parent but may
         * serve as the parent of a <tt>ClassLoader</tt> instance.
         *
         * <p> Normally, the Java virtual machine loads classes from the local file
         * system in a platform-dependent manner.  For example, on UNIX systems, the
         * virtual machine loads classes from the directory defined by the
         * <tt>CLASSPATH</tt> environment variable.
         *
         * <p> However, some classes may not originate from a file; they may originate
         * from other sources, such as the network, or they could be constructed by an
         * application.  The method defineClass converts an array of bytes into an instance of class Class. Instances of this newly defined class can be created using Class.newInstance.
         *
         * <p> The methods and constructors of objects created by a class loader may
         * reference other classes.  To determine the class(es) referred to, the Java
         * virtual machine invokes the {@link #loadClass <tt>loadClass</tt>} method of
         * the class loader that originally created the class.
         *
         */
        public abstract class ClassLoader { }
        ```
        <!-- panels:end -->

        <!-- panels:start -->
        <!-- div:title-panel -->
        #### 网络类加载器demo
        <!-- div:left-panel-100 -->
        ```java
        /*
         * <p> The network class loader subclass must define the methods {@link
         * #findClass <tt>findClass</tt>} and <tt>loadClassData</tt> to load a class
         * from the network.  Once it has downloaded the bytes that make up the class,
         * it should use the method {@link #defineClass <tt>defineClass</tt>} to
         * create a class instance.  A sample implementation is:
         *
         */
        class NetworkClassLoader extends ClassLoader {
            String host;
            int port;
    
            public Class findClass(String name) {
                byte[] b = loadClassData(name);
                return defineClass(name, b, 0, b.length);
            }
    
            private byte[] loadClassData(String name) {
                // load the class data from the connection
            }
        }

        // 
        ClassLoader loader = new NetworkClassLoader(host, port);
        Object main = loader.loadClass("Main", true).newInstance();
        ```
        <!-- panels:end -->

    + ### 加载流程

        ![](/.images/doc/advance/advance/class-loader-01.png ':size=70%')

        + 加载loading，类的加载过程这一阶段，通过一个类的完全限定查找此类字节码文件，并利用字节码文件创建一个Class对象。
        + 验证verification，目的在于确保Class文件的字节流中包含的信息符合当前虚拟机的要求，不会危害虚拟机自身的安全，主要包括四种验证， 文件格式，元数据，字节码，符号引用。
        + 准备preparation，为类变量，（既static 修饰的字段变量）分配内存并且设置该变量的初始值即0（如static int i = 5 ；这里只将i初始化为0。 至于5的值将在初始化时赋值，这里不包含用final修饰的static,因为final在编译的时候会分配，注意这里不会为实例变量 分配初始化，类变量会分配在方法区中，而实例变量时会随着对象一起分配到Java堆中。
        + 解析resolution，主要将常量池中的符号引用替换为直接引用的过程。符号引用就是一组符号用来描述目标，可以是任何字面量，而直接引用 就是直接指向目标的指针，相对偏移量或者一个间接定位到目标的句柄。有类或接口的解析，字段解析，类方法解析， 接口方法解析等。
        + 初始化initialization、类加载的最后阶段，如该类具有超类，则对其进行初始化，执行类的初始化`<clinit>()`，包括静态初始化器和静态初始化成员变量（如前面只初始化了 默认值的static变量将会在这个阶段赋值，成员变量也将被初始化）。

    + ### 加载规则

        <!-- panels:start -->
        <!-- div:left-panel-40 -->
        > [?] JVM 启动的时候，并不会一次性加载所有的类，而是根据需要去动态加载。也就是说，大部分类在具体用到的时候才会去加载，这样对内存更加友好。
        <br><br>对于已经加载的类会被放在 ClassLoader 中。在类加载的时候，系统会首先判断当前类是否被加载过。已经被加载的类会直接返回，否则才会尝试加载。也就是说，对于一个类加载器来说，相同二进制名称的类只会被加载一次。

        > [!] 通过注释可以看出：
        <br>JVM 会自己调用`addClass`方法用来记录当前类加载器已经加载的类。
        <!-- div:right-panel-60 -->
        ```java
        public abstract class ClassLoader {

            // The parent class loader for delegation
            // Note: VM hardcoded the offset of this field, thus all new fields
            // must be added *after* it.
            private final ClassLoader parent;

            // The classes loaded by this class loader. The only purpose of this table
            // is to keep the classes from being GC'ed until the loader is GC'ed.
            private final Vector<Class<?>> classes = new Vector<>();

            // Invoked by the VM to record every loaded class with this loader.
            void addClass(Class<?> c) {
                classes.addElement(c);
            }
        }
        ```
        <!-- panels:end -->
    
    + ### 分类

        > [?] JVM 中内置了三个重要的 ClassLoader：
        <br><br>1). ___BootstrapClassLoader(启动类加载器)___ ：最顶层的加载类，由 C++实现，没有对应的Java类。所以在Java中是取不到的。如果一个类的classloader是null。已经足可以证明他就是由BootStrapClassLoader 加载的,通常表示为 null，并且没有父级，主要用来加载JDK内部的核心类库`%JAVA_HOME%/lib目录下的rt.jar、resources.jar、charsets.jar等 jar 包和类`以及被`-Xbootclasspath`参数指定的路径下的所有类。
        <br><br>2). ___ExtClassLoader(扩展类加载器)___ ：主要负责加载`%JRE_HOME%/lib/ext`目录下的 jar 包和类以及被`java.ext.dirs`系统变量所指定的路径下的所有类。
        <br><br>3). ___AppClassLoader(应用程序类加载器)___ ：面向我们用户的加载器，负责加载当前应用`classpath`下的所有 jar 包和类。

        > [!] 属性parent是通过`ClassLoader(Void unused, ClassLoader parent)`方法传进去的。`AppClassLoader`和`ExtClassLoader`并非继承关系。

        ![](/.images/doc/advance/advance/class-loader-03.png ':size=66%')
        ![](/.images/doc/advance/advance/class-loader-02.png ':size=33%')

    + ### 双亲委派模型

        <!-- panels:start -->
        <!-- div:left-panel-40 -->
        > [?] 参考[定义<sup>5). </sup>](#定义)。如果一个类加载器收到了类加载器的请求，它首先不会自己尝试加载这个类，而是把这个请求委派给父类加载器去完成，每一个层次的类加载器都是如此，因此所有的加载请求最终都应该传送到顶层的启动类加载器中，只有当父类加载器反馈自己无法完成这个加载请求（它的搜索范围中没有找到所需的类时，子加载类才会尝试自己去加载)。

        > [!] 自定义加载器的话，需要继承 ClassLoader 。如果我们不想打破双亲委派模型，就重写 ClassLoader 类中的 findClass() 方法即可，无法被父类加载器加载的类最终会通过这个方法被加载。但是，如果想打破双亲委派模型则需要重写 loadClass() 方法。

        > [!] 由于双亲委派模型以及类加载器的一些特性(使用当前类加载器去加载引用类)，会出现一些情况。
        <br><br>比如：
        <br> 使用当前类加载器`CustomClassLoader1`去加载子类`CustomClassLoader2`管理路径下的class。会找不到类，以至于加载不了。[代码实现]()
        <br><br>解决办法：
        <br>Java设计团队只好引入了一个不太优雅的设计 ___线程上下文件类加载器(Thread Context ClassLoader)___ 。这个类加载器可以通过java.lang.Thread类的setContextClassLoader()方法进行设置，如果创建线程时还未设置，它将会从父线程中继承一个。如：[SPI](#spi)
        <!-- div:right-panel-60 -->
        ```java
        protected Class<?> loadClass(String name, boolean resolve)
            throws ClassNotFoundException
        {
            synchronized (getClassLoadingLock(name)) {
                // First, check if the class has already been loaded
                Class<?> c = findLoadedClass(name);
                if (c == null) {
                    long t0 = System.nanoTime();
                    try {
                        if (parent != null) {
                            c = parent.loadClass(name, false);
                        } else {
                            c = findBootstrapClassOrNull(name);
                        }
                    } catch (ClassNotFoundException e) {
                        // ClassNotFoundException thrown if class not found
                        // from the non-null parent class loader
                    }

                    if (c == null) {
                        // If still not found, then invoke findClass in order
                        // to find the class.
                        long t1 = System.nanoTime();
                        c = findClass(name);

                        // this is the defining class loader; record the stats
                        sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                        sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                        sun.misc.PerfCounter.getFindClasses().increment();
                    }
                }
                if (resolve) {
                    resolveClass(c);
                }
                return c;
            }
        }
        ```
        <!-- panels:end -->

    + ### 初始化顺序
        > [!] 父，子，静态代码块  -- 和静态变量  等级初始化
        <br>父，子，普通代码块  -- 和普通变量  -- 构造方法（最后）  等级初始化
        <br>继续new Parent()，只执行父类的普通属性赋值和普通代码块，最后父类构造方法
        <br>继续new Child(), 执行父类的普通属性和代码块，构造方法，然后是子类的

        <!-- panels:start -->
        <!-- div:left-panel-67 -->
        ```java
        public class Parent {
            static { System.out.println("父类：静态代码块"); }
            private static String staticStringInParent = initStaticStringInParent();
            private String stringInParent = initStringInParent();
            public Parent() { System.out.println("父类：构造方法"); }

            { System.out.println("父类：普通代码块"); }

            private static String initStaticStringInParent() { System.out.println("父类：静态方法，被静态成员变量赋值调用。"); return "initStaticStringInParent";}
            private String initStringInParent() {System.out.println("父类：普通成员方法，被普通成员变量赋值调用。"); return "initStringInParent";}

            public static void main(String[] args) {
                Parent p = new Child();
                System.out.println("\n===============================\n\n\n");
                p = new Child();
            }
        }
        // 子类
        class Child extends Parent {
            { System.out.println("子类：普通代码块");}
            private static String staticStringInChild = initStaticStringInChild();
            static { System.out.println("子类：静态代码块"); }
            private String stringInChild = initStringInChild();
            public Child() { System.out.println("子类：构造方法"); }

            private static String initStaticStringInChild() {System.out.println("子类：静态方法，被静态成员变量赋值调用。"); return "initStaticStringInChild";}
            private String initStringInChild() {System.out.println("子类：普通成员方法，被普通成员变量赋值调用。"); return "initStringInChild";}
        }
        ```
        <!-- div:right-panel-33 -->
        ```shell
        # output: 

        // 父类：静态代码块
        // 父类：静态方法，被静态成员变量赋值调用。
        // 子类：静态方法，被静态成员变量赋值调用。
        // 子类：静态代码块
        // 父类：普通成员方法，被普通成员变量赋值调用。
        // 父类：普通代码块
        // 父类：构造方法
        // 子类：普通代码块
        // 子类：普通成员方法，被普通成员变量赋值调用。
        // 子类：构造方法
        //
        // ===============================
        //
        // 父类：普通成员方法，被普通成员变量赋值调用。
        // 父类：普通代码块
        // 父类：构造方法
        // 子类：普通代码块
        // 子类：普通成员方法，被普通成员变量赋值调用。
        // 子类：构造方法
        ```
        <!-- panels:end -->
    
    + ### Reference
        * https://javaguide.cn/java/jvm/classloader.html#类加载器

* ## 动态代理

    + ### 简介

        > [!] 动态代理就是，在程序运行期，创建目标对象的代理对象，并对目标对象中的方法进行功能性增强的一种技术。
        <br>在生成代理对象的过程中，目标对象不变，代理对象中的方法是目标对象方法的增强方法。
        <br>可以理解为运行期间，对象中方法的动态拦截，在拦截方法的前后执行功能操作。

    + ### 两种方式
        
        - #### JDK
            > [?] 基于接口的动态代理，代理类必须实现某个接口。
            <br>代理配置： -Dsun.misc.ProxyGenerator.saveGeneratedFiles=true  源码：[`ProxyGenerator`](https://github.com/openjdk/jdk/blob/jdk8-b120/jdk/src/share/classes/sun/misc/ProxyGenerator.java#L341C13-L341C31)。可以生成代理类字节文件，方便反编译查看生成内容。
            <br>repo: [jdk_dynamic](https://github.com/12302-bak/idea-test-project/tree/master/_0_base-learning/src/main/java/_base/proxy/jdk_dynamic)

            ![](/.images/doc/advance/advance/class-dynamic-proxy-jdk.png ':size=80%')
            
            <details open><summary>代码示例</summary>

            ```java
            @Test
            public void test(){
                InvocationHandler handler = new InvocationHandler() {
                    private Object target; 
                    {this.target = new Student("eli _w");}
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        System.out.println("invoke: " +method.getName());
                        MonitorUtil.start();
                        Object result = method.invoke(target, args);
                        MonitorUtil.finish(method.getName());
                        return result;
                    }
                };
                Person proxy = (Person)Proxy.newProxyInstance(Person.class.getClassLoader(), new Class[]{Person.class}, handler);
                proxy.sayHello("exec: hello world");
            }        
            ```
            </details>
        
            <details><summary>生成的代理类</summary>

            ```java
            package com.sun.proxy;

            import _base.proxy.jdk_dynamic.Person;
            import java.lang.reflect.InvocationHandler;
            import java.lang.reflect.Method;
            import java.lang.reflect.Proxy;
            import java.lang.reflect.UndeclaredThrowableException;

            public final class $Proxy4 extends Proxy implements Person {
                private static Method m1;
                private static Method m4;
                private static Method m2;
                private static Method m3;
                private static Method m0;

                public $Proxy4(InvocationHandler var1) throws  {
                    super(var1);
                }

                public final boolean equals(Object var1) throws  {
                    try {
                        return (Boolean)super.h.invoke(this, m1, new Object[]{var1});
                    } catch (RuntimeException | Error var3) {
                        throw var3;
                    } catch (Throwable var4) {
                        throw new UndeclaredThrowableException(var4);
                    }
                }

                public final void sayHello(String var1) throws  {
                    try {
                        super.h.invoke(this, m4, new Object[]{var1});
                    } catch (RuntimeException | Error var3) {
                        throw var3;
                    } catch (Throwable var4) {
                        throw new UndeclaredThrowableException(var4);
                    }
                }

                public final String toString() throws  {
                    try {
                        return (String)super.h.invoke(this, m2, (Object[])null);
                    } catch (RuntimeException | Error var2) {
                        throw var2;
                    } catch (Throwable var3) {
                        throw new UndeclaredThrowableException(var3);
                    }
                }

                public final void giveMoney() throws  {
                    try {
                        super.h.invoke(this, m3, (Object[])null);
                    } catch (RuntimeException | Error var2) {
                        throw var2;
                    } catch (Throwable var3) {
                        throw new UndeclaredThrowableException(var3);
                    }
                }

                public final int hashCode() throws  {
                    try {
                        return (Integer)super.h.invoke(this, m0, (Object[])null);
                    } catch (RuntimeException | Error var2) {
                        throw var2;
                    } catch (Throwable var3) {
                        throw new UndeclaredThrowableException(var3);
                    }
                }

                static {
                    try {
                        m1 = Class.forName("java.lang.Object").getMethod("equals", Class.forName("java.lang.Object"));
                        m4 = Class.forName("_base.proxy.jdk_dynamic.Person").getMethod("sayHello", Class.forName("java.lang.String"));
                        m2 = Class.forName("java.lang.Object").getMethod("toString");
                        m3 = Class.forName("_base.proxy.jdk_dynamic.Person").getMethod("giveMoney");
                        m0 = Class.forName("java.lang.Object").getMethod("hashCode");
                    } catch (NoSuchMethodException var2) {
                        throw new NoSuchMethodError(var2.getMessage());
                    } catch (ClassNotFoundException var3) {
                        throw new NoClassDefFoundError(var3.getMessage());
                    }
                }
            }

            ```
            </details>
        
        - #### CGLIB

            > [?] 基于ASM字节码操作框架。由于继承关系，不能代理final类。
            <br>代理类生成配置： -Dcglib.debugLocation=/path/to/somewhere   源码：[`DebuggingClassWriter`](https://github.com/cglib/cglib/blob/RELEASE_3_3_0/cglib/src/main/java/net/sf/cglib/core/DebuggingClassWriter.java#L79) 
            <br>cglib生成代理类的过程当中会生成一些辅助类，所以打断点获取Class字节数组的时候需要注意，一般来说最后一次生成的才是正真的Proxy。
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 1. class net.sf.cglib.proxy.Enhancer\$EnhancerKey\$\$KeyFactoryByCGLIB\$\$7fb24d72 
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 2. class net.sf.cglib.core.MethodWrapper\$MethodWrapperKey\$\$KeyFactoryByCGLIB\$\$d45e49f7 
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 3. class _base.proxy.cglib_dynamic.StudentNoIntfs\$\$EnhancerByCGLIB\$\$81f8e23d
            <br>repo: [cglib-dynamic](https://github.com/12302-bak/idea-test-project/tree/master/_0_base-learning/src/main/java/_base/proxy/cglib_dynamic)

            <details open><summary>代码示例</summary>

            ```java
            @Test
            public void test(){
                MethodInterceptor interceptor = new MethodInterceptor() {
                    public Object target;
                    {this.target = new StudentNoIntfs("eli _w");}
                    @Override
                    public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
                        System.out.println("intercept: " + method.getName());
                        MonitorUtil.start();
                        Object result = method.invoke(target, args);
                        MonitorUtil.finish(method.getName());
                        return result;
                    }
                };
                Enhancer enhancer = new Enhancer();
                enhancer.setSuperclass(StudentNoIntfs.class);
                enhancer.setCallback(interceptor);
                StudentNoIntfs proxy = (StudentNoIntfs) enhancer.create();
                proxy.sayHello("exec: hello cglib");
            }        
            ```
            </details>
        
            <details><summary>生成的代理类</summary>

            ```java
            package _base.proxy.cglib_dynamic;

            import java.lang.reflect.Method;
            import net.sf.cglib.core.ReflectUtils;
            import net.sf.cglib.core.Signature;
            import net.sf.cglib.proxy.Callback;
            import net.sf.cglib.proxy.Factory;
            import net.sf.cglib.proxy.MethodInterceptor;
            import net.sf.cglib.proxy.MethodProxy;

            public class StudentNoIntfs$$EnhancerByCGLIB$$81f8e23d extends StudentNoIntfs implements Factory {
            private boolean CGLIB$BOUND;
            public static Object CGLIB$FACTORY_DATA;
            private static final ThreadLocal CGLIB$THREAD_CALLBACKS;
            private static final Callback[] CGLIB$STATIC_CALLBACKS;
            private MethodInterceptor CGLIB$CALLBACK_0;
            private static Object CGLIB$CALLBACK_FILTER;
            private static final Method CGLIB$sayHello$0$Method;
            private static final MethodProxy CGLIB$sayHello$0$Proxy;
            private static final Object[] CGLIB$emptyArgs;
            private static final Method CGLIB$equals$1$Method;
            private static final MethodProxy CGLIB$equals$1$Proxy;
            private static final Method CGLIB$toString$2$Method;
            private static final MethodProxy CGLIB$toString$2$Proxy;
            private static final Method CGLIB$hashCode$3$Method;
            private static final MethodProxy CGLIB$hashCode$3$Proxy;
            private static final Method CGLIB$clone$4$Method;
            private static final MethodProxy CGLIB$clone$4$Proxy;

                static void CGLIB$STATICHOOK1() {
                    CGLIB$THREAD_CALLBACKS = new ThreadLocal();
                    CGLIB$emptyArgs = new Object[0];
                    Class var0 = Class.forName("_base.proxy.cglib_dynamic.StudentNoIntfs$$EnhancerByCGLIB$$81f8e23d");
                    Class var1;
                    Method[] var10000 = ReflectUtils.findMethods(new String[]{"equals", "(Ljava/lang/Object;)Z", "toString", "()Ljava/lang/String;", "hashCode", "()I", "clone", "()Ljava/lang/Object;"}, (var1 = Class.forName("java.lang.Object")).getDeclaredMethods());
                    CGLIB$equals$1$Method = var10000[0];
                    CGLIB$equals$1$Proxy = MethodProxy.create(var1, var0, "(Ljava/lang/Object;)Z", "equals", "CGLIB$equals$1");
                    CGLIB$toString$2$Method = var10000[1];
                    CGLIB$toString$2$Proxy = MethodProxy.create(var1, var0, "()Ljava/lang/String;", "toString", "CGLIB$toString$2");
                    CGLIB$hashCode$3$Method = var10000[2];
                    CGLIB$hashCode$3$Proxy = MethodProxy.create(var1, var0, "()I", "hashCode", "CGLIB$hashCode$3");
                    CGLIB$clone$4$Method = var10000[3];
                    CGLIB$clone$4$Proxy = MethodProxy.create(var1, var0, "()Ljava/lang/Object;", "clone", "CGLIB$clone$4");
                    CGLIB$sayHello$0$Method = ReflectUtils.findMethods(new String[]{"sayHello", "(Ljava/lang/String;)V"}, (var1 = Class.forName("_base.proxy.cglib_dynamic.StudentNoIntfs")).getDeclaredMethods())[0];
                    CGLIB$sayHello$0$Proxy = MethodProxy.create(var1, var0, "(Ljava/lang/String;)V", "sayHello", "CGLIB$sayHello$0");
                }

                final void CGLIB$sayHello$0(String var1) {
                    super.sayHello(var1);
                }

                public final void sayHello(String var1) {
                    MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
                    if (var10000 == null) {
                        CGLIB$BIND_CALLBACKS(this);
                        var10000 = this.CGLIB$CALLBACK_0;
                    }

                    if (var10000 != null) {
                        var10000.intercept(this, CGLIB$sayHello$0$Method, new Object[]{var1}, CGLIB$sayHello$0$Proxy);
                    } else {
                        super.sayHello(var1);
                    }
                }

                final boolean CGLIB$equals$1(Object var1) {
                    return super.equals(var1);
                }

                public final boolean equals(Object var1) {
                    MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
                    if (var10000 == null) {
                        CGLIB$BIND_CALLBACKS(this);
                        var10000 = this.CGLIB$CALLBACK_0;
                    }

                    if (var10000 != null) {
                        Object var2 = var10000.intercept(this, CGLIB$equals$1$Method, new Object[]{var1}, CGLIB$equals$1$Proxy);
                        return var2 == null ? false : (Boolean)var2;
                    } else {
                        return super.equals(var1);
                    }
                }

                final String CGLIB$toString$2() {
                    return super.toString();
                }

                public final String toString() {
                    MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
                    if (var10000 == null) {
                        CGLIB$BIND_CALLBACKS(this);
                        var10000 = this.CGLIB$CALLBACK_0;
                    }

                    return var10000 != null ? (String)var10000.intercept(this, CGLIB$toString$2$Method, CGLIB$emptyArgs, CGLIB$toString$2$Proxy) : super.toString();
                }

                final int CGLIB$hashCode$3() {
                    return super.hashCode();
                }

                public final int hashCode() {
                    MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
                    if (var10000 == null) {
                        CGLIB$BIND_CALLBACKS(this);
                        var10000 = this.CGLIB$CALLBACK_0;
                    }

                    if (var10000 != null) {
                        Object var1 = var10000.intercept(this, CGLIB$hashCode$3$Method, CGLIB$emptyArgs, CGLIB$hashCode$3$Proxy);
                        return var1 == null ? 0 : ((Number)var1).intValue();
                    } else {
                        return super.hashCode();
                    }
                }

                final Object CGLIB$clone$4() throws CloneNotSupportedException {
                    return super.clone();
                }

                protected final Object clone() throws CloneNotSupportedException {
                    MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
                    if (var10000 == null) {
                        CGLIB$BIND_CALLBACKS(this);
                        var10000 = this.CGLIB$CALLBACK_0;
                    }

                    return var10000 != null ? var10000.intercept(this, CGLIB$clone$4$Method, CGLIB$emptyArgs, CGLIB$clone$4$Proxy) : super.clone();
                }

                public static MethodProxy CGLIB$findMethodProxy(Signature var0) {
                    String var10000 = var0.toString();
                    switch(var10000.hashCode()) {
                    case -508378822:
                        if (var10000.equals("clone()Ljava/lang/Object;")) {
                            return CGLIB$clone$4$Proxy;
                        }
                        break;
                    case 771401912:
                        if (var10000.equals("sayHello(Ljava/lang/String;)V")) {
                            return CGLIB$sayHello$0$Proxy;
                        }
                        break;
                    case 1826985398:
                        if (var10000.equals("equals(Ljava/lang/Object;)Z")) {
                            return CGLIB$equals$1$Proxy;
                        }
                        break;
                    case 1913648695:
                        if (var10000.equals("toString()Ljava/lang/String;")) {
                            return CGLIB$toString$2$Proxy;
                        }
                        break;
                    case 1984935277:
                        if (var10000.equals("hashCode()I")) {
                            return CGLIB$hashCode$3$Proxy;
                        }
                    }

                    return null;
                }

                public StudentNoIntfs$$EnhancerByCGLIB$$81f8e23d(String var1) {
                    super(var1);
                    CGLIB$BIND_CALLBACKS(this);
                }

                public StudentNoIntfs$$EnhancerByCGLIB$$81f8e23d() {
                    CGLIB$BIND_CALLBACKS(this);
                }

                public static void CGLIB$SET_THREAD_CALLBACKS(Callback[] var0) {
                    CGLIB$THREAD_CALLBACKS.set(var0);
                }

                public static void CGLIB$SET_STATIC_CALLBACKS(Callback[] var0) {
                    CGLIB$STATIC_CALLBACKS = var0;
                }

                private static final void CGLIB$BIND_CALLBACKS(Object var0) {
                    StudentNoIntfs$$EnhancerByCGLIB$$81f8e23d var1 = (StudentNoIntfs$$EnhancerByCGLIB$$81f8e23d)var0;
                    if (!var1.CGLIB$BOUND) {
                        var1.CGLIB$BOUND = true;
                        Object var10000 = CGLIB$THREAD_CALLBACKS.get();
                        if (var10000 == null) {
                            var10000 = CGLIB$STATIC_CALLBACKS;
                            if (var10000 == null) {
                                return;
                            }
                        }

                        var1.CGLIB$CALLBACK_0 = (MethodInterceptor)((Callback[])var10000)[0];
                    }

                }

                public Object newInstance(Callback[] var1) {
                    CGLIB$SET_THREAD_CALLBACKS(var1);
                    StudentNoIntfs$$EnhancerByCGLIB$$81f8e23d var10000 = new StudentNoIntfs$$EnhancerByCGLIB$$81f8e23d();
                    CGLIB$SET_THREAD_CALLBACKS((Callback[])null);
                    return var10000;
                }

                public Object newInstance(Callback var1) {
                    CGLIB$SET_THREAD_CALLBACKS(new Callback[]{var1});
                    StudentNoIntfs$$EnhancerByCGLIB$$81f8e23d var10000 = new StudentNoIntfs$$EnhancerByCGLIB$$81f8e23d();
                    CGLIB$SET_THREAD_CALLBACKS((Callback[])null);
                    return var10000;
                }

                public Object newInstance(Class[] var1, Object[] var2, Callback[] var3) {
                    CGLIB$SET_THREAD_CALLBACKS(var3);
                    StudentNoIntfs$$EnhancerByCGLIB$$81f8e23d var10000 = new StudentNoIntfs$$EnhancerByCGLIB$$81f8e23d;
                    switch(var1.length) {
                    case 0:
                        var10000.<init>();
                        break;
                    case 1:
                        if (var1[0].getName().equals("java.lang.String")) {
                            var10000.<init>((String)var2[0]);
                            break;
                        }

                        throw new IllegalArgumentException("Constructor not found");
                    default:
                        throw new IllegalArgumentException("Constructor not found");
                    }

                    CGLIB$SET_THREAD_CALLBACKS((Callback[])null);
                    return var10000;
                }

                public Callback getCallback(int var1) {
                    CGLIB$BIND_CALLBACKS(this);
                    MethodInterceptor var10000;
                    switch(var1) {
                    case 0:
                        var10000 = this.CGLIB$CALLBACK_0;
                        break;
                    default:
                        var10000 = null;
                    }

                    return var10000;
                }

                public void setCallback(int var1, Callback var2) {
                    switch(var1) {
                    case 0:
                        this.CGLIB$CALLBACK_0 = (MethodInterceptor)var2;
                    default:
                    }
                }

                public Callback[] getCallbacks() {
                    CGLIB$BIND_CALLBACKS(this);
                    return new Callback[]{this.CGLIB$CALLBACK_0};
                }

                public void setCallbacks(Callback[] var1) {
                    this.CGLIB$CALLBACK_0 = (MethodInterceptor)var1[0];
                }

                static {
                    CGLIB$STATICHOOK1();
                }
            }

            ```
            </details>



* ## 反射

* ## 序列化

* ## SPI

    + ### 定义

        > [?] [wiki](https://en.wikipedia.org/wiki/Service_provider_interface): ___Service provider interface (SPI)___ 是一种API，打算让第三方厂商实现和扩展。它可以用来扩展和替换组件。
        <br>换句话说：是一种服务发现机制。它通过在 ClassPath 路径下的 META-INF/services 文件夹查找文件，自动加载文件里所定义的类。

    + ### 样例
        
        <!-- panels:start -->
        <!-- div:left-panel-40 -->
        > [?] <br>[测试代码](https://github.com/12302-bak/idea-test-project/tree/learning/_0_base-learning/src/main/java/_jvm/classLoader/spi)
        <br><br>1). 定义接口`ISpiTest`.
        <br><br>2). 接口`ISpiTest`实现类`SpiTestImpl`.
        <br><br>3). 按照规范在`META-INF/services/`目录下放置接口名`_jvm.classLoader.spi.service.ISpiTest`,内容为`_jvm.classLoader.spi.service.impl.SpiTestImpl`实现类的文件。
        <br><br>4). 使用`ServiceLoader`加载接口进行测试
        <!-- div:right-panel-60 -->
        <!-- tabs:start -->
        ##### **Entrance.java**
        ```java
        public class Entrance {
            public static void main(String[] args) {
                ServiceLoader<ISpiTest> serviceLoader = ServiceLoader.load(ISpiTest.class);
                Iterator<ISpiTest> iterator = serviceLoader.iterator();
                while(iterator.hasNext()){
                    ISpiTest next = iterator.next();
                    next.saySpi();
                }
            }
        }
        ```

        ##### **ISpiTest.java**
        ```java
        public interface ISpiTest {
            public void saySpi();
        }
        ```

        ##### **SpiTestImpl.java**
        ```java
        public class SpiTestImpl implements ISpiTest {
            @Override
            public void saySpi() {
                System.out.println("call saySpi");
            }
        }
        ```
        ##### **_jvm.classLoader.spi.service.ISpiTest**
        ```shell
        _jvm.classLoader.spi.service.impl.SpiTestImpl
        ```
        <!-- tabs:end -->
        <!-- panels:end -->

    + ### 结构

        > [?] 目前有两种实现技术，一种是JDK自带的[`java.util.ServiceLoader`](https://github.com/openjdk/jdk/blob/jdk8-b120/jdk/src/share/classes/java/util/ServiceLoader.java)，另外一种是sun公司提供的[`sun.misc.Service`](https://github.com/openjdk/jdk/blob/jdk8-b120/jdk/src/share/classes/sun/misc/Service.java)。
        <br>下面主要已JDK的进行分析。

        <!-- panels:start -->
        <!-- div:title-panel -->
        ##### ServiceLoader
        <!-- div:left-panel-50 -->
        > [?] 初始化`ServiceLoader`

        ```java
        // 调用
        ServiceLoader<ISpiTest> serviceLoader = ServiceLoader.load(ISpiTest.class);
        Iterator<ISpiTest> iterator = serviceLoader.iterator();
        while(iterator.hasNext()){
            ISpiTest next = iterator.next();
            next.saySpi();
        }

        // 
        public static <S> ServiceLoader<S> load(Class<S> service) {
            ClassLoader cl = Thread.currentThread().getContextClassLoader();
            return ServiceLoader.load(service, cl);
        }

        //
        public static <S> ServiceLoader<S> load(Class<S> service,
                                            ClassLoader loader){
            return new ServiceLoader<>(service, loader);
        }

        // 
        public void reload() {
            providers.clear();
            lookupIterator = new LazyIterator(service, loader);
        }

        private ServiceLoader(Class<S> svc, ClassLoader cl) {
            service = Objects.requireNonNull(svc, "Service interface cannot be null");
            loader = (cl == null) ? ClassLoader.getSystemClassLoader() : cl;
            acc = (System.getSecurityManager() != null) ? AccessController.getContext() : null;
            reload();
        }
        ```
        <!-- div:right-panel-50 -->
        > [?] 懒加载迭代

        ```java
        // 调用 iterator()方法返回一个迭代器，迭代器内部有一个类型为 LazyIterator lookupIterator懒加载迭代器。
        // 查找和加载在这个懒加载器中。
        public Iterator<S> iterator() {
            return new Iterator<S>() {

                Iterator<Map.Entry<String,S>> knownProviders
                    = providers.entrySet().iterator();

                public boolean hasNext() {
                    if (knownProviders.hasNext())
                        return true;
                    return lookupIterator.hasNext();
                }

                public S next() {
                    if (knownProviders.hasNext())
                        return knownProviders.next().getValue();
                    return lookupIterator.next();
                }

                public void remove() {
                    throw new UnsupportedOperationException();
                }

            };
        }

        // 然后就是使用懒加载迭代器通过`ClassLoader`getResource加载资源文件，
        configs = loader.getResources(fullName);
        // 以及 Class.forName(cn, false, loader); 加载资源文件类。最后强转换一下
        c = Class.forName(cn, false, loader);
        S p = service.cast(c.newInstance());
        ```
        <!-- panels:end -->

    + ### 使用场景

        - #### JDBC

            > [?] JDBC 4.0 开始增加了[`Autoloading of JDBC drivers`](https://docs.oracle.com/javadb/10.8.3.0/ref/rrefjdbc4_0summary.html)自动加载驱动的特性。主要使用的是 ***SPI*** 以及破坏双亲委派机制的 ***Thread Context ClassLoader*** 技术。
            <br><br>Autoloading of JDBC drivers. In earlier versions of JDBC, applications had to manually register drivers before requesting Connections. With JDBC 4.0, applications no longer need to issue a Class.forName() on the driver name; instead, the DriverManager will find an appropriate JDBC driver when the application requests a Connection.

            <!-- panels:start -->
            <!-- div:title-panel -->
            ##### 数据库驱动注册方式(老版本)
            <!-- div:left-panel-50 -->
            > [?] **Mysql**
            ```java
            /**
             * 方式①
             * 直接手动注册。
             */
            DriverManager.registerDriver(new com.mysql.jdbc.Driver());
            //Getting the connection
            String mysqlUrl = "jdbc:mysql://localhost/mydatabase";
            Connection con = DriverManager.getConnection(mysqlUrl, "root", "password");

            /**
             * 方式②
             * 间接手动注册。
             *（间接是因为Class.forName()加载类的时候会进行初始话，调用静态方法，近而调用注册方法注册）
             */
            Class.forName("com.mysql.jdbc.Driver");
            //Getting the connection
            String mysqlUrl = "jdbc:mysql://localhost/mydatabase";
            Connection con = DriverManager.getConnection(mysqlUrl, "root", "password");

            // Class.forName()调用静态方法进行注册。
            public class Driver extends NonRegisteringDriver implements java.sql.Driver {
                public Driver() throws SQLException {
                }

                static {
                    try {
                        
                        DriverManager.registerDriver(new Driver());
                    } catch (SQLException var1) {
                        throw 
                            new RuntimeException("Can't register driver!");
                    }
                }
            }
            ```
            
            <!-- div:right-panel-50 -->
            > [?] **SQLServer**
            ```java
            /**
             * 方式①
             * 直接手动注册。
             */
            DriverManager.registerDriver(new com.microsoft.sqlserver.jdbc.SQLServerDriver());
            //Getting the connection
            String mysqlUrl = "jdbc:sqlserver://localhost:1433;DatabaseName=wtf";
            Connection con = DriverManager.getConnection(mysqlUrl, "root", "password");
            
            /**
             * 方式②
             * 间接手动注册。
             *（间接是因为Class.forName()加载类的时候会进行初始话，调用静态方法，近而调用注册方法注册）
             */
            Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
            //Getting the connection
            String mysqlUrl = "jdbc:sqlserver://localhost:1433;DatabaseName=wtf";
            Connection con = DriverManager.getConnection(mysqlUrl, "sa", "password");

            // Class.forName()调用静态方法进行注册。 
            public final class SQLServerDriver implements Driver {
                 static {
                    DRIVER_PROPERTIES = new SQLServerDriverPropertyInfo[]{new SQLServerDriverPropertyInfo("applicationName", "Microsoft SQL Server JDBC Driver", false, (String[])null), new SQLServerDriverPropertyInfo("databaseName", "", false, (String[])null), new SQLServerDriverPropertyInfo("disableStatementPooling", "true", false, new String[]{"true"}), new SQLServerDriverPropertyInfo("encrypt", "false", false, TRUE_FALSE), new SQLServerDriverPropertyInfo("failoverPartner", "", false, (String[])null), new SQLServerDriverPropertyInfo("hostNameInCertificate", "", false, (String[])null), new SQLServerDriverPropertyInfo("instanceName", "", false, (String[])null), new SQLServerDriverPropertyInfo("integratedSecurity", "false", false, TRUE_FALSE), new SQLServerDriverPropertyInfo("lastUpdateCount", "true", false, TRUE_FALSE), new SQLServerDriverPropertyInfo("lockTimeout", "-1", false, (String[])null), new SQLServerDriverPropertyInfo("loginTimeout", "15", false, (String[])null), new SQLServerDriverPropertyInfo("packetSize", String.valueOf(8000), false, (String[])null), new SQLServerDriverPropertyInfo("password", "", true, (String[])null), new SQLServerDriverPropertyInfo("portNumber", "1433", false, (String[])null), new SQLServerDriverPropertyInfo("responseBuffering", "adaptive", false, new String[]{"adaptive", "full"}), new SQLServerDriverPropertyInfo("selectMethod", "direct", false, new String[]{"direct", "cursor"}), new SQLServerDriverPropertyInfo("sendStringParametersAsUnicode", "true", false, TRUE_FALSE), new SQLServerDriverPropertyInfo("serverName", "", false, (String[])null), new SQLServerDriverPropertyInfo("trustServerCertificate", "false", false, TRUE_FALSE), new SQLServerDriverPropertyInfo("trustStore", "", false, (String[])null), new SQLServerDriverPropertyInfo("trustStorePassword", "", false, (String[])null), new SQLServerDriverPropertyInfo("sendTimeAsDatetime", "true", false, TRUE_FALSE), new SQLServerDriverPropertyInfo("user", "", true, (String[])null), new SQLServerDriverPropertyInfo("workstationID", "", false, (String[])null), new SQLServerDriverPropertyInfo("xopenStates", "false", false, TRUE_FALSE)};
                    driverPropertiesSynonyms = new String[][]{{"database", "databaseName"}, {"userName", "user"}, {"server", "serverName"}, {"port", "portNumber"}};
                    baseID = 0;
                    loggerExternal = Logger.getLogger("com.microsoft.sqlserver.jdbc.Driver");
                    drLogger = Logger.getLogger("com.microsoft.sqlserver.jdbc.internals.SQLServerDriver");
                    try {
                        DriverManager.registerDriver(new SQLServerDriver());
                    } catch (SQLException var1) {
                        var1.printStackTrace();
                    }
                }
            }
            ```
            <!-- panels:end -->

            > [!] 新老版本之间的区别就是，不用手动注册驱动了。新版本可以直接通过`DriverManager.getConnection()`获取连接，只要classpath存在合适的驱动包。
            <br>比如`mysql-connector-java-5.1.34.jar`或者`sqljdbc4.jar`。之所以可以这样，是因为：
            <br><br>1). 调用`DriverManager.getConnection()`代码会进行`DriverManager`类加载。而`DriverManager`属于java.sql.包。根据类加载器的特性，这个类会被`BootstrapClassLoader`进行加载并且会初始化静态方法。近而调用`loadInitialDrivers()`方法。
            <br><br>2). 方法里面分为两部分，一个是获取系统属性`jdbc.drivers`的字符串进行:分割，使用`Class.forName(aDriver, true,ClassLoader.getSystemClassLoader());`进行加载并 ___初始化___ ，注意使用的是`ClassLoader.getSystemClassLoader()`系统类加载器，因为`BootstrapClassLoader`本身加载不了。
            <br><br>3). 另外一个是使用SPI进行加载的。使用spi加载的时候由前面介绍的 [ServiceLoader初始化第11行](#serviceloader) 知道。它会使用当前线程的类加载器。一般默认为`AppClassLoader`也既系统类加载器。然后查找classpath下面jar包中的`META-INF/services/`目录下的`java.sql.Driver`的实现。然后使用线程类加载器加载里面定义的类[并没有初始化]`c = Class.forName(cn, false, loader);`，获取到类后通过反射`S p = service.cast(c.newInstance());`实例化对象并进行 ___初始化___ 。
            <br><br>4). 不管上述哪一部分，都是 ___初始化___ 调用三方厂商实现的驱动里面的静态方法将自己注册到驱动管理器里面。近而省略手动加载的步骤，实现自动加载。

            ```java
            public class DriverManager {
                /**
                 * Load the initial JDBC drivers by checking the System property
                 * jdbc.properties and then use the {@code ServiceLoader} mechanism
                 */
                static {
                    loadInitialDrivers();
                    println("JDBC DriverManager initialized");
                }

                private static void loadInitialDrivers() {
                    String drivers;
                    try {
                        drivers = AccessController.doPrivileged(new PrivilegedAction<String>() {
                            public String run() {
                                return System.getProperty("jdbc.drivers");
                            }
                        });
                    } catch (Exception ex) {
                        drivers = null;
                    }
                    // If the driver is packaged as a Service Provider, load it.
                    // Get all the drivers through the classloader
                    // exposed as a java.sql.Driver.class service.
                    // ServiceLoader.load() replaces the sun.misc.Providers()

                    AccessController.doPrivileged(new PrivilegedAction<Void>() {
                        public Void run() {

                            ServiceLoader<Driver> loadedDrivers = ServiceLoader.load(Driver.class);
                            Iterator<Driver> driversIterator = loadedDrivers.iterator();

                            /* Load these drivers, so that they can be instantiated.
                            * It may be the case that the driver class may not be there
                            * i.e. there may be a packaged driver with the service class
                            * as implementation of java.sql.Driver but the actual class
                            * may be missing. In that case a java.util.ServiceConfigurationError
                            * will be thrown at runtime by the VM trying to locate
                            * and load the service.
                            *
                            * Adding a try catch block to catch those runtime errors
                            * if driver not available in classpath but it's
                            * packaged as service and that service is there in classpath.
                            */
                            try{
                                while(driversIterator.hasNext()) {
                                    driversIterator.next();
                                }
                            } catch(Throwable t) {
                            // Do nothing
                            }
                            return null;
                        }
                    });

                    println("DriverManager.initialize: jdbc.drivers = " + drivers);

                    if (drivers == null || drivers.equals("")) {
                        return;
                    }
                    String[] driversList = drivers.split(":");
                    println("number of Drivers:" + driversList.length);
                    for (String aDriver : driversList) {
                        try {
                            println("DriverManager.Initialize: loading " + aDriver);
                            Class.forName(aDriver, true,
                                    ClassLoader.getSystemClassLoader());
                        } catch (Exception ex) {
                            println("DriverManager.Initialize: load failed: " + ex);
                        }
                    }
                }
            }
            ```

    + ### Reference

        - https://docs.oracle.com/javadb/10.8.3.0/ref/rrefjdbc4_0summary.html

* ## 范型&通配符

* ## 注解

* ## 语法糖

* ## 新特性

* ## Reference

  + https://drive.google.com/file/d/13OR290XMvjjkMZ9pzOumxas6eEvU7NNa/view?usp=sharing
