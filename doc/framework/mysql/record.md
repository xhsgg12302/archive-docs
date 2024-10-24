## HEAD
* ### 表注释查询

    ```sql
    -- oracle
    select * from user_tab_comments where comments like '%危险%';

    -- mysql
    SELECT COLUMN_NAME,column_comment FROM INFORMATION_SCHEMA.Columns
    WHERE table_name='表名' AND table_schema='数据库名'
    ```

* ### 父子路径

    ```sql
    -- 宁夏，银川，金凤，良田镇
    SELECT T2.*
    FROM (
            SELECT
                @r AS _code,
                (SELECT @r := sgd_father_url FROM stats_gov_data WHERE sgd_source_url = _code) AS v2,
                @l := @l + 1 AS lvl
            FROM
                stats_gov_data h , (SELECT @r := '2020/64/01/06/640106100.html',@l :=0) vars
            WHERE @r <> '2020/index.html'
                
    ) T1
    LEFT JOIN stats_gov_data T2 ON T1._code = T2.sgd_source_url
    order by T1.lvl desc;

    -- 甘肃省-天水市-武山县
    SELECT T2.*
    FROM (
        SELECT   
            @r AS _code,   
            (SELECT @r := raa_father_code FROM `rsp_administrative_area` WHERE raa_code = _code) AS v2,   
            @l := @l + 1 AS lvl   
        FROM   
            rsp_administrative_area h , (SELECT @r := '620524',@l :=0) vars   
        WHERE @r is not null) T1   
    left JOIN rsp_administrative_area T2   
    ON T1._code = T2.raa_code
    order by T1.lvl desc;

    -- 所有子节点，包括父节点
    SELECT * FROM (

        SELECT t1.*,
            IF(FIND_IN_SET(rgp_father_id, @pids) > 0 OR rgp_id = 1, @pids := CONCAT(@pids, ',', rgp_id), '0') AS ischild
        FROM (
            SELECT * FROM rsp_grid_position AS t 
        -- WHERE t.rgp_id = '01' 
            ORDER BY t.rgp_id ASC
        ) t1,
        (SELECT @pids := 1 ) t2 
    ) t3 
    WHERE ischild != '0'
    ```

* ### 时间处理
    ```sql
    SELECT
        MONTH( "2019-03-11 13:24:51" ),
        YEAR ( "2019-03-11 13:24:51" ),
        DAY ( "2019-03-11 13:24:51" ),
        HOUR ( "2019-03-11 13:24:51" ),
        MINUTE ( "2019-03-11 13:24:51" ),
        SECOND ( "2019-03-11 13:24:51" );

    SELECT now(), EXTRACT( DAY_SECOND FROM now( ) );

    select unix_timestamp();
    select from_unixtime(1683993600000 / 1000);
    select curdate(), curtime(), date(now());
    select EXTRACT(YEAR_MONTH FROM now()); -- MICROSECOND、SECOND、MINUTE、HOUR、DAY、WEEK、MONTH、QUARTER、YEAR、SECOND_MICROSECOND、MINUTE_MICROSECOND、MINUTE_SECOND、HOUR_MICROSECOND、HOUR_SECOND、HOUR_MINUTE、DAY_MICROSECOND、DAY_SECOND、DAY_MINUTE、DAY_HOUR、YEAR_MONTH
    -- DATE_ADD(date,INTERVAL expr type)

    select TIME_TO_SEC('00:06:53');
    select str_to_date('2018-07-03 18:06:53', '%Y-%m-%d %H:%i:%s');
    select date_format(now(), '%Y-%m-%d %H:%i:%s' );
    select timediff('2018-07-03 19:13:21', '2018-07-03 18:06:53');
    ```