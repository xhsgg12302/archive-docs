---
weight: 10
---

hello builder

> [!TIP|label:计算某个函数的虚拟地址，比如（printf）] 1，获取函数在实现类库中的偏移地址：`nm -D /lib/x86_64-linux-gnu/libc.so.6 | grep ' printf'`
<br>2，查看当前进程的 maps，从中获取类库**基址**：`cat /proc/81432/maps | grep 'libc'`
<br>3，两个值相加得到函数地址：`echo 'ibase=16;obase=10; 60100 + 7F4AD9C3C000 ' | bc` ==> 7F4AD9C9C100
<br><br>![](/.images/devops/build/exec-memory-func-addr.png ':size=100%')


## 截图

?> ![](/.images/devops/build/exec-objdump-h.png ':size=49%' ) ![](/.images/devops/build/exec-objdump-s-d.png ':size=50%' ) 

!> ![](/.images/devops/build/exec-objdump-ht.png ':size=49%' ) 

?> ![](/.images/devops/build/exec-readelf-h.png ':size=49%' ) ![](/.images/devops/build/exec-readelf-S-sections.png ':size=50%' )
<br>![](/.images/devops/build/exec-readelf-s-symbol.png ':size=49%' )　