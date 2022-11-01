## 基本命令

## 应用
  
1. ### 利用共有仓库存储日常搜集的安装包供后续使用

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

2. ### 将springboot项目打包做成镜像，通过docker直接启动
    + 简要说明
        > 将原来的项目通过docker build 打包成一个镜像，如果需要查看API，或者说项目功能的话就不用直接打开IDEA查看了。
   
    + 方式
        * 把依赖组件(redis,zk,mysql)编排，一次性构建
            - 基于依赖镜像构建，比如：
                ```docker
                # ---- Dependencies ----
                FROM node:12-alpine AS dependencies
                WORKDIR /app
                COPY package.json ./
                RUN yarn install

                # ---- Build ----
                FROM dependencies AS build
                WORKDIR /app
                COPY . /app
                RUN yarn build

                FROM nginx:1.16-alpine
                COPY --from=build /app/dist /usr/share/nginx/html
                EXPOSE 80
                CMD [ "nginx", "-g", "daemon off;" ]
                ```
            - 基于docker compose 进行编排
        * 分开单独打包
            1. 创建docker网络
                ```shell
                # 创建网络
                $ docker network create demo-net

                # 查看docker网络信息
                $ docker network ls
                $ docker network inspect demo-net

                # 删除指定网络
                $ docker network rm demo-net
                ```
            2. 单独启动redis `$ docker run -d -p 6379:6370 --name rds6379 --network demo-net [--network-alias redis] redis:6.2.7`
            3. 单独启动zookeeper `$ docker run -d -p 2181:2181 --name zk2181 --network demo-net [--network-alias zookeeper] zookeeper:3.6.3 `
            4. 使用centos加入网络环境ping检测网络环境是否正常 `$ docker run -it --name test-centos --net demo-net centos`
            5. 构建springboot镜像
                ```shell
                # 修改配置文件中主机名，包括Redis，MySQL，zookeeper等
               
                # 通过Dockerfile构建镜像
                $ docker build -f Dockerfile -t service:version . 

                # 可以push镜像到仓库，方便不同环境使用
                $ docker login --username=5127*****@qq.com registry.cn-hangzhou.aliyuncs.com
                $ docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/eli_w/service:[镜像版本号]
                $ docker push registry.cn-hangzhou.aliyuncs.com/eli_w/service:[镜像版本号]
                ```
            6. 启动镜像启动镜像并检查是否正常 `$ docker run -d -p 9017:9017 --name insurance-center --net demo-net insurance-center:5.0.1`

3. ###  其他应用
    > 有待补充

## Reference

- [docker docs](https://docs.docker.com/engine/reference/run/)
- [容器镜像服务](https://cr.console.aliyun.com/cn-hangzhou/instance/repositories)

