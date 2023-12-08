## 网页下载需要修改的部分 
```shell
1. <body data-page="/Users/mac/Downloads/REPORT/README.md" class="sticky ready" style="">

    ==> <body data-page="/Users/mac/Downloads/REPORT/README.md" class="sticky ready close" style="">

2.  body.close .content {
        left: 300px;
    }

3.  body:not(.ready) {
        # overflow: hidden;
    }
```