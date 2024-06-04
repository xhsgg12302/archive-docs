# 单例模式

* ## 描述

    > [?] 单例模式（Singleton Pattern）是 Java 中最简单的设计模式之一。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。
    <br><br>这种模式涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。
    <br><br>单例模式是一种创建型设计模式，它确保一个类只有一个实例，并提供了一个全局访问点来访问该实例。

* ## UML

* ## 代码实现

    ```java
    public class Main {
        public static void main(String[] args) {
            //获取唯一可用的对象
            SingleObject object = SingleObject.getInstance();
            //显示消息
            object.showMessage();
        }
    }
    ```

    <!-- tabs:start -->
    ### **懒汉式**

    ```java
    public class Singleton {  
        private Singleton (){}
        private static Singleton instance;  
        public static /*synchronized*/ Singleton getInstance() {  
            if (instance == null) {  
                instance = new Singleton();  
            }  
            return instance;  
        }  
    }
    ```
    ### **饿汉式**

    ```java
    public class Singleton {  
        private Singleton (){}
        private static Singleton instance = new Singleton();
        public static Singleton getInstance() {
            return instance;
        }
    }
    ```
    ### **双检锁/双重校验锁[DCL]**

    ```java
    public class Singleton {  
        private Singleton (){}
        private volatile static Singleton singleton;  
        public static Singleton getSingleton() {  
            if (singleton == null) {  
                synchronized (Singleton.class) {  
                    if (singleton == null) {  
                        singleton = new Singleton();  
                    }  
                }  
            }  
            return singleton;  
        }
    }
    ```
    ### **登记式/静态内部类**

    ```java
    public class Singleton {  
        private Singleton (){}
        private static class SingletonHolder {  
            private static final Singleton INSTANCE = new Singleton();  
        }  
        public static final Singleton getInstance() {  
            return SingletonHolder.INSTANCE;  
        }  
    }
    ```
    ### **枚举**

    ```java
    public enum Singleton {  
        INSTANCE;  
        public void whateverMethod() {  
        }  
    }
    ```
    <!-- tabs:end -->

* ## Reference

    + https://www.runoob.com/design-pattern/singleton-pattern.html
