* ## Intro(FactoryBean)

    > [?] `spring.version = 4.3.7.RELEASE`

    + ### 准备编码

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ```java
        // 创建普通bean
        @Data
        @NoArgsConstructor
        @AllArgsConstructor
        public class Tool {

            private int id;

        }

        // 创建工厂
        @Data
        public class ToolFactory implements FactoryBean<Tool> {

            private int factoryId;

            private int toolId;

            @Override
            public Tool getObject() throws Exception {
                return new Tool(toolId);
            }

            @Override
            public Class<?> getObjectType() {
                return Tool.class;
            }

            @Override
            public boolean isSingleton() {
                return false;
            }
        }
        ```
        <!-- div:right-panel-50 -->
        ```xml
        <!-- factory-bean.xml -->

        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

            <!-- FactoryBean work process -->
            <bean id="toolFB" class="_framework.spring.factory_bean.beans.ToolFactory">
                <property name="factoryId" value="9090" />
                <property name="toolId" value="2" />
            </bean>

        </beans>
        ```
        ```java
        public class SpringFactoryBeanTest {

            public static void main(String[] args) {

                ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("_framework/spring/res/factory-bean.xml");
                ToolFactory fb = applicationContext.getBean("&toolFB", ToolFactory.class);
                Tool tool1 = applicationContext.getBean(Tool.class);
                Tool tool2 = applicationContext.getBean(Tool.class);
                System.out.println();
                applicationContext.destroy();

            }

        }
        ```
        <!-- panels:end -->

    + ### 解析流程

* ## Reference
    + https://www.jianshu.com/p/271cefdd708c