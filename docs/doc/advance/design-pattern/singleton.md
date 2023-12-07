# 单例模式

## 描述

## UML

## 代码实现
<!-- tabs:start -->
### **懒汉式[线程不安全]**
```java
public class Singleton {  
    private Singleton (){}
    private static Singleton instance;
    public static Singleton getInstance() {  
        if (instance == null) {  
            instance = new Singleton();  
        }  
        return instance;  
    }
}
```

### **懒汉式[线程安全]**
```java
public class Singleton {  
    private Singleton (){}
    private static Singleton instance;  
    public static synchronized Singleton getInstance() {  
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
```java
public class Entrance {
   public static void main(String[] args) {
      //获取唯一可用的对象
      SingleObject object = SingleObject.getInstance();
      //显示消息
      object.showMessage();
   }
}
```

## Reference
* https://www.runoob.com/design-pattern/singleton-pattern.html
