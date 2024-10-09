* ## Intro(bc)

    + ### 保留位数

        > [?] `echo 'scale=3; 10 / 3' | bc `
        <br>`echo 'scale=5; 820 / (480 + 90)' | bc`

    + ### 进制转换

        > [!CAUTION] `1).`: 特别需要注意 ibase，obase，以及值之间的联系，不然会出现意想不到的结果。如下图：[参见](https://unix.stackexchange.com/questions/199615/understand-ibase-and-obase-in-case-of-conversions-with-bc)
        <br>简而言之，就是在进制转换的时候，不管`ibase`、`obase`哪一个在前，都要以前面使用的进制为主，对后面的值以前面的进制进行转换。举例如下：
        <br>在2到八进制中，可以写成`ibase=2;obase=1000;`，或者`obase=8;ibase=2;`，对于第一个，（2，1000），1000 为二进制的值 8，第二个（8，2），2 为八进制中的值 2
        <br><br>`2).`: 十六进制中的字母需要大写。小写就是: **(standard_in) 1: syntax error**。

        > [?] 二进制-八进制
        <br>`echo 'ibase=2;obase=1000;  11010111' | bc` ，output: ***327***
        <br>`echo 'obase=8;ibase=2;     11010111' | bc` ，output: ***327***
        <br><br> 二进制-十进制
        <br>`echo 'ibase=2;obase=1010;  11010111' | bc` ，output: ***215***
        <br>`echo 'obase=10;ibase=2;    11010111' | bc` ，output: ***215***
        <br><br> 二进制-十六进制
        <br>`echo 'ibase=2;obase=10000; 11010111' | bc` ，output: ***D7***
        <br>`echo 'obase=16;ibase=2;    11010111' | bc` ，output: ***D7***
        <br><br> 八进制-二进制
        <br>`echo 'ibase=8;obase=2;    327' | bc` ，output: ***11010111***
        <br><br> 八进制-十进制
        <br>`echo 'ibase=8;obase=12;   327' | bc` ，output: ***215***
        <br><br> 八进制-十六进制
        <br>`echo 'ibase=8;obase=20;   327' | bc` ，output: ***D7***
        <br><br> 十六进制-二进制
        <br>`echo 'ibase=16;obase=2;    D7' | bc` ，output: ***11010111***
        <br><br> 十六进制-八进制
        <br>`echo 'ibase=16;obase=8;    D7' | bc` ，output: ***327***
        <br><br> 十六进制-十进制
        <br>`echo 'ibase=16;obase=A;    D7' | bc` ，output: ***215***

        ![](/.images/devops/os/util/bc-base-conversion-01.png ':size=70%')

* ## Reference
    + https://pubs.opengroup.org/onlinepubs/9699919799/utilities/bc.html
    + https://unix.stackexchange.com/questions/199615/understand-ibase-and-obase-in-case-of-conversions-with-bc