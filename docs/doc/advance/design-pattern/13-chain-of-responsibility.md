# 责任链模式

* ## 描述

    ?> 顾名思义，责任链模式（Chain of Responsibility Pattern）为请求创建了一个接收者对象的链。这种模式给予请求的类型，对请求的发送者和接收者进行解耦。这种类型的设计模式属于行为型模式。
    <br><br>在这种模式中，通常每个接收者都包含对另一个接收者的引用。如果一个对象不能处理该请求，那么它会把相同的请求传给下一个接收者，依此类推。

    !> 我们创建抽象类 AbstractLogger，带有详细的日志记录级别。然后我们创建三种类型的记录器，都扩展了 AbstractLogger。每个记录器消息的级别是否属于自己的级别，如果是则相应地打印出来，否则将不打印并把消息传给下一个记录器。

* ## UML

* ## 代码实现

    ```java
    public class Main {
        private static AbstractLogger getChainOfLoggers(){

            AbstractLogger debugLogger = new DebugLogger(AbstractLogger.DEBUG);
            AbstractLogger infoLogger = new InfoLogger(AbstractLogger.INFO);
            AbstractLogger errorLogger = new ErrorLogger(AbstractLogger.ERROR);

            debugLogger.setNextLogger(infoLogger);
            infoLogger.setNextLogger(errorLogger);

            return debugLogger;
        }

        public static void main(String[] args) {
            AbstractLogger loggerChain = getChainOfLoggers();

            loggerChain.logMessage(AbstractLogger.DEBUG, "This is a debug level information.");

            loggerChain.logMessage(AbstractLogger.INFO, "This is an info level information.");

            loggerChain.logMessage(AbstractLogger.ERROR, "This is an error level information.");
        }
    }
    ```

    <!-- tabs:start -->
    ### **AbstractLogger.java**
    ```java
    public abstract class AbstractLogger {
        public static int DEBUG = 1;
        public static int INFO = 2;
        public static int ERROR = 3;

        protected int level;

        //责任链中的下一个元素
        protected AbstractLogger nextLogger;

        public void setNextLogger(AbstractLogger nextLogger){
            this.nextLogger = nextLogger;
        }

        public void logMessage(int level, String message){
            if(this.level >= level){
                write(message);
            }
            if(nextLogger != null){
                nextLogger.logMessage(level, message);
            }else{ System.out.println();}
        }

        abstract protected void write(String message);
    }
    ```
    ### **DebugLogger.java**
    ```java
    public class DebugLogger extends AbstractLogger {

        public DebugLogger(int level){
            this.level = level;
        }

        @Override
        protected void write(String message) {
            System.out.println("Debug::Logger: " + message);
        }
    }
    ```
    ### **InfoLogger.java**
    ```java
    public class InfoLogger extends AbstractLogger {

        public InfoLogger(int level){
            this.level = level;
        }

        @Override
        protected void write(String message) {
            System.out.println("Info::Logger: " + message);
        }
    }
    ```
    ### **ErrorLogger.java**
    ```java
    public class ErrorLogger extends AbstractLogger {

        public ErrorLogger(int level){
            this.level = level;
        }

        @Override
        protected void write(String message) {
            System.out.println("Error::Logger: " + message);
        }
    }
    ```
    <!-- tabs:end -->

    ![](/.images/doc/advance/design-pattern/dp-chain-of-responsibility-01.png ':size=80%')

* ## Reference

    + https://www.runoob.com/design-pattern/chain-of-responsibility-pattern.html
