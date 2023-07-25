* ## 基本命令
    + ### BASE
        ```shell
        # 提交镜像
        docker commit -a "xhsgg12302@gmail.com" -m "with requirements.txt" 44f357ee68bc  eli/anaconda3:v1.0
        # 加载镜像
        docker load -i  /path/tmp/docker-build.tar
        # 删除容器
        docker ps -qa | xargs -n 1 docker rm -f

        # 这将列出所有没有被任何标签或容器引用的镜像。这些镜像通常被称为悬空镜像，因为它们没有被命名或引用，而且也没有被垃圾回收机制删除。
        # https://docs.docker.com/engine/reference/commandline/images/#filter
        docker image ls -f "dangling=true"
        # 删除所有没有被标记或容器引用的悬空镜像
        docker image prune
        # 查找标记为 NONE 的镜像。运行以下命令：
        docker images | grep '<none>' | awk '{print $3}'
        # 删除标记为 NONE 的镜像。使用以下命令删除这些镜像：
        docker rmi $(docker images | grep '<none>' | awk '{print $3}')

        # 启动时去除基础镜像的entryponit  
        # https://stackoverflow.com/questions/40122152/how-to-remove-entrypoint-from-parent-image-on-dockerfile
        # 或者在Dockerfile中 写入 ENTRYPOINT []  
        docker run -d --entrypoint='' ff3b94479b5d /bin/sh -c "npm i; xvfb-run --server-args='-screen 0 1280x800x24 -ac -nolisten tcp -dpi 96 +extension RANDR' npm run dev"
        ```
    + ### MIRRORS
        ```shell
        {
            "experimental": true,
            "registry-mirrors": [
                "https://docker.mirrors.ustc.edu.cn",
                "https://hub-mirror.c.163.com",
                "https://reg-mirror.qiniu.com",
                "https://osssx6ow.mirror.aliyuncs.com"
            ]
        }
        ```


## 存储安装包

* ### 简要说明
    > 将一些日常工作中用到的常用软件或者破解工具以及流程做个记录，利用公共仓库进行存储，使用的时候直接docker拉取镜像运行，然后挂载容器内文件到本地，直接进行安装。

* ### 流程
    
    - 构建Dockerfile	
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
    - build镜像 
        + `$ docker build -f .\Dockerfile -t busybox-with-scrt:8.x  [--network host .] . ` 
        + `$ docker build -f Dockerfile -t wechat-chatgpt-base:v1.0 --network host --build-arg "HTTP_PROXY=http://192.168.164.191:7890" --build-arg "HTTPS_PROXY=http://192.168.164.191:7890" . `
        + `$ docker run -it -e http_proxy=192.168.164.191:7890 -e https_proxy=192.168.164.191:7890 demo:v1.0`
    - 推送镜像
        ```bash
        $ docker login --username=5127*****@qq.com registry.cn-hangzhou.aliyuncs.com
        $ docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/eli_w/busybox-with-scrt:[镜像版本号]
        $ docker push registry.cn-hangzhou.aliyuncs.com/eli_w/busybox-with-scrt:[镜像版本号]
        ```
    
    - 映射容器中的文件到宿主机的方式
        <!-- tabs:start -->
        #### **自动移动**
        ```bash
        # 有entrypoint.sh加持
        $ docker run -v C:\Users\neddn\Desktop\package:/data --rm -it [imageId]
        ```

        #### **手动移动**
        ```bash
        # docker pull registry.cn-hangzhou.aliyuncs.com/eli_w/busybox-with-pdf-books:1.0.0
        $ docker run -v ~/Desktop/books:/data --rm --name books -[d]it 5f1c5a7b06e9
        $ mv books/* /data/
        ```
        <!-- tabs:end -->

## 项目打包
* ### 简要说明
    > 将原来的项目通过docker build 打包成一个镜像，如果需要查看API，或者说项目功能的话就不用直接打开IDEA查看了。

* ### 方式一：(把依赖组件(redis,zk,mysql)编排，一次性构建)
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
        * docker-compose.yml
            ```yaml
            version: "3.7"
            services:
                app:
                    container_name: insuranceName
                    image: awesome/webapp
                    build: ./
                    ports:
                        - "9017:9017"
                    environment:
                        - TZ=Asia/Shanghai
                    networks:
                        - demo-net
                    depends_on:
                        - mysql
                        - redis
                        - zoo

                mysql:
                    hostname: mysql
                    image: mysql:5.7.26
                    # network_mode: "host" # 如果需要容器使用宿主机IP(内网IP)，则可以配置此项
                    container_name: mysql # 指定容器名称，如果不设置此参数，则由系统自动生成
                    restart: always # 设置容器自启模式
                    command: mysqld --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci # 设置utf8字符集
                    environment:
                        - TZ=Asia/Shanghai # 设置容器时区与宿主机保持一致
                        - MYSQL_ROOT_PASSWORD=root # 设置root密码
                    # volumes:
                        # - /etc/localtime:/etc/localtime:ro # 设置容器时区与宿主机保持一致
                        # - ./mysql/data:/var/lib/mysql/data # 映射数据库保存目录到宿主机，防止数据丢失
                        # - ./mysql/my.cnf:/etc/mysql/my.cnf # 映射数据库配置文件
                    ports:
                        - "3306:3306"
                    privileged: true
                    networks:
                        demo-net:
                            aliases:
                                - mysql

                redis:
                    hostname: redis-compose
                    image: redis:6.2.7
                    container_name: redis-compose
                    restart: always
                    # command: redis-server /etc/redis.conf # 启动redis命令
                    environment:
                        - TZ=Asia/Shanghai
                    volumes:
                        # - /etc/localtime:/etc/localtime:ro # 设置容器时区与宿主机保持一致
                        - redis:/data
                        # - ./redis/redis.conf:/etc/redis.conf
                    ports:
                        - "6379:6379"
                    privileged: true
                    networks:
                        demo-net:
                            aliases:
                                - redis

                zoo:
                    image: zookeeper:3.6.3
                    restart: always
                    container_name: zoo-compose
                    ports:
                        - "2181:2181"
                    networks:
                        demo-net:
                            aliases:
                                - zookeeper

            volumes:
                redis:

            networks:
                demo-net: 
            ```
        * 基本命令
            ```shell
            # 指定文件在后台启动
            $ docker-compose -f docker-compose.yml up -d [--build/是否重新构建]           
            # 查看运行状态：
            $ docker-compose ps
            # 停止运行：
            $ docker-compose down # Stop and remove containers, networks, images, and volumes
            $ docker-compose stop # Stop services
            # 重启:
            $ docker-compose restart
            # 重启单个服务：
            $ docker-compose restart service-name
            # 进入容器命令行：
            $ docker-compose exec service-name sh
            # 查看容器运行log：
            $ docker-compose logs [service-name]
            ```
* ### 方式2：（分开单独打包）
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

## MySQL数据卷容器
* ### 简要说明
    > 将以往的mysql数据库中的数据目录`datadir`进行保存，制作成数据卷容器，方便保存及后续供mysql镜像挂载

* ### 流程
    1. 获取 MySQL datadir目录中的数据
    2. 构建Dockerfile
        ```docker
        # Dockerfile
        FROM busybox:1.34.1
        LABEL maintainer = xhsgg12302@126.com
        ENV DATADIR /mysql-data/mysql
        RUN mkdir -p $DATADIR
        # 此处的mysql文件夹就是datadir指定的路径
        ADD mysql $DATADIR
        VOLUME $DATADIR
        COPY Dockerfile /
        CMD /bin/sh
        ```
    3. build 数据卷镜像 `$ docker build -f Dockerfile -t mysql-data:1.0.0 . `
        > push到仓库
        ```shell
        $ docker login --username=5127*****@qq.com registry.cn-hangzhou.aliyuncs.com
        $ docker tag mysql-data:1.0.0 registry.cn-hangzhou.aliyuncs.com/eli_w/busybox-with-mysql-datadir:1.0.0
        $ docker push registry.cn-hangzhou.aliyuncs.com/eli_w/busybox-with-mysql-datadir:1.0.0
        ```
    4. 启动数据卷容器 `$ docker run -dit --rm --name mysql-data mysql-data:1.0.0`
    5. 挂载数据卷容器启动MySQL服务 `$ docker run -d -p 3336:3336  --name mysql-5.6.49 --volumes-from mysql-data  mysql:5.6.49 --datadir=/mysql-data/mysql --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci --port=3336 --rm `



## Reference

- [docker docs](https://docs.docker.com/engine/reference/run/)
- [容器镜像服务](https://cr.console.aliyun.com/cn-hangzhou/instance/repositories)
- [docker volume 与bind的区别 和注意事项](https://codeantenna.com/a/YdJdiIFSh7)

