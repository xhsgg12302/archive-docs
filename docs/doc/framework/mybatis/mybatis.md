* ## Intro(Mybatis)

    ?> Mybatis最早Apache基金会的一个开源项目iBatis,2010年这个项目被迁移到google code,并改名为MyBatis.
    <br>Mybatis是支持SQL查询，存储过程和高级映射的持久层框架
    <br>Mybatis封装了JDBC几乎所有的代码和参数手工设置以及结果检索
    <br>Mybatis可以使用XML和注解进行映射关系配置。

    + ### 使用

        1. #### 加载jar包（mybatis，jdbc）

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

        2. #### 创建主配置文件SqlMapConfig.xml

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

        3. #### 创建映射文件Student.xml

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

        4. #### 使用代码获取数据

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

    + ### 高级

        * #### 自定义映射

            ```xml
            <resultMap id="BaseResultMap" type="_framework.mybatis.bean.Student" >
                <!-- 相同的可以不用定义 --> 
                <result column="is_young" property="young" />
            </resultMap>
            ```

        * #### 注解配置

            ```xml
            // 主配置文件SqlMapConfig.xml中的修改
            <mapper class="_framework.mybatis.mapper.StudentMapper" />
            
            // StudentMapper中需要再接口方法上加上注解映射
            @Select("select * from student where name like '%${name}%'")
            List<Student> findAllStudent(String name);
            ```

        * #### 缓存

            1. ##### 一级缓存

                ?> 也叫本地缓存，sqlSession级别的缓存，一直开启，与数据库同一次会话期间查询到的数据会放在本地缓存中
                <br>以后如果需要获取相同的数据，直接从缓存中拿，没必要再去数据库中查询

                `一级缓存失效的四种情况`
                - sqlSession不同
                - sqlSession相同，查询条件不同
                - sqlSession相同，两次查询期间执行了增删改操作
                - sqlSession相同，手动清除一级缓存`session.clearCache()`
                
            2. ##### 二级缓存

                ?> 也叫全局缓存，基于namespace级别的缓存，一个namespace对一个一个二级缓存

                `工作机制`
                1. 一个会话，查询一条数据，这个数据就会被放在当前会话的一级缓冲中
                2. 如果会话关闭，一级缓存中的数据会被保存到二级缓存中，新的会话查询，就可以参照二级缓存中的内容
                
                ---

                `使用配置`
                1. 开启全局二级缓存配置: 主配置文件中加入 `<setting name="cacheEnabled" value="true"/>`
                2. 在mapper.xml中配置使用二级缓存：

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
                3. 由于涉及到序列化，所以POJO需要实现序列化接口

* ## 扩展

    1. xml样例

        <details><summary>mapper.xml样例01</summary>

        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
        <mapper namespace="cn.**.**.**.InventoryDao">
            <resultMap id="BaseResultMap" type="cn.**.*.*.InventoryBean">
                <id column="id" property="id" jdbcType="BIGINT"/>
                <result column="totalVolume" property="totalVolume" jdbcType="DECIMAL"/>
                <result column="receiptNum" property="receiptNum" jdbcType="CHAR"/>
                <result column="sendDetailId" property="sendDetailId" jdbcType="CHAR"/>
                <result column="createUserName" property="createUserName" jdbcType="VARCHAR"/>
                <result column="manuallyEntered" property="manuallyEntered" jdbcType="INTEGER"/>
                <result column="receiptDate" property="receiptDate" jdbcType="TIMESTAMP"/>
                <result column="receiptDetailId" property="receiptDetailId" jdbcType="CHAR"/>
                <result column="allocatedCount" property="allocatedCount" jdbcType="INTEGER"/>
                <result column="updateTime" property="updateTime" jdbcType="DATE"/>

            </resultMap>

            <sql id="Base_Column_List">
            totalVolume,receiptNum,inventoryId,putListNum,totalWeight,onHandCount
            </sql>

            <select id="selectByInventoryId" parameterType="cn.*.*.*.InventoryBean" resultMap="BaseResultMap">
                <![CDATA[ SELECT ]]>
                <choose>
                    <when test="field!=null and field!=''">
                        <![CDATA[ ${field} ]]>
                    </when>
                    <otherwise>
                        <include refid="Base_Column_List"/>
                    </otherwise>
                </choose>
                <![CDATA[ FROM `qmhd_co_wfms_wms`.t_inventory ]]>
                <where>
                    <if test="inventoryId != null and inventoryId != ''">
                        <![CDATA[ AND inventoryId = #{inventoryId,jdbcType=CHAR} ]]>
                    </if>
                </where>
                <if test="orderByParam != null and orderByParam != ''">
                    <![CDATA[ ORDER BY ${orderByParam} ]]>
                    <if test="orderByAccording != null and orderByAccording != ''">
                        <![CDATA[ ${orderByAccording} ]]>
                    </if>
                </if>
                <![CDATA[ LIMIT 0,1 ]]>
            </select>

            <update id="updateListsByTwoId" parameterType="java.util.List">
                <foreach collection="list" item="item" index="index" separator=";">
                    <![CDATA[ UPDATE `qmhd_co_wfms_wms`.t_inventory ]]>
                    <set>
                        <if test="item.totalVolume != null ">
                            <![CDATA[ totalVolume = #{item.totalVolume,jdbcType=DECIMAL}, ]]>
                        </if>
                        <if test="item.receiptNum != null ">
                            <![CDATA[ receiptNum = #{item.receiptNum,jdbcType=CHAR}, ]]>
                        </if>
                    </set>
                    <where>
                        <if test="item.inventoryId != null and item.inventoryId != ''">
                            <![CDATA[ AND inventoryId = #{item.inventoryId,jdbcType=CHAR} ]]>
                        </if>
                    </where>
                </foreach>
            </update>

            <update id="updateByTwoId" parameterType="cn.*.*.*.InventoryBean">
                <![CDATA[ UPDATE `qmhd_co_wfms_wms`.t_inventory ]]>
                <set>
                    <if test="totalVolume != null ">
                        <![CDATA[ totalVolume = #{totalVolume,jdbcType=DECIMAL}, ]]>
                    </if>
                    <if test="receiptNum != null ">
                        <![CDATA[ receiptNum = #{receiptNum,jdbcType=CHAR}, ]]>
                    </if>
                </set>
                <where>
                    <if test="inventoryId != null and inventoryId != ''">
                        <![CDATA[ AND inventoryId = #{inventoryId,jdbcType=CHAR}  ]]>
                    </if>
                </where>
            </update>

            <update id="updateBySingleOnAndIn" parameterType="cn.*.*.*.InventoryBean">
                <![CDATA[ UPDATE `qmhd_co_wfms_wms`.t_inventory ]]>
                <set>
                    <if test="totalVolume != null ">
                        <![CDATA[ totalVolume = #{totalVolume,jdbcType=DECIMAL}, ]]>
                    </if>
                </set>
                <where>
                    <if test="receiptId != null and receiptId != ''">
                        <![CDATA[ AND receiptId = #{receiptId,jdbcType=CHAR} ]]>
                    </if>
                    <if test="goodsNum != null and goodsNum != ''">
                        <![CDATA[ AND goodsNum = #{goodsNum,jdbcType=CHAR} ]]>
                    </if>
                </where>
                <if test="orderByParam != null and orderByParam != ''">
                    <![CDATA[ ORDER BY ${orderByParam} ]]>
                    <if test="orderByAccording != null and orderByAccording != ''">
                        <![CDATA[ ${orderByAccording} ]]>
                    </if>
                </if>
                <if test="pageSize>0">
                    <bind name="pageNo" value="(_parameter.pageNo-1)*_parameter.pageSize"/>
                    <![CDATA[ LIMIT #{pageSize,jdbcType=INTEGER} ]]>
                </if>
            </update>

            <!-- 根据条件获取单个库存记录 -->
            <select id="selectSingleByInventoryBean" parameterType="cn.*.*.*.InventoryBean" resultMap="BaseResultMap">
                <![CDATA[ SELECT ]]>
                <choose>
                    <when test="field!=null and field!=''">
                        <![CDATA[ ${field} ]]>
                    </when>
                    <otherwise>
                        <include refid="Base_Column_List"/>
                    </otherwise>
                </choose>
                <![CDATA[ FROM `qmhd_co_wfms_wms`.t_inventory ]]>
                <where>
                    <if test="totalVolume != null and totalVolume != ''">
                        <bind name="totalVolume" value="'%' + _parameter.totalVolume + '%'"/>
                        <![CDATA[ AND totalVolume LIKE #{totalVolume,jdbcType=DECIMAL} ]]>
                    </if>
                </where>
                <if test="orderByParam != null and orderByParam != ''">
                    <![CDATA[ ORDER BY ${orderByParam} ]]>
                    <if test="orderByAccording != null and orderByAccording != ''">
                        <![CDATA[ ${orderByAccording} ]]>
                    </if>
                </if>
                <![CDATA[ LIMIT 0,1 ]]>
            </select>

            <!-- _12302_2021/7/31_< 引用 sql > -->
            <select id="queryKeyAndValue" parameterType="long" resultMap="BaseResultMap">
                select
                <include refid="Base_Column_List"/>
                from wom_order_expand woe
                where woe.woe_wo_id = #{woId}
            </select>

            <!-- _12302_2021/7/31_< 批量更新 > -->
            <update id="updateBatchByWoeKeyAndWoId" parameterType="list">
                update wom_order_expand
                <trim prefix="set" suffixOverrides=",">
                    <trim prefix="woe_value =case" suffix="end,">
                        <foreach collection="list" item="item" index="index">
                            <if test="item.woeValue!=null and item.woeValue != '' ">
                                when woe_key = #{item.woeKey} and woe_wo_id = #{item.woeWoId}
                                then #{item.woeValue}
                            </if>
                        </foreach>
                    </trim>
                </trim>
                where woe_id in
                <foreach collection="list" item="item" index="index" separator="," open="(" close=")">
                    #{item.woeId}
                </foreach>
            </update>

            <!-- _12302_2021/7/31_< 批量删除 > -->
            <delete id="deleteBatchByList" parameterType="list">
                delete from wom_order_expand where
                <foreach collection="list" item="item" index="index" separator=" or " open="(" close=")">
                    woe_key = #{item.woeKey} and woe_wo_id = #{item.woeWoId}
                </foreach>
            </delete>

            <!-- _12302_2021/7/31_< 1，2，3，4 ==> 正在弄，弄完了，还弄吗 > -->
            <select id="getAllExtendData" parameterType="list" resultMap="BaseResultMap">
                select
                woe.woe_wo_id,woe.woe_key,woe.woe_value,GROUP_CONCAT(wd.wd_name) as 'woe_value_string'
                FROM wom_order_expand woe
                LEFT JOIN wom_dictionary wd ON FIND_IN_SET(wd.wd_id,woe.woe_value)
                <where>
                    <trim prefix="woe.woe_wo_id in ">
                        <foreach collection="list" item="item" index="index" separator=" , " open="(" close=")">
                            #{item}
                        </foreach>
                    </trim>
                </where>
                GROUP BY woe.woe_id;
            </select>

        </mapper>
        ```
        
        </details>

* ## Reference

    + https://www.bilibili.com/video/BV1d4411g7tv/?p=269