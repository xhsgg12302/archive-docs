> [!] awk (Alfred Aho 、Peter Weinberger 和 Brian Kernighan):
    <br /> 样式扫描和处理语言，它允许您创建简短的程序。读取输入文件，为数据排序，处理数据，对输入执行计算以及生成报表等。其中语法方面有借鉴C语言。
 
## 一.语法格式：

```shell
awk 'pattern{action[;action..]}' {filenames}
    
原理：
    调用awk时，依次对文件中匹配的每一行做后面的action，每一对pattern+action用分号[;]隔开。
注意：
    如果一个'' 中没有pattern，默认所有行。action 必须{}包起来
    如果一个'' 中没有action，默认打印本行。
    如果什么都没有，只有'',不做任何处理。
    print,printf:
        print 输出后加一个ORS，而printf标准输出，想换行得加'\n',而这个换行不受awk的ORS控制。
```

## 二.常用选项：

```shell
[option]
    -F fs or --field-separator fs                               :指定输入文件拆分符，是一个字符串或正则，如-F:
    -v var=value or --asign var=value                           :赋值一个用户变量
    -f scripfile or --file scriptfile                           :从脚本文件中读取awk命令。
    -mf nnn and -mr nnn                                         :对nnn值设置内在限制，-mf选项限制分配给nnn的最大块数目；-mr选项限制记录的最大数目。这两个功能是Bell实验室版awk的扩展功能，在标准awk中不适用。
    -W compact or --compat, -W traditional or --traditional     :在兼容模式下运行awk。所以gawk的行为和标准的awk完全一样，所有的awk扩展都被忽略。
    -W copyleft or --copyleft, -W copyright or --copyright      :打印简短的版权信息。
    -W help or --help, -W usage or --usage                      :打印全部awk选项和每个选项的简短说明。
    -W lint or --lint                                           :打印不能向传统unix平台移植的结构的警告。
    -W lint-old or --lint-old                                   :打印关于不能向传统unix平台移植的结构的警告。
    -W posix                                                    :打开兼容模式。但有以下限制，不识别：/x、函数关键字、func、换码序列以及当fs是一个空格时，将新行作为一个域分隔符；操作符**和**=不能代替^和^=；fflush无效。
    -W re-interval or --re-inerval                              :允许间隔正则表达式的使用，参考(grep中的Posix字符类)，如括号表达式[[:alpha:]]。
    -W source program-text or --source program-text             :使用program-text作为源代码，可与-f命令混用。
    -W version or --version                                     :打印bug报告信息的版本。
```

+ ## 三. 使用案例：

    - ### 1）简单样例
        ```shell
        # echo 'hello this' | awk ''  > 默认不做处理
        # echo 'hello this' | awk '/hello/'  >打印hello this
        # echo 'hello this' | awk '{print $0}'    >打印hello this

        # cat test.txt | awk '{if(NR>=20 && NR<=30) print $0}'    > 打印20～30行内容

        >test.txt
        I am Poe,my qq is 1234567
        # cat test.txt | awk -F '[ ,]+' '{print $3" "$7'    >Poe 1234567
        ```

    - ### 2）BEGIN，END模块
        ```shell
        # cat /etc/passwd | awk '{count++;print $0;}END{print "count is",count}'    >统计/etc/passwd的账户人数
        # ll | awk '{size=size+$5}END{print "size is",size/1024,"K"}'   >统计某个文件夹下总字节数
        ```

    - ### 3）awk运算符
        ```shell
        # awk 'BEGIN{a=5;a+=5;print a}' >5,赋值运算符
        # awk 'BEGIN{a=1;b=2;print (a>2&&b>1,a=1||b>1)}'   >0 1 ,逻辑运算符
        # awk 'BEGIN{a="100testaa";if(a~/100/)print "OK"}'  >OK,正则运算符
        # awk 'BEGIN{a="100testaa"} a~/100/ {print "OK"}'  >等待用户输入
        # awk 'BEGIN{a="11";if(a>=9){print "ok"}}'  >无输出,关系运算符
        # awk 'BEGIN{a=11;if(a>=9){print "ok"}}'    >ok
        # awk 'BEGIN{a;if(a>=b){print "ok"}}'   >ok
        # awk 'BEGIN{a="b";print a++,++a}'  >0 2,算数运算符
        # awk 'BEGIN{a="20b4";print a++,++a}'   >20 22
        # awk 'BEGIN{a="b";print a=="b"?"ok":"err"}' >ok,三目运算符
        # awk 'BEGIN{a="b";print a=="c"?"ok":"err"}' >err
        ```

    - ### 4）常用awk内置变量
        ```shell
        $0      当前记录
        $1~$n   当前记录的第n个字段
        FS      输入字段分隔符，默认空格
        RS      输入记录分隔符，默认换行符
        NF      当前记录书中的字段个数
        NR      行号，从1开始
        OFS     输出字段分隔符，默认空格
        ORS     输出记录分隔符，默认换行符
        
        >tab.txt
        ww  CC      IDD
        
        # cat tab.txt | awk 'BEGIN{FS="\t+"}{print $1,$2,$3}'  >ww CC IDD
        # cat space.txt | awk -F [[:space:]+] '{print $1,$2,$3,$4,$5}'
        # cat hello.txt | awk -F [" ":]+ '{print $1,$2,$3}'
        # cat hello.txt | awk -F ":" 'NF==8{print $0}' hello.txt
        # ifconfig eth0 | awk -F [" ":]+ 'NR==2{print $4}'
        
        >record.txt
        Jimmy the Weasel
        100 Pleasant Drive
        San Francisco,CA 123456

        Big Tony
        200 Incognito Ave.
        Suburbia,WA 64890
        
        >awk.txt
        #!/bin/awk
        BEGIN {
            FS="\n"
            RS=""
        }
        {
            print $1","$2","$3
        }
        
        # awk -f awk.txt recode.txt 
        >Jimmy the Weasel,100 Pleasant Drive,San Francisco,CA 123456
        >Big Tony,200 Incognito Ave.,Suburbia,WA 64890
        ```

    - ### 5）awk正则
        ```shell
        # cat /etc/passwd | awk '/root/{print $0}'
        # cat /etc/passwd | awk -F: '$5~/root/{print $0}'
        # cat /etc/passwd | awk -F: '$1=="root"{print $0}'
        ```
        
    - ### 6）awk的C语言特性（if,循环和数组）
        ```shell
        -----------------------------
        {
            if(){
                if(){
                    ...
                }else{
                    ...
                }
            }elseif(){
                ...
            }else{
                ...
            }
        }
        -----------------------------
        {
           count=1 do{
               ...
               if(){
                   break;
               }
           } while()
        }
        -----------------------------
        for(x=1;x<=4;x++){
            ...
            if(){
                continue;
            }
        }
        -----------------------------
        awk中的数组是关联数组，数字索引也会转变为字符串索引，是无序的，有序的得通过下标获得
        -----------------------------
        {
            cities[1]="beijing"
            cities[2]="shanghai"
            cities[“three”]="guangzhou"
            for( c in cities){
                print cities[c]
            }
            print cities["three"];  //也可以
        }
        # netstat -an|awk '/^tcp/{++s[$NF]}END{for(a in s)print a,s[a]}'    
        >LISTEN 9
        >ESTABLISHED 4
        ```
        
        
    - ### 7）常用字符串函数
        ```shell
        #替换
        # awk 'BEGIN{info="this is a test2010test!";gsub(/[0-9]+/,"!",info);print info}'   >this is a test!test!
        
        #查找
        # awk 'BEGIN{info="this is a test2010test!";print index(info,"test")?"ok":"no found";}'   >ok
        
        #匹配查找
        # awk 'BEGIN{info="this is a test2010test!";print match(info,/[0-9]+/)?"ok":"no found";}' >ok
        
        #截取
        # awk 'BEGIN{info="this is a test2010test!";print substr(info,4,10);}'   >s is a tes
        
        #分割
        # awk 'BEGIN{info="this is a test";split(info,tA," ");print length(tA);for(k in tA){print k,tA[k];}}'
        >4
        >4 test
        >1 this
        >2 is
        >3 a
        
        #length(),blength(),tolower(),toupper()...等
        ```

## 四.参考：

*   [ Linux三剑客之awk命令 ](https://www.cnblogs.com/ginvip/p/6352157.html)
*   [ Linux awk 命令 ](https://www.runoob.com/linux/linux-comm-awk.html)

