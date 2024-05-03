* ## Intro(XML Schema)

    ?> XML约束就是用于限制XML文件中可以出现的标签以及属性(顺序/格式/类型)文件。**DTD**约束（约束的粒度比较粗糙）简单/**Schame**约束（粒度比较细）复杂。

    + ### DTD约束

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ```xml
        <?xml version="1.0" encoding="UTF-8" ?>
        <!--引入本地DTD约束-->
        <!DOCTYPE persons SYSTEM "person.dtd">

        <persons>
            <person name="pOne">
                <name>张三</name>
                <age>18</age>
            </person>
        </persons>
        ```
        <!-- div:right-panel-50 -->
        ```xml
        <!-- person.dtd -->
        <!ELEMENT persons (person+) >
        <!ELEMENT person (name, age) >
        <!ELEMENT name (#PCDATA) >
        <!ELEMENT age (#PCDATA) >

        <!--  -->
        <!ATTLIST person name CDATA #REQUIRED >
        ```
        <!-- panels:end -->

        - #### 编写规则

            1. 通过`<!ELEMENT 元素名 >` 声明可以使用哪些标签.
            2. 如果是一个复杂元素，那么需要在元素名后面加一个空格，然后编写()，()里面写该元素中可以出现的子元素。
                <br>2.1 复杂元素中后面的()中的内容，可以通过逗号`,`分割（必须按顺序出现），也可以通过或符号`|`分割（必须出现一个）。
                <br>2.2 复杂元素中后面的()中的内容，默认只能出现一次，如果想要出现多次，可以在子元素的名称后面添加符号，(`?`:只能出现一次),(`+`:最少一次),(`*`:可有可无)
            3. 如果是一个简单元素，那么需要在元素名后面加一个空格，然后编写(#PCDATA)。
            4. 如果是一个属性，通过`<!ATTLIST 元素名 属性名 属性类型 属性值 >` 进行声明。
                <br>4.1 属性的类型：(`CDATA`类型：普通的字符串)
                <br>4.2 属性的约束：(`#REQUIRED`： 必须的, `#IMPLIED`： 属性不是必须的, `#FIXED value`：属性值是固定的)

        - #### 引入方式

            1. 引入本地DTD。 `<!DOCTYPE 根元素名称 SYSTEM 'DTD文件路径' >`
            2. 引入网络DTD。 `<!DOCTYPE 根元素名称 PUBLIC "DTD文件名称" "DTD文档的URL">`
            3. 在xml文件内部嵌入。 `<!DOCTYPE 根元素名称 [DTD文件内容] >`

         
            <!-- tabs:start -->
            ##### **引入本地DTD**
            ```xml
            <?xml version="1.0" encoding="UTF-8" ?>
            <!-- 引入本地DTD约束 -->
            <!DOCTYPE persons SYSTEM "person.dtd">

            <persons>
                <person name="pOne">
                    <name>张三</name>
                    <age>18</age>
                </person>
            </persons> 
            ```
            ##### **引入网络DTD**
            ```xml
            <?xml version="1.0" encoding="UTF-8" ?>
            <!-- 引入网络DTD -->
            <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

            <mapper namespace="site.wtfu.framework.dao.UserDao">
            </mapper>
            ```
            ##### **在xml文件内部嵌入**
            ```xml
            <?xml version="1.0" encoding="UTF-8" ?>
            <!-- 在xml文件内部嵌入 -->
            <!DOCTYPE persons [
                <!ELEMENT persons (person*)>
                <!ELEMENT person (name,age)>
                <!ELEMENT name (#PCDATA)>
                <!ELEMENT age (#PCDATA)>
                <!ATTLIST person name CDATA #REQUIRED>
            ]>

            <persons>
                <person name="pOne">
                    <name>张三</name>
                    <age>18</age>
                </person>

                <person name="pTwo">
                    <name>张三</name>
                    <age>18</age>
                </person>
            </persons>
            ```
            <!-- tabs:end -->

        - #### 参考引用

            1. http://mybatis.org/dtd/mybatis-3-mapper.dtd

                <details><summary>mybatis-3-mapper.dtd</summary>

                ```xml
                <?xml version="1.0" encoding="UTF-8" ?>
                <!--

                    Copyright 2009-2018 the original author or authors.

                    Licensed under the Apache License, Version 2.0 (the "License");
                    you may not use this file except in compliance with the License.
                    You may obtain a copy of the License at

                        http://www.apache.org/licenses/LICENSE-2.0

                    Unless required by applicable law or agreed to in writing, software
                    distributed under the License is distributed on an "AS IS" BASIS,
                    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
                    See the License for the specific language governing permissions and
                    limitations under the License.

                -->
                <!ELEMENT mapper (cache-ref | cache | resultMap* | parameterMap* | sql* | insert* | update* | delete* | select* )+>
                <!ATTLIST mapper
                namespace CDATA #IMPLIED
                >

                <!ELEMENT cache-ref EMPTY>
                <!ATTLIST cache-ref
                namespace CDATA #REQUIRED
                >

                <!ELEMENT cache (property*)>
                <!ATTLIST cache
                type CDATA #IMPLIED
                eviction CDATA #IMPLIED
                flushInterval CDATA #IMPLIED
                size CDATA #IMPLIED
                readOnly CDATA #IMPLIED
                blocking CDATA #IMPLIED
                >

                <!ELEMENT parameterMap (parameter+)?>
                <!ATTLIST parameterMap
                id CDATA #REQUIRED
                type CDATA #REQUIRED
                >

                <!ELEMENT parameter EMPTY>
                <!ATTLIST parameter
                property CDATA #REQUIRED
                javaType CDATA #IMPLIED
                jdbcType CDATA #IMPLIED
                mode (IN | OUT | INOUT) #IMPLIED
                resultMap CDATA #IMPLIED
                scale CDATA #IMPLIED
                typeHandler CDATA #IMPLIED
                >

                <!ELEMENT resultMap (constructor?,id*,result*,association*,collection*, discriminator?)>
                <!ATTLIST resultMap
                id CDATA #REQUIRED
                type CDATA #REQUIRED
                extends CDATA #IMPLIED
                autoMapping (true|false) #IMPLIED
                >

                <!ELEMENT constructor (idArg*,arg*)>

                <!ELEMENT id EMPTY>
                <!ATTLIST id
                property CDATA #IMPLIED
                javaType CDATA #IMPLIED
                column CDATA #IMPLIED
                jdbcType CDATA #IMPLIED
                typeHandler CDATA #IMPLIED
                >

                <!ELEMENT result EMPTY>
                <!ATTLIST result
                property CDATA #IMPLIED
                javaType CDATA #IMPLIED
                column CDATA #IMPLIED
                jdbcType CDATA #IMPLIED
                typeHandler CDATA #IMPLIED
                >

                <!ELEMENT idArg EMPTY>
                <!ATTLIST idArg
                javaType CDATA #IMPLIED
                column CDATA #IMPLIED
                jdbcType CDATA #IMPLIED
                typeHandler CDATA #IMPLIED
                select CDATA #IMPLIED
                resultMap CDATA #IMPLIED
                name CDATA #IMPLIED
                columnPrefix CDATA #IMPLIED
                >

                <!ELEMENT arg EMPTY>
                <!ATTLIST arg
                javaType CDATA #IMPLIED
                column CDATA #IMPLIED
                jdbcType CDATA #IMPLIED
                typeHandler CDATA #IMPLIED
                select CDATA #IMPLIED
                resultMap CDATA #IMPLIED
                name CDATA #IMPLIED
                columnPrefix CDATA #IMPLIED
                >

                <!ELEMENT collection (constructor?,id*,result*,association*,collection*, discriminator?)>
                <!ATTLIST collection
                property CDATA #REQUIRED
                column CDATA #IMPLIED
                javaType CDATA #IMPLIED
                ofType CDATA #IMPLIED
                jdbcType CDATA #IMPLIED
                select CDATA #IMPLIED
                resultMap CDATA #IMPLIED
                typeHandler CDATA #IMPLIED
                notNullColumn CDATA #IMPLIED
                columnPrefix CDATA #IMPLIED
                resultSet CDATA #IMPLIED
                foreignColumn CDATA #IMPLIED
                autoMapping (true|false) #IMPLIED
                fetchType (lazy|eager) #IMPLIED
                >

                <!ELEMENT association (constructor?,id*,result*,association*,collection*, discriminator?)>
                <!ATTLIST association
                property CDATA #REQUIRED
                column CDATA #IMPLIED
                javaType CDATA #IMPLIED
                jdbcType CDATA #IMPLIED
                select CDATA #IMPLIED
                resultMap CDATA #IMPLIED
                typeHandler CDATA #IMPLIED
                notNullColumn CDATA #IMPLIED
                columnPrefix CDATA #IMPLIED
                resultSet CDATA #IMPLIED
                foreignColumn CDATA #IMPLIED
                autoMapping (true|false) #IMPLIED
                fetchType (lazy|eager) #IMPLIED
                >

                <!ELEMENT discriminator (case+)>
                <!ATTLIST discriminator
                column CDATA #IMPLIED
                javaType CDATA #REQUIRED
                jdbcType CDATA #IMPLIED
                typeHandler CDATA #IMPLIED
                >

                <!ELEMENT case (constructor?,id*,result*,association*,collection*, discriminator?)>
                <!ATTLIST case
                value CDATA #REQUIRED
                resultMap CDATA #IMPLIED
                resultType CDATA #IMPLIED
                >

                <!ELEMENT property EMPTY>
                <!ATTLIST property
                name CDATA #REQUIRED
                value CDATA #REQUIRED
                >

                <!ELEMENT typeAlias EMPTY>
                <!ATTLIST typeAlias
                alias CDATA #REQUIRED
                type CDATA #REQUIRED
                >

                <!ELEMENT select (#PCDATA | include | trim | where | set | foreach | choose | if | bind)*>
                <!ATTLIST select
                id CDATA #REQUIRED
                parameterMap CDATA #IMPLIED
                parameterType CDATA #IMPLIED
                resultMap CDATA #IMPLIED
                resultType CDATA #IMPLIED
                resultSetType (FORWARD_ONLY | SCROLL_INSENSITIVE | SCROLL_SENSITIVE | DEFAULT) #IMPLIED
                statementType (STATEMENT|PREPARED|CALLABLE) #IMPLIED
                fetchSize CDATA #IMPLIED
                timeout CDATA #IMPLIED
                flushCache (true|false) #IMPLIED
                useCache (true|false) #IMPLIED
                databaseId CDATA #IMPLIED
                lang CDATA #IMPLIED
                resultOrdered (true|false) #IMPLIED
                resultSets CDATA #IMPLIED 
                >

                <!ELEMENT insert (#PCDATA | selectKey | include | trim | where | set | foreach | choose | if | bind)*>
                <!ATTLIST insert
                id CDATA #REQUIRED
                parameterMap CDATA #IMPLIED
                parameterType CDATA #IMPLIED
                timeout CDATA #IMPLIED
                flushCache (true|false) #IMPLIED
                statementType (STATEMENT|PREPARED|CALLABLE) #IMPLIED
                keyProperty CDATA #IMPLIED
                useGeneratedKeys (true|false) #IMPLIED
                keyColumn CDATA #IMPLIED
                databaseId CDATA #IMPLIED
                lang CDATA #IMPLIED
                >

                <!ELEMENT selectKey (#PCDATA | include | trim | where | set | foreach | choose | if | bind)*>
                <!ATTLIST selectKey
                resultType CDATA #IMPLIED
                statementType (STATEMENT|PREPARED|CALLABLE) #IMPLIED
                keyProperty CDATA #IMPLIED
                keyColumn CDATA #IMPLIED
                order (BEFORE|AFTER) #IMPLIED
                databaseId CDATA #IMPLIED
                >

                <!ELEMENT update (#PCDATA | selectKey | include | trim | where | set | foreach | choose | if | bind)*>
                <!ATTLIST update
                id CDATA #REQUIRED
                parameterMap CDATA #IMPLIED
                parameterType CDATA #IMPLIED
                timeout CDATA #IMPLIED
                flushCache (true|false) #IMPLIED
                statementType (STATEMENT|PREPARED|CALLABLE) #IMPLIED
                keyProperty CDATA #IMPLIED
                useGeneratedKeys (true|false) #IMPLIED
                keyColumn CDATA #IMPLIED
                databaseId CDATA #IMPLIED
                lang CDATA #IMPLIED
                >

                <!ELEMENT delete (#PCDATA | include | trim | where | set | foreach | choose | if | bind)*>
                <!ATTLIST delete
                id CDATA #REQUIRED
                parameterMap CDATA #IMPLIED
                parameterType CDATA #IMPLIED
                timeout CDATA #IMPLIED
                flushCache (true|false) #IMPLIED
                statementType (STATEMENT|PREPARED|CALLABLE) #IMPLIED
                databaseId CDATA #IMPLIED
                lang CDATA #IMPLIED
                >

                <!-- Dynamic -->

                <!ELEMENT include (property+)?>
                <!ATTLIST include
                refid CDATA #REQUIRED
                >

                <!ELEMENT bind EMPTY>
                <!ATTLIST bind
                name CDATA #REQUIRED
                value CDATA #REQUIRED
                >

                <!ELEMENT sql (#PCDATA | include | trim | where | set | foreach | choose | if | bind)*>
                <!ATTLIST sql
                id CDATA #REQUIRED
                lang CDATA #IMPLIED
                databaseId CDATA #IMPLIED
                >

                <!ELEMENT trim (#PCDATA | include | trim | where | set | foreach | choose | if | bind)*>
                <!ATTLIST trim
                prefix CDATA #IMPLIED
                prefixOverrides CDATA #IMPLIED
                suffix CDATA #IMPLIED
                suffixOverrides CDATA #IMPLIED
                >
                <!ELEMENT where (#PCDATA | include | trim | where | set | foreach | choose | if | bind)*>
                <!ELEMENT set (#PCDATA | include | trim | where | set | foreach | choose | if | bind)*>

                <!ELEMENT foreach (#PCDATA | include | trim | where | set | foreach | choose | if | bind)*>
                <!ATTLIST foreach
                collection CDATA #REQUIRED
                item CDATA #IMPLIED
                index CDATA #IMPLIED
                open CDATA #IMPLIED
                close CDATA #IMPLIED
                separator CDATA #IMPLIED
                >

                <!ELEMENT choose (when* , otherwise?)>
                <!ELEMENT when (#PCDATA | include | trim | where | set | foreach | choose | if | bind)*>
                <!ATTLIST when
                test CDATA #REQUIRED
                >
                <!ELEMENT otherwise (#PCDATA | include | trim | where | set | foreach | choose | if | bind)*>

                <!ELEMENT if (#PCDATA | include | trim | where | set | foreach | choose | if | bind)*>
                <!ATTLIST if
                test CDATA #REQUIRED
                >
                ```

                </details>

            2. http://mybatis.org/dtd/mybatis-3-config.dtd

                [dcos| 创建主配置文件SqlMapConfig.xml](../mybatis/mybatis.md#创建主配置文件sqlmapconfigxml )
            
    + ### Schema约束

        - #### 编写规则
        - #### 引入方式
        - #### 参考引用

## Reference
* https://www.bilibili.com/video/BV19P411S7mf/?vd_source=550a4dc4b2a914c0681a14307bbe8cbe