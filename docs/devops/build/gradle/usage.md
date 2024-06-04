
* ## 目录结构
    
    > [?] Gradle uses two main directories to perform and manage its work: the Gradle User Home directory and the Project Root directory.[参考](https://docs.gradle.org/current/userguide/directory_layout.html)

    ![](/.images/devops/build/gradle/usage/gradle-usage-02.png ':size=49%')
    ![](/.images/devops/build/gradle/usage/gradle-usage-03.png ':size=50%')

* ## 初始化脚本

    > [?] 在项目构建之前进行初始化。[参考](https://docs.gradle.org/current/userguide/init_scripts.html#init_scripts)

    + ### 基本用法

    > [?] 初始化脚本（又名init 脚本）与 Gradle 中的其他脚本类似。但是，这些脚本是在构建开始之前运行的。
    <br>以下是几种可能的用途：
    
        - 设置企业范围的配置，例如在哪里可以找到自定义插件。
        - 根据当前环境设置属性，例如开发人员的计算机与持续集成服务器。
        - 提供构建所需的有关用户的个人信息，例如存储库或数据库身份验证凭据。
        - 定义特定于计算机的详细信息，例如 JDK 的安装位置。
        - 注册构建监听器。希望监听 Gradle 事件的外部工具可能会发现这很有用。
        - 注册构建记录器。您可以自定义 Gradle 如何记录它生成的事件。

    > [!] 初始化脚本的一个主要限制是它们无法访问buildSrc项目中的类。

    + ### 使用初始化脚本

    > [?] 使用 init 脚本有多种方法：
    <br>如果找到多个 init 脚本，它们都将按照下面指定的顺序执行。
    <br>给定目录中的脚本按字母顺序执行。例如，工具可以在命令行上指定一个 init 脚本，并在主目录中指定另一个 init 脚本来定义环境。这两个脚本都会在 Gradle 执行时运行。 

        - 在命令行上指定一个文件。命令行选项后面是`-I,--init-script `的路径。(命令行选项可以出现多次，每次都会添加另一个初始化脚本。如果命令行上指定的任何文件不存在，构建将失败。)
        - init.gradle将一个名为（或init.gradle.ktsKotlin）的文件放入该目录中。$GRADLE_USER_HOME/
        - .gradle将以（或.init.gradle.ktsKotlin）结尾的文件放入目录中。$GRADLE_USER_HOME/init.d/
        - .gradle将以（或.init.gradle.ktsKotlin）结尾的文件放入Gradle 发行版的目录中。$GRADLE_HOME/init.d/

    + ### 编写初始化脚本

    > [?] 与 Gradle 构建脚本一样，init 脚本是 Groovy 或 Kotlin 脚本。每个 init 脚本都有一个与其关联的[Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.invocation.Gradle.html)实例。初始化脚本中的任何属性引用和方法调用都将委托给此Gradle实例。
    <br>每个 init 脚本还实现[Script](https://docs.gradle.org/current/dsl/org.gradle.api.Script.html)接口。

        - #### 从初始化脚本配置项目

            > [?] 您可以使用 init 脚本来配置构建中的项目。这与在多项目构建中配置项目类似。
            <br>以下示例展示了如何在评估项目之前从 init 脚本执行额外配置：
            <br>此示例使用此功能来配置仅用于特定环境的附加repo。

            ```groovy
            // build.gradle
            repositories {
                jcenter {
                    name = "jcenter"
                }
            }

            tasks.register('showRepos') {
                def repositoryNames = repositories.collect { it.name }
                doLast {
                    println "All repos:"
                    println repositoryNames
                }
            }

            // init.gradle
            allprojects {
                repositories {
                    mavenLocal { name 'maven-local' }
                    maven {
                        name 'aliyun'
                        url "http://maven.aliyun.com/nexus/content/groups/public"
                    }
                    maven {
                        name 'wtfu'
                        url 'http://mvn.wtfu.site/nexus/content/groups/public'
                    }
                    maven { 
                        name 'sonatype'
                        url 'https://oss.sonatype.org/content/repositories/snapshots/'
                    }
                    //jcenter()
                    //google { name 'google' }
                    mavenCentral { name 'maven-central' }
                }
            }
            ```
            运行结果:

            ![](/.images/devops/build/gradle/usage/gradle-usage-04.png ':size=99%')

    + ### init 脚本的外部依赖项

    > [?] Init 脚本还可以声明与该initscript()方法的依赖关系，传入一个声明 init 脚本类路径的闭包。
    <br><br>还可以传递给该initscript()方法的闭包配置一个[ScriptHandler](https://docs.gradle.org/current/javadoc/org/gradle/api/initialization/dsl/ScriptHandler.html)实例。您可以通过向配置添加依赖项来声明 init 脚本类路径classpath。
    <br>例如，这与声明 Java 编译类路径的方式相同。您可以使用[声明依赖项中](https://docs.gradle.org/current/userguide/declaring_dependencies.html#declaring-dependencies)描述的任何依赖项类型，项目依赖项除外。
    <br>声明 init 脚本类路径后，您可以像使用类路径上的任何其他类一样使用 init 脚本中的类。以下示例添加到前面的示例中，并使用 init 脚本类路径中的类。
    <br>具有外部依赖项的初始化脚本：

    ```groovy
    // init.grdle
    import org.apache.commons.math.fraction.Fraction

    initscript {
        repositories {
            mavenCentral()
        }
        dependencies {
            classpath 'org.apache.commons:commons-math:2.0'
        }
    }

    println Fraction.ONE_FIFTH.multiply(2)

    // build.gradle
    tasks.register('doNothing')
    ```

    运行结果:
    
    ![](/.images/devops/build/gradle/usage/gradle-usage-05.png ':size=99%')

    + ### 初始化脚本插件

    > [?] 与 Gradle 构建脚本或 Gradle 设置文件一样，插件可以应用于 init 脚本。
    <br>在初始化脚本中使用插件： 

    ```groovy
    // init.gradle
    apply plugin: EnterpriseRepositoryPlugin

    class EnterpriseRepositoryPlugin implements Plugin<Gradle> {

        private static String ENTERPRISE_REPOSITORY_URL = "https://repo.gradle.org/gradle/repo"

        void apply(Gradle gradle) {
            // ONLY USE ENTERPRISE REPO FOR DEPENDENCIES
            gradle.allprojects { project ->
                project.repositories {

                    // Remove all repositories not pointing to the enterprise repository url
                    all { ArtifactRepository repo ->
                        if (!(repo instanceof MavenArtifactRepository) ||
                            repo.url.toString() != ENTERPRISE_REPOSITORY_URL) {
                            project.logger.lifecycle "Repository ${repo.url} removed. Only $ENTERPRISE_REPOSITORY_URL is allowed"
                            remove repo
                        }
                    }

                    // add the enterprise repository
                    maven {
                        name "STANDARD_ENTERPRISE_REPO"
                        url ENTERPRISE_REPOSITORY_URL
                    }
                }
            }
        }
    }

    // build.gradle
    repositories{
        mavenCentral()
    }

    @Immutable
    class RepositoryData {
        String name
        URI url
    }

    tasks.register('showRepositories') {
        def repositoryData = repositories.collect { new RepositoryData(it.name, it.url) }
        doLast {
            repositoryData.each {
                println "repository: ${it.name} ('${it.url}')"
            }
        }
    }
    ```

    ```shell
    // 应用 init 脚本时的输出：
    > gradle --init-script init.gradle -q showRepositories
    repository: STANDARD_ENTERPRISE_REPO ('https://repo.gradle.org/gradle/repo')
    ```

    > [!] 初始化脚本中的插件可确保运行构建时仅使用指定的存储库。
    <br>当在 init 脚本中应用插件时，Gradle 会实例化插件并调用插件实例的Plugin.apply(T)方法。
    <br>该gradle对象作为参数传递，可用于配置构建的各个方面。当然，所应用的插件可以解析为外部依赖项，如init 脚本的外部依赖项中所述



## Reference
* https://docs.gradle.org/current/userguide/userguide.html