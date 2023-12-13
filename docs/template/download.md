## 网页下载需要修改的部分 
```shell
1. <body data-page="/Users/mac/Downloads/REPORT/README.md" class="sticky ready" style="">

    ==> <body data-page="/Users/mac/Downloads/REPORT/README.md" class="sticky ready close" style="">

2.  body.close .content {
        left: 200px;
    }

3.  body:not(.ready) {
        # overflow: hidden;
    }
```
```shell
# vus.css
# body.close .content{left:0}
# body:not(.ready){overflow:hidden}
find . -type f -name 'vue.css' -exec sed -i '' \
    -e 's/body\.close \.content{left:0}/body\.close \.content{left:200px}/g' \
    -e 's/body:not(\.ready){overflow:hidden}/body:not(\.ready){\/*overflow:hidden*\/}/g' \
    {} +

# *.html
find . -type f -name '*.html'  -exec sed -i '' \
    -e 's/class=".*sticky.*" /class="sticky close" /g' \
    -e 's/<a href="https:\/\/github\.com\/xhsgg12302\/knownledges.*<\/a>//g' \
    -e 's/<a style="text-decoration: underline;.*Edit on github<\/a>//g' {} +
```