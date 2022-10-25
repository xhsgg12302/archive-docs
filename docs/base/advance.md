## 类加载器
* 加载流程
* 父子类初始化顺序
    > 父，子，静态代码块  -- 和静态变量  等级初始化 <br>
    > 父，子，普通代码块  -- 和普通变量  -- 构造方法（最后）  等级初始化 <br>
    > 继续new Parent()，只执行父类的普通属性赋值和普通代码块，最后父类构造方法 <br>
    > 继续new Child(), 执行父类的普通属性和代码块，构造方法，然后是子类的
    
    <details><summary>代码示例</summary>

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

    class Child extends Parent {
        { System.out.println("子类：普通代码块");}
        private static String staticStringInChild = initStaticStringInChild();
        static { System.out.println("子类：静态代码块"); }
        private String stringInChild = initStringInChild();
        public Child() { System.out.println("子类：构造方法"); }

        private static String initStaticStringInChild() {System.out.println("子类：静态方法，被静态成员变量赋值调用。"); return "initStaticStringInChild";}
        private String initStringInChild() {System.out.println("子类：普通成员方法，被普通成员变量赋值调用。"); return "initStringInChild";}
    }
  
    output: 
    父类：静态代码块
    父类：静态方法，被静态成员变量赋值调用。
    子类：静态方法，被静态成员变量赋值调用。
    子类：静态代码块
    父类：普通成员方法，被普通成员变量赋值调用。
    父类：普通代码块
    父类：构造方法
    子类：普通代码块
    子类：普通成员方法，被普通成员变量赋值调用。
    子类：构造方法

    ===============================

    父类：普通成员方法，被普通成员变量赋值调用。
    父类：普通代码块
    父类：构造方法
    子类：普通代码块
    子类：普通成员方法，被普通成员变量赋值调用。
    子类：构造方法
    ```
    </details>

## 动态代理
* 简介
    > 动态代理就是，在程序运行期，创建目标对象的代理对象，并对目标对象中的方法进行功能性增强的一种技术。
    > 在生成代理对象的过程中，目标对象不变，代理对象中的方法是目标对象方法的增强方法。
    > 可以理解为运行期间，对象中方法的动态拦截，在拦截方法的前后执行功能操作。<br>
* 两种方式
    + JDK
        > 基于接口的动态代理，代理类必须实现某个接口。<br>
        > 代理配置： -Dsun.misc.ProxyGenerator.saveGeneratedFiles=true  源码：`ProxyGenerator`。可以生成代理类字节文件，方便反编译查看生成内容。<br>
        > repo: [jdk_dynamic](https://github.com/xhsgg12302/idea-test-project/tree/master/_0_base-learning/src/main/java/_base/proxy/jdk_dynamic)

        <details><summary>代码示例</summary>

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
    
    + CGLIB
        > 基于ASM字节码操作框架。由于继承关系，不能代理final类。<br>
        > 代理类生成配置： -Dcglib.debugLocation=/path/to/somewhere   源码：`DebuggingClassWriter`  <br>
        > cglib生成代理类的过程当中会生成一些辅助类，所以打断点获取Class字节数组的时候需要注意，一般来说最后一次生成的才是正真的Proxy。<br>
        > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 1. class net.sf.cglib.proxy.Enhancer$EnhancerKey$$KeyFactoryByCGLIB$$7fb24d72 <br>
        > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 2. class net.sf.cglib.core.MethodWrapper$MethodWrapperKey$$KeyFactoryByCGLIB$$d45e49f7 <br>
        > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 3. class _base.proxy.cglib_dynamic.StudentNoIntfs$$EnhancerByCGLIB$$81f8e23d <br>
        > repo: [cglib-dynamic](https://github.com/xhsgg12302/idea-test-project/tree/master/_0_base-learning/src/main/java/_base/proxy/cglib_dynamic)
        
        <details><summary>代码示例</summary>

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
    
        <details><summary>代码示例</summary>

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



## 反射

## 序列化

## SPI

## 范型&通配符

## 注解

## 语法糖

## 新特性
