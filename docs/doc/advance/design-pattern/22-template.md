# 模板模式

* ## 描述

    ?> 在模板模式（Template Pattern）中，一个抽象类公开定义了执行它的方法的方式/模板。它的子类可以按需要重写方法实现，但调用将以抽象类中定义的方式进行。这种类型的设计模式属于行为型模式。

* ## UML

* ## 代码实现

    ```java
    public class Main {
        public static void main(String[] args) {

            Game game = new Cricket();
            game.play();

            game = new Football();
            game.play();
        }
    }
    ```

    <!-- tabs:start -->
    ### **Game.java**
    ```java
    public abstract class Game {
        abstract void initialize();
        abstract void startPlay();
        abstract void endPlay();
        //模板
        public final void play(){
            //初始化游戏
            initialize();
            //开始游戏
            startPlay();
            //结束游戏
            endPlay();
        }
    }
    ```
    ### **Cricket.java**
    ```java
    public class Cricket extends Game {

        @Override
        void endPlay() {
            System.out.println("Cricket Game Finished!");
        }

        @Override
        void initialize() {
            System.out.println("Cricket Game Initialized! Start playing.");
        }

        @Override
        void startPlay() {
            System.out.println("Cricket Game Started. Enjoy the game!");
        }
    }
    ```
    ### **Football.java**
    ```java
    public class Football extends Game {

        @Override
        void endPlay() {
            System.out.println("Football Game Finished!");
        }

        @Override
        void initialize() {
            System.out.println("Football Game Initialized! Start playing.");
        }

        @Override
        void startPlay() {
            System.out.println("Football Game Started. Enjoy the game!");
        }
    }
    ```
    <!-- tabs:end -->

    ![](/.images/doc/advance/design-pattern/dp-template-01.png ':size=80%')

* ## Reference

    + https://www.runoob.com/design-pattern/template-pattern.html
