<font style="font-size:1.3em;font-weight:bold;"> 目录 </font> 

[toc]

# 为啥这么受欢迎


> 只是你不知道而已

`   a b cd ef gb lakgg `


---
title: "post blog title"
date: 2020-05-23
---



![favicon.ico](https://wtfo.gitee.io/favicon.ico "h")
!(blogs)[https://wtfo.site/favicon.ico a]

----

----

----

_斜体_

__加粗__

~~_hahahahahahahahaha thisis abc_~~

___ad___

### 字符效果和横线等
                
----

~~删除线~~ <s>删除线（开启识别HTML标签时）</s>
*斜体字*      _斜体字_
**粗体**  __粗体__
***粗斜体*** ___粗斜体___

上标：X<sub>2</sub>，下标：O<sup>2</sup>





# 1. 提出问题

...

# 2. 解决方法

## 2.1 利用HTML标签

...

## 2.2 利用Markdown标签

...

# 3. 总结

```java
public static void main(String[] args){
    String temp = "hello"
    System.out.println(temp);
}
```

![asdf](https://wtfo.gitee.io/favicon.ico "he")

+ this
    + one
    + two 
    + three
    *
-  that
    + abc
    + abc
* where
    1. 
    2. 
    3. 
    4. 
    5. 
    6. 
    7. 

#### GFM task list

- [x] GFM task list 1
- [x] GFM task list 2
- [ ] GFM task list 3
    - [ ] GFM task list 3-1
    - [ ] GFM task list 3-2
    - [ ] GFM task list 3-3
- [ ] GFM task list 4
    - [ ] GFM task list 4-1
    - [ ] GFM task list 4-2


<!-- tabs:start -->
#### **Ubuntu:20.4**
<!-- tabs:start -->
<!-- tabs:end -->
<!-- tabs:end -->


![](/.images/doc/advance/jni/jni-show-01.png)
![](/.images/doc/advance/jni/jni-show-01.png 'comment :size=70%')


如果输入不了中文，参考 [mysql安装](/docs/doc/advance/mysql/install?id=源码安装)


emoji表示
:100:
<!-- 
    https://docsify.js.org/#/zh-cn/plugins?id=emoji
    :100:
    [参考]([/docs/devops/build/cmake?id=cmake-100)
    不需要解析emoji 
    https://github.com/docsifyjs/docsify/issues/742
-->

single backslash (status: opening)
<!-- 
反斜杠 转义 ``
# https://github.com/docsifyjs/docsify/issues/1881
-->

## specification 01
* paragraph

    + inner paragraph

    start of file text

        1. li one
        2. li two

            text of li two
    <!-- 如果`end of the each li`需要跳出 bullet,则需要添加一个空白行。需要注意空白行, 得预留一个跟外部顶格一样多的空格。比如`start of file text` 前面有四个空格,所以需要添加有四个空格的空白行。-->
    
    end of the each li

## markdown引用变量
[key]:value
直接把key放入[],这样显示文本是key，链接为value
[baidu] [我的主页]  ← 这里有两，只会显示第一个，估计识别成两个中括号的模式了（因为鼠标放上去显示链接是第二个变量值，两个中括号中间空格无效）
[baidu]
[我的主页]
[baidu]:http://www.baidu.com
[我的主页]:http://www.jianchengss.com
[image-qr-code]: https://gitee.com/jianchengss/resources/raw/master/images/weixin/mp_QR-code/Jason_pinyaxuan_8.jpg
[image-error]: https://www.baidu.com/Jason_pinyaxuan_8.jpg


## 在当前页面引入其他页面的链接
```shell
# 比如在 usage.md中引入maven.md
[recongize](../maven.md)
[abc](./gradle.md)
[mysql install docker](/docs/doc/framework/mysql/install.md?id=压缩包安装)
```

## 数学符号
$ E=mc^2 $
$$ E=mc^2 $$
[示例](/docs/algo/tree/03-01-AVL-tree.md)

## mermaid version
* https://github.com/orgs/community/discussions/37498#discussioncomment-7152968
```mermaid
info
```

## 数学符号和example panels组合
?> 将下面代码放到render.md文件中就可以看到效果
* ## Sample

    + ### Demo

        <!-- panels:start -->
        <!-- div:title-panel -->
        #### Hello World
        <!-- div:left-panel -->
        ?> If you are on widescreen, checkout the *right* panel, *right* there →

        <!-- div:right-panel -->
        ?> This is an example panel.
        <br>You can see it's usage in practice in the docs listed below:

        - [Fairlay API](https://fairlay.com/api)
        - [FLAP services](https://docs.flap.cloud/#/create_new_service?id=special-files)

        <small>please contact me if you use docsify-example-panels. i would like to display it here too.</small>
        <!-- panels:end -->


## Reference
* https://marked.js.org/
* https://github.com/pandao/editor.md
* https://pandao.github.io/editor.md/