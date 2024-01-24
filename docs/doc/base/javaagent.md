## JavaAgent
?> 通过实现agent类中的premain方法将自定义的类转换器注册到JVM中，从而实现类加载之前**对字节码进行处理**。本次实验中对类未进行处理，而是在加载类之前打印输出。
<br>完整代码可[参考](https://github.com/12302-bak/idea-test-project/tree/learning/_6_un_JTW)

* ### 代码实现

<!-- tabs:start -->
#### **MyAgent**
```java
package agent;

import java.lang.instrument.ClassFileTransformer;
import java.lang.instrument.Instrumentation;

public class MyAgent {
    public static void premain(String args, Instrumentation instrumentation){
        ClassFileTransformer classFileTransformer = new MyTransfor();
        instrumentation.addTransformer(classFileTransformer);
    }
}
```
#### **MyTransfor**
```java
package agent;

import java.lang.instrument.ClassFileTransformer;
import java.lang.instrument.IllegalClassFormatException;
import java.security.ProtectionDomain;

public class MyTransfor implements ClassFileTransformer {
    @Override
    public byte[] transform(ClassLoader loader, String className, Class<?> classBeingRedefined, ProtectionDomain protectionDomain, byte[] classfileBuffer) throws IllegalClassFormatException {

        /*_12302_2019-07-26_<此处可以静态代理>*/
        System.out.println("from: " + className + "--> hello agent!");

        /*_12302_2019-07-26_<return null 并不表示将要加载的类清空,而是不往其中添加任何东西>*/
        return null;
    }
}
```
#### **TestMain**
```java
package demo;

public class TestMain {
    public static void main(String[] args) {
        System.out.println("this is _base.base.demo.TestMain.main(),and nothing else");
    }
}
```
<!-- tabs:end -->

* ### 构建及运行
!> 使用maven-assembly-plugin插件进行构建的。在`assembly.xml`配置中排除主类包，并在pom.xml`assembly`插件配置中定义了生成`META-INF/MANIFEST.MF`文件且包含`Premain-Class: agent.MyAgent` 属性。

    ```shell
    # 进入JTW主目录
    cd _6_un_JTW

    # 构建agent,因为pom.xml文件中定义的 assembly.single 绑定声明周期为package。所以需要package处理。
    mvn -Dmaven.test.skip=true clean package

    # 查看jar文件列表
    jar -tf target/agent/custom-agent-1.0-SNAPSHOT.jar

    # 提取并检查`META-INF/MANIFEST.MF`中是否存在`Premain-Class: agent.MyAgent` 
    jar xf target/agent/custom-agent-1.0-SNAPSHOT.jar  META-INF/MANIFEST.MF

    # 运行TestMain主类查看效果
    java -cp target/classes  -javaagent:target/agent/custom-agent-1.0-SNAPSHOT.jar demo.TestMain
    ```

    ![](/.images/doc/base/agent/agent-intro-01.png ':size=70%')


## Reference
* https://github.com/12302-bak/idea-test-project/tree/learning/_6_un_JTW
* https://mp.weixin.qq.com/s/wOtc4_GOEBymM6GwNW3tag