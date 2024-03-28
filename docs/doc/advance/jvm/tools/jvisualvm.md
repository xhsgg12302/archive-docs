* ## Intro(jvisualvm)

    + OQL控制台

        ?> 使用样例
        <br>`select s from java.lang.String s where s.toString().contains("hello")`
        <br>`select s from java.lang.String s where s.toString().equals("12")`
        <br>`select s from java.lang.String s where /^\d+$/.test(s.toString())`
        <br>`select i from java.lang.Integer i where i.value > 12302`

* ## Reference
    + https://htmlpreview.github.io/?https://raw.githubusercontent.com/visualvm/visualvm.java.net.backup/master/www/oqlhelp.html
    + https://cr.openjdk.org/~sundar/8022483/webrev.01/raw_files/new/src/share/classes/com/sun/tools/hat/resources/oqlhelp.html
    + https://www.cnblogs.com/ghj1976/p/5408295.html