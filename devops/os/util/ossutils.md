---
draft: true
---

# 清空数据库数据；

	truncate table ar_item;
	truncate table ar_item_sku;
	-- truncate table ar_item_sku_detail; 暂时没用到

	truncate table ar_item_scene;
	truncate table ar_item_scene_detail;
	truncate table ar_item_scene_inspiration;

	truncate table ar_item_recommend;
	truncate table ar_measurement;
	truncate table ar_style;

	truncate table ar_item_category;
	-- drop table if exists ar_item_category_bak;


# 导入准备执行的文件到OSS，excel + image

	# frame
	ossutil -e oss-cn-hangzhou.aliyuncs.com -i LTAI5tF1kHaTc8Xxy9ea7n2x -k {JxCXaCXGVEgHs4kn/2AbMchve6SYEPku1APxSMDeXctEadBFaFX4eDGH+1KrzQw0}  ls -d oss://ma-frame-app

	# arbuy
	ossutil -e oss-cn-hangzhou.aliyuncs.com -i LTAI5tRngcVAndFSmApKr7go -k {+QzfMS0SvxsHbhsUrQpR16E0N9lKjutFOwtRDxWLHMRgwbHoPalf04BWz2P4GHxo} ls -d oss://meta-arbuy

	# alias
	alias ossarbuy='ossutilmac64 -e oss-cn-hangzhou.aliyuncs.com -i LTAI5tRngcVAndFSmApKr7go -k {+QzfMS0SvxsHbhsUrQpR16E0N9lKjutFOwtRDxWLHMRgwbHoPalf04BWz2P4GHxo}'
	alias ossframe='ossutilmac64 -e oss-cn-hangzhou.aliyuncs.com -i LTAI5tF1kHaTc8Xxy9ea7n2x -k {JxCXaCXGVEgHs4kn/2AbMchve6SYEPku1APxSMDeXctEadBFaFX4eDGH+1KrzQw0}'

	ossarbuy ls -d  -s oss://meta-arbuy/dev/  //列出objects
	./ossutil64 sync ./test_sync oss://wangtw-test-sync2/ --delete -f   // 上传 自动删除远端没有的
	./ossutil64 sync oss://wangtw-test-sync2 ./test_sync  --delete -f --backup-dir backup    // 从oss下载同步，并且备份本地差异文件。
	./ossutil64 sync oss://wangtw-test-sync2/prefix1 oss://wangtw-test-sync2/prefix2 --delete -f   // oss之间同步。


	# 删除
		# 单个文件
		ossarbuy rm oss://meta-arbuy/dev/import_img_bak/ikea/G_C/0_level/书房.jpg
		# 文件夹
		ossarbuy rm -rf oss://meta-arbuy/dev/import_img_bak/ikea/G_C/0_level/


	# model
		1) 上传到test
		ossarbuy sync  ~/Desktop/art-world/model/ios  oss://meta-arbuy/test/model/ios
		2) 同步到dev
		ossarbuy sync -f  oss://meta-arbuy/test/model/ios  oss://meta-arbuy/dev/model/ios

	# 准备excel 和 image.zip
		ossarbuy cp -f ~/Desktop/art-world/导入数据专用.zip oss://meta-arbuy/dev/导入数据专用.zip
		ossarbuy sync -f ~/Desktop/art-world/_scratch oss://meta-arbuy/dev/_scratch
	

# 执行开放的API

	1, 获取token
		# test : https://admin-test.arbuy.art/index
		$ Authorization=''
		curl  -H "authorization: $Authorization"

		# 设置请求url
		reqUrl='http://localhost:8080'
		reqUrl='https://admin-test.arbuy.art/test-api'
		reqUrl='https://admin.arbuy.art/prod-api'

	2, curl 请求excel导入数据
		curl -X POST -H  "Authorization: $Authorization" -H  "Content-Type:multipart/form-data" -F  "check=false" -F  "force=true" -F  "key=dev/_scratch/首批：后台数据上传整理表.xlsx" -F  "sheetName=商品分组"  "$reqUrl/tool/import/ikea"

		curl -X POST -H  "Authorization: $Authorization" -H  "Content-Type:multipart/form-data" -F  "check=true" -F  "force=false" -F  "key=dev/_scratch/首批：后台数据上传整理表.xlsx" -F  "sheetName=商品分组"  "$reqUrl/tool/import/ikea"
		curl -X POST -H  "Authorization: $Authorization" -H  "Content-Type:multipart/form-data" -F  "check=true" -F  "force=false" -F  "key=dev/_scratch/首批：后台数据上传整理表.xlsx" -F  "sheetName=场景组合"  "$reqUrl/tool/import/ikea"
		curl -X POST -H  "Authorization: $Authorization" -H  "Content-Type:multipart/form-data" -F  "check=true" -F  "force=false" -F  "key=dev/_scratch/首批：后台数据上传整理表.xlsx" -F  "sheetName=场景组合详情"  "$reqUrl/tool/import/ikea"
		curl -X POST -H  "Authorization: $Authorization" -H  "Content-Type:multipart/form-data" -F  "check=true" -F  "force=false" -F  "key=dev/_scratch/首批：后台数据上传整理表.xlsx" -F  "sheetName=我的装修-推荐列表"  "$reqUrl/tool/import/ikea"

		curl -X POST -H  "Authorization: $Authorization" -H  "Content-Type:multipart/form-data" -F  "check=false" -F  "force=true" -F  "key=dev/_scratch/表1_商品数据信息（宜家购）-单体（调整-1）.xlsx" -F  "sheetName=制作排序（第一批）OK"  "$reqUrl/tool/import/ikea"

		curl -X POST -H  "Authorization: $Authorization" -H  "Content-Type:multipart/form-data" -F  "check=true" -F  "force=false" -F  "key=dev/_scratch/表1_商品数据信息（宜家购）-单体（调整-1）.xlsx" -F  "sheetName=制作排序（第一批）OK"  "$reqUrl/tool/import/ikea"
		curl -X POST -H  "Authorization: $Authorization" -H  "Content-Type:multipart/form-data" -F  "check=true" -F  "force=false" -F  "key=dev/_scratch/表1_商品数据信息（宜家购）-单体（调整-1）.xlsx" -F  "sheetName=第二批（OK）"  "$reqUrl/tool/import/ikea"

	3, crul 请求更新图

		curl -X POST -H  "Authorization: $Authorization" -H  "Content-Type:application/x-www-form-urlencoded" --data-urlencode  "force=true&key=dev/_scratch/导入数据专用.zip&type=场景一级分组对应" "$reqUrl/tool/import/importReallyImg"
		curl -X POST -H  "Authorization: $Authorization" -H  "Content-Type:application/x-www-form-urlencoded" --data-urlencode  "force=false&key=dev/_scratch/导入数据专用.zip&type=商品一级分组对应" "$reqUrl/tool/import/importReallyImg"
		curl -X POST -H  "Authorization: $Authorization" -H  "Content-Type:application/x-www-form-urlencoded" --data-urlencode  "force=false&key=dev/_scratch/导入数据专用.zip&type=推荐列表图片" "$reqUrl/tool/import/importReallyImg"
		curl -X POST -H  "Authorization: $Authorization" -H  "Content-Type:application/x-www-form-urlencoded" --data-urlencode  "force=false&key=dev/_scratch/导入数据专用.zip&type=商品分组图片" "$reqUrl/tool/import/importReallyImg"
		curl -X POST -H  "Authorization: $Authorization" -H  "Content-Type:application/x-www-form-urlencoded" --data-urlencode  "force=false&key=dev/_scratch/导入数据专用.zip&type=场景分组图片" "$reqUrl/tool/import/importReallyImg"







# OVER
