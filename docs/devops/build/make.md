* ## Intro(make)

    + ### demo

        <!-- panels:start -->
        <!-- div:left-panel-60 -->
        ```makefile
        # makefile 对空格相当挑剔，必须是制表符
        OBJS = main.o tool1.o tool2.o
        CC = gcc
        CFLAGS += -c -Wall -g

        mytool: $(OBJS)
            $(CC) $^ -o $@

        %.o: %.c
            $(CC) $^ $(CFLAGS) -o $@

        .PHONY: clean
        clean:
            # 禁用回显使用@，或者在外部 make -s clean
            # https://stackoverflow.com/questions/9967105/suppress-echo-of-command-invocation-in-makefile
            @$(RM) *.o mytool -r
        ```
        <!-- div:right-panel-40 -->
        ![](/.images/devops/build/make-process-01.png ':size=81%')
        <!-- panels:end -->

* ## Reference

    + https://www.gnu.org/software/make/manual/make.html
    + https://stackoverflow.com/questions/2209827/why-is-no-one-using-make-for-java
    + https://www.ruanyifeng.com/blog/2015/02/make.html
    + https://www.bilibili.com/video/BV1hE411N7BK/