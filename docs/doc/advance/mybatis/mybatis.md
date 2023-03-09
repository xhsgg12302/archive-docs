## 简介
> Mybatis最早Apache基金会的一个开源项目iBatis,2010年这个项目被迁移到google code,并改名为MyBatis.<br>
> Mybatis是支持SQL查询，存储过程和高级映射的持久层框架<br>
> Mybatis封装了JDBC几乎所有的代码和参数手工设置以及结果检索<br>
> Mybatis可以使用XML和注解进行映射关系配置。

## 使用
1. 加载jar包（mybatis，jdbc）
    <details><summary>代码示例</summary>

    ```xml
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.5</version>
    </dependency>
   
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.30</version>
    </dependency>
    ```
    </details>

2. 创建主配置文件SqlMapConfig.xml
    <details><summary>配置文件</summary>

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">

    <configuration>
        <properties resource="_framework/mybatis/config/jdbc.properties" />
        <settings>
            <!-- 打印查询语句 -->
            <setting name="logImpl" value="STDOUT_LOGGING" />
        </settings>
        <environments default="development">
            <environment id="development">
                <transactionManager type="JDBC"/>
                <dataSource type="POOLED">
                    <property name="driver" value="${driver}"/>
                    <property name="url" value="${url}"/>
                    <property name="username" value="${username}"/>
                    <property name="password" value="${password}"/>
                </dataSource>
            </environment>
        </environments>

        <!-- _12302_2022/5/3_< https://mybatis.net.cn/configuration.html#mappers > -->
        <mappers>
            <!--<mapper resource="sqlMapper/Student.xml"/>-->
            <!--<mapper class="_framework.mybatis.mapper.StudentMapper" />-->
            <!--<package name="_framework.mybatis.mapper"/>-->
            <mapper resource="_framework/mybatis/mapper/Student.xml"/>
        </mappers>
    </configuration>
    ```
    </details>

3. 创建映射文件Student.xml
    <details><summary>代码示例</summary>

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="_framework.mybatis.mapper.StudentMapper">

        <resultMap id="BaseResultMap" type="_framework.mybatis.bean.Student" >
            <result column="name" property="name" />
            <result column="email" property="email" />
            <result column="age" property="age" />
            <result column="is_young" property="young" />
        </resultMap>

        <sql id="Base_Column_List"> `name`,`email`,`age`,`is_young` </sql>

        <select id="selectByName" parameterType="java.lang.String" resultMap="BaseResultMap">
            select * from student where name = #{name}
        </select>

    </mapper>
    ```
    </details>

4. 使用代码获取数据
    <details><summary>代码示例</summary>

    ```java
    @Test
    public void test(){
        try(InputStream resource = Resources.getResourceAsStream("_framework/mybatis/config/sqlMappersConfig.xml")){
            SqlSessionFactory sqlSessionFactory =  new SqlSessionFactoryBuilder().build(resource);
            SqlSession sqlSession = sqlSessionFactory.openSession();
            //List<Student> students = sqlSession.selectList("selectByName","王", new RowBounds(2,4));
            StudentMapper userMapper = sqlSession.getMapper(StudentMapper.class);
            List<Student> students = userMapper.selectByName("王");

            for (Student student : students) {
                System.out.println(student);
            }
            sqlSession.close();
        }catch (Exception e) { e.printStackTrace();}
    }
    ```
    </details>

## 高级
* 自定义映射
    <details><summary>代码示例</summary>

    ```xml
    <resultMap id="BaseResultMap" type="_framework.mybatis.bean.Student" >
        <!-- 相同的可以不用定义 --> 
        <result column="is_young" property="young" />
    </resultMap>
    ```
    </details>

* 注解配置
    <details><summary>代码示例</summary>

    ```xml
    // 主配置文件SqlMapConfig.xml中的修改
    <mapper class="_framework.mybatis.mapper.StudentMapper" />
    
    // StudentMapper中需要再接口方法上加上注解映射
    @Select("select * from student where name like '%${name}%'")
    List<Student> findAllStudent(String name);
    ```
    </details>

* 缓存
    1. 一级缓存
        > 也叫本地缓存，sqlSession级别的缓存，一直开启，与数据库同一次会话期间查询到的数据会放在本地缓存中
        > 以后如果需要获取相同的数据，直接从缓存中拿，没必要再去数据库中查询

        `一级缓存失效的四种情况`
        - sqlSession不同
        - sqlSession相同，查询条件不同
        - sqlSession相同，两次查询期间执行了增删改操作
        - sqlSession相同，手动清除一级缓存`session.clearCache()`
        
    2. 二级缓存
        > 也叫全局缓存，基于namespace级别的缓存，一个namespace对一个一个二级缓存

        `工作机制`
        1. 一个会话，查询一条数据，这个数据就会被放在当前会话的一级缓冲中
        2. 如果会话关闭，一级缓存中的数据会被保存到二级缓存中，新的会话查询，就可以参照二级缓存中的内容
        
        ---

        `使用配置`
        1. 开启全局二级缓存配置: 主配置文件中加入 `<setting name="cacheEnabled" value="true"/>`
        2. 在mapper.xml中配置使用二级缓存：
            <details><summary>代码示例</summary>

            ```xml
            <mapper namespace="_framework.mybatis.mapper.StudentMapper">
                <cache eviction="" flushInterval="" readOnly="" size="" type=""></cache>
                <!--
                eviction : 缓存的回收策略：
                    LRU： 最近最少使用，移除最长时间不被使用的对象
                    FIFO：先进先出，按照对象进入缓存的顺序来移除
                    SOFT：软引用，移除基于垃圾回收状态和软引用规则的对象
                    WEAK：弱引用，更积极的移除基于垃圾回收器状态和弱引用规则的对象
                flushInterval: 缓存刷新间隔
                readOnly: 是否只读：
                    true: mybatis认为所有从缓存中获取数据的操作都是只读的，不会修改数据。
                          为了加快获取速度，会直接将缓存中的引用交给用户，不安全，速度快。
                    false: mybatis会觉得数据可能会修改
                          利用反序列化&序列化的技术将数据克隆一份给用户，安全，速度慢。
                size：缓存存放多少元素
                type：指定自定义缓存的全类名
                -->
            </mapper>
            ```
            </details>
        3. 由于涉及到序列化，所以POJO需要实现序列化接口

## 原理