# 策略模式

* ## 描述

    ?> 在策略模式（Strategy Pattern）中一个类的行为或其算法可以在运行时更改。这种类型的设计模式属于行为型模式。
    <br><br>在策略模式定义了一系列算法或策略，并将每个算法封装在独立的类中，使得它们可以互相替换。通过使用策略模式，可以在运行时根据需要选择不同的算法，而不需要修改客户端代码。
    <br><br>在策略模式中，我们创建表示各种策略的对象和一个行为随着策略对象改变而改变的 context 对象。策略对象改变 context 对象的执行算法。

    !> 我们将创建一个定义活动的 Strategy 接口和实现了 Strategy 接口的实体策略类。Context 是一个使用了某种策略的类。StrategyPatternDemo，我们的演示类使用 Context 和策略对象来演示 Context 在它所配置或使用的策略改变时的行为变化。

* ## UML

* ## 代码实现

    ```java
    public class Main {
        
        public static void main(String[] args) {

            Context context = new Context(new OperationAdd());
            System.out.println("10 + 5 = " + context.executeStrategy(10, 5));

            context = new Context(new OperationSubtract());
            System.out.println("10 - 5 = " + context.executeStrategy(10, 5));

            context = new Context(new OperationMultiply());
            System.out.println("10 * 5 = " + context.executeStrategy(10, 5));
        }
    }
    ```
    ```java
    public class Context {
        private Strategy strategy;

        public Context(Strategy strategy){
            this.strategy = strategy;
        }

        public int executeStrategy(int num1, int num2){
            return strategy.doOperation(num1, num2);
        }
    }
    ```
    ```java
    public interface Strategy {
        public int doOperation(int num1, int num2);
    }
    ```

    <!-- tabs:start -->
    ### **OperationAdd.java**
    ```java
    public class OperationAdd implements Strategy{
        @Override
        public int doOperation(int num1, int num2) {
            return num1 + num2;
        }
    }
    ```
    ### **OperationSubtract.java**
    ```java
    public class OperationSubtract implements Strategy{
        @Override
        public int doOperation(int num1, int num2) {
            return num1 - num2;
        }
    }
    ```
    ### **OperationMultiply.java**
    ```java
    public class OperationMultiply implements Strategy{
        @Override
        public int doOperation(int num1, int num2) {
            return num1 * num2;
        }
    }
    ```
    <!-- tabs:end -->

    ![](/.images/doc/advance/design-pattern/dp-strategy-01.png ':size=80%')

* ## Reference

    + https://www.runoob.com/design-pattern/strategy-pattern.html
