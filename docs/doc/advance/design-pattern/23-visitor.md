# 访问者模式

* ## 描述

    ?> 在访问者模式（Visitor Pattern）中，我们使用了一个访问者类，它改变了元素类的执行算法。通过这种方式，元素的执行算法可以随着访问者改变而改变。这种类型的设计模式属于行为型模式。根据模式，元素对象已接受访问者对象，这样访问者对象就可以处理元素对象上的操作。

    !> 访问模式可以有变形，比如有些写法中被访问的是抽象类，而另外的是接口实现。
    <br><br>[`ASM`](https://asm.ow2.io/)里面就用到了访问者模式来处理字节码信息。

* ## UML

* ## 代码实现

    ```java
    public class Main {
        public static void main(String[] args) {

            List<Staff> staffs = new ArrayList<>();
            staffs.add(new Manager("经理-A"));
            staffs.add(new Manager("经理-B"));
            staffs.add(new Engineer("工程师-A"));
            staffs.add(new Engineer("工程师-B"));
            staffs.add(new Engineer("工程师-C"));
            staffs.add(new Engineer("工程师-D"));

            CEOVisitor ceoVisitor = new CEOVisitor();
            for (Staff staff : staffs) {
                staff.accept(ceoVisitor);
            }

            System.out.println();

            CTOVisitor ctoVisitor = new CTOVisitor();
            for (Staff staff : staffs) {
                staff.accept(ctoVisitor);
            }
        }
    }
    ```

    <!-- panels:start -->
    <!-- div:title-panel -->
    ### active(访问)
    <!-- tabs:start -->
    #### **Visitor.java**
    ```java
    public interface Visitor {
        void visit(Engineer engineer);
        void visit(Manager manager);
    }
    ```
    #### **CEOVisitor.java**
    ```java
    public class CEOVisitor implements Visitor {

        @Override
        public void visit(Engineer engineer) {
            System.out.println("CEO -visit-> 工程师: " + engineer.name + ", KPI: " + engineer.kpi);
        }

        @Override
        public void visit(Manager manager) {
            System.out.println("CEO -visit-> 经理: " + manager.name + ", KPI: " + manager.kpi +
                    ", 新产品数量: " + manager.getProducts());
        }
    }
    ```
    #### **CTOVisitor.java**
    ```java
    public class CTOVisitor implements Visitor {

        @Override
        public void visit(Engineer engineer) {
            System.out.println("CTO -visit-> 工程师: " + engineer.name + ", 代码行数: " + engineer.getCodeLines());
        }

        @Override
        public void visit(Manager manager) {
            System.out.println("CTO -visit-> 经理: " + manager.name + ", 产品数量: " + manager.getProducts());
        }
    }
    ```
    <!-- tabs:end -->
    <!-- panels:end -->

    <!-- panels:start -->
    <!-- div:title-panel -->
    ### passive(被访问)
    <!-- tabs:start -->
    #### **Staff.java**
    ```java
    public abstract class Staff {

        public String name;
        public int kpi;// 员工KPI

        public Staff(String name) {
            this.name = name;
            kpi = new Random().nextInt(10);
        }
        // 核心方法，接受Visitor的访问
        public abstract void accept(Visitor visitor);
    }
    ```
    #### **Engineer.java**
    ```java
    public class Engineer extends Staff {

        public Engineer(String name) {
            super(name);
        }

        @Override
        public void accept(Visitor visitor) {
            visitor.visit(this);
        }
        // 工程师一年的代码数量
        public int getCodeLines() {
            return new Random().nextInt(10 * 10000);
        }
    }
    ```
    #### **Manager.java**
    ```java
    public class Manager extends Staff {

        public Manager(String name) {
            super(name);
        }

        @Override
        public void accept(Visitor visitor) {
            visitor.visit(this);
        }

        // 一年做的产品数量
        public int getProducts() {
            return new Random().nextInt(10);
        }
    }
    ```
    <!-- tabs:end -->
    <!-- panels:end -->

    ![](/.images/doc/advance/design-pattern/dp-visitor-01.png ':size=80%')

* ## Reference

    + https://www.jianshu.com/p/1f1049d0a0f4
    + https://www.runoob.com/design-pattern/visitor-pattern.html
