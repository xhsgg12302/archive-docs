## 基本命令

## 应用
  
1. 利用共有仓库存储日常搜集的安装包供后续使用

    + 简要说明
    	> 将一些日常工作中用到的常用软件或者破解工具以及流程做个记录，利用公共仓库进行存储，使用的时候直接docker拉取镜像运行，然后挂载容器内文件到本地，直接进行安装。

	+ 流程
		
		* 构建Dockerfile	
			```docker
			# Dockerfile
			FROM busybox
			LABEL maintainer xhsgg12302@126.com

			ENV DATADIR /target

			RUN mkdir -p $DATADIR

			ADD scrt_sfx-x64.8.5.4.1942.exe securecrtzcj.rar SecureCRT..........zip VanDyke-SecureCRT-and-SecureFX-Crack.7z  $DATADI
			# VOLUME ["/target"]

			COPY entrypoint.sh /
			COPY Dockerfile /

			ENTRYPOINT ["/entrypoint.sh"]

			CMD ["sh"]
			```
			> *添加entrypoint.sh 是因为需要容器启动后执行移动相应文件到挂载目录的操作*
			```bash
			#!/bin/sh
			# vim:sw=4:ts=4:et
			# entrypoint.sh

			mkdir -p /data
			mv /target/* /data/

			# echo "$@"  ==>  sh
			exec "$@"
			```
		* build镜像 `$ docker build -f .\Dockerfile -t busybox-with-scrt:8.x . `
		* 推送镜像
			```bash
			$ docker login --username=5127*****@qq.com registry.cn-hangzhou.aliyuncs.com
			$ docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/eli_w/busybox-with-scrt:[镜像版本号]
			$ docker push registry.cn-hangzhou.aliyuncs.com/eli_w/busybox-with-scrt:[镜像版本号]
			```
		* 运行容器挂载文件使用
			```bash
			$ docker run -v C:\Users\neddn\Desktop\package:/data --rm -it [imageId]
			```








2.  其他应用
    > 有待补充

## Reference

- [docker docs](https://docs.docker.com/engine/reference/run/)

