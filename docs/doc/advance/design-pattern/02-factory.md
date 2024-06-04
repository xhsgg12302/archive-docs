# 工厂模式

* ## 描述

    > [?] 工厂模式提供了一种创建对象的方式，而无需指定要创建的具体类。
    <br><br>工厂模式属于创建型模式，它在创建对象时提供了一种封装机制，将实际创建对象的代码与使用代码分离。

* ## UML

* ## 代码实现

    ```java
    public class Main {
        public static void main(String[] args) {

            ShapeFactory shapeFactory = new ShapeFactory();

            Shape shape1 = shapeFactory.getShape("CIRCLE");
            shape1.draw();

            Shape shape2 = shapeFactory.getShape("RECTANGLE");
            shape2.draw();

            Shape shape3 = shapeFactory.getShape("SQUARE");
            shape3.draw();
        }
    }
    ```

    <!-- tabs:start -->
    ### **ShapeFactory.java**
    ```java
    public class ShapeFactory {
        
        //使用 getShape 方法获取形状类型的对象
        public Shape getShape(String shapeType){
            if(shapeType == null){ return null; }

            if(shapeType.equalsIgnoreCase("CIRCLE")){
                return new Circle();
            } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
                return new Rectangle();
            } else if(shapeType.equalsIgnoreCase("SQUARE")){
                return new Square();
            }
            return null;
        }
    }
    ```
    ### **Shape.java**
    ```java
    public interface Shape {
        void draw();
    }
    ```
    ### **Circle.java**
    ```java
    public class Circle implements Shape {
        @Override
        public void draw() {
            System.out.println("Inside Circle::draw() method.");
        }
    }
    ```
    ### **Rectangle.java**
    ```java
    public class Rectangle implements Shape {
        @Override
        public void draw() {
            System.out.println("Inside Rectangle::draw() method.");
        }
    }
    ```
    ### **Square.java**
    ```java
    public class Square implements Shape {
        @Override
        public void draw() {
            System.out.println("Inside Square::draw() method.");
    }
    ```
    <!-- tabs:end -->

    ![](/.images/doc/advance/design-pattern/dp-factory-01.png ':size=80%')

* ## Reference

    + https://www.runoob.com/design-pattern/factory-pattern.html
