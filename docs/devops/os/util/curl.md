## SYNTAX
```shell
# 1、直接访问默认是get
curl http://www.example.com 
# 2、-i,--include 打印响应头
curl http://www.example.com -i 
# 3、-I,--head 大写的只会打印响应头信息
curl http://www.example.com -I
# 3、-v,--verbose 详细信息
curl http://www.example.com -v
# 4、-d,--date 请求数据 配合-X(请求方式)
curl http://www.example.com -X post -d "username=xhsgg&pwd=123456"
# 5、-b,--cookie 附带cookie信息
curl http://www.example.com -b "JSESSIONID=************;time=***"
# 6、-H,--header 请求头消息
curl http://www.example.com -H "Content-Type:application/json"
# 7、-F,--form 表单的内容，可以上传文件
curl http://www.example.com -F "file=@_path_"
```

## 实例
* ### ajax提交new from
* ### 请求打码服务器
* ### curl -k 忽略证书授信问题