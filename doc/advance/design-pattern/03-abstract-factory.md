# 抽象工厂模式

* ## 描述

    > [?] 抽象工厂模式（Abstract Factory Pattern）是围绕一个超级工厂创建其他工厂。该超级工厂又称为其他工厂的工厂。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。
    <br><br>在抽象工厂模式中，接口是负责创建一个相关对象的工厂，不需要显式指定它们的类。每个生成的工厂都能按照工厂模式提供对象。
    <br><br>抽象工厂模式提供了一种创建一系列相关或相互依赖对象的接口，而无需指定具体实现类。通过使用抽象工厂模式，可以将客户端与具体产品的创建过程解耦，使得客户端可以通过工厂接口来创建一族产品。

* ## UML

* ## 代码实现

    <!-- tabs:start -->
    ### **Main.java**
    ```java
    public class Main {
        public static void main(String[] args) {
            //获取形状工厂
            AbstractFactory shapeFactory = FactoryProducer.getFactory("SHAPE");

            // 会出现NPE
            // Color color = shapeFactory.getColor("everything");
            Shape shape1 = shapeFactory.getShape("CIRCLE");
            shape1.draw();

            //获取颜色工厂
            AbstractFactory colorFactory = FactoryProducer.getFactory("COLOR");

            Color color1 = colorFactory.getColor("RED");
            color1.fill();
        }
    }
    ```
    ### **FactoryProducer.java**
    ```java
    public class FactoryProducer {
        public static AbstractFactory getFactory(String choice){
            if(choice.equalsIgnoreCase("SHAPE")){
                return new ShapeFactory();
            } else if(choice.equalsIgnoreCase("COLOR")){
                return new ColorFactory();
            }
            return null;
        }
    }
    ```
    ### **AbstractFactory.java**
    ```java
    public abstract class AbstractFactory {

        public abstract Color getColor(String color);

        public abstract Shape getShape(String shape);

    }
    ```
    <!-- tabs:end -->

    <!-- tabs:start -->
    ### **ShapeFactory.java**
    ```java
    public class ShapeFactory extends AbstractFactory {

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

        @Override
        public Color getColor(String color) {
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

    <!-- tabs:start -->
    ### **ColorFactory.java**
    ```java
    public class ColorFactory extends AbstractFactory {
        @Override
        public Shape getShape(String shapeType){
            return null;
        }

        @Override
        public Color getColor(String color) {
            if(color == null){ return null; }

            if(color.equalsIgnoreCase("RED")){
                return new Red();
            } else if(color.equalsIgnoreCase("GREEN")){
                return new Green();
            } else if(color.equalsIgnoreCase("BLUE")){
                return new Blue();
            }
            return null;
        }
    }
    ```
    ### **Color.java**
    ```java
    public interface Color {
        void fill();
    }
    ```
    ### **Blue.java**
    ```java
    public class Blue implements Color {
        @Override
        public void fill() {
            System.out.println("Inside Blue::fill() method.");
        }
    }
    ```
    ### **Green.java**
    ```java
    public class Green implements Color {
        @Override
        public void fill() {
            System.out.println("Inside Green::fill() method.");
        }
    }
    ```
    ### **Red.java**
    ```java
    public class Red implements Color {
        @Override
        public void fill() {
            System.out.println("Inside Red::fill() method.");
        }
    }
    ```
    <!-- tabs:end -->

    ![](/.images/doc/advance/design-pattern/dp-abstract-factory-01.png ':size=80%')

* ## Reference

    + https://www.runoob.com/design-pattern/factory-pattern.html
