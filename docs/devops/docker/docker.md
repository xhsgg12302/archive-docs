* ## 基本命令
    + ### BASE
        ```shell
        # 提交镜像
        docker commit -a "xhsgg12302@gmail.com" -m "with requirements.txt" 44f357ee68bc  eli/anaconda3:v1.0
        # 标签镜像
        docker tag bad7184c3c60  gitlab.pixdaddy.com:5050/library/eclipse-temurin:17.0.2_8-jre-3.8.18-python-alpine3.17-with-pip-deps
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

        # docker 不使用缓存构建镜像
        docker build -t **:1.0 --no-cache -f Dockerfile . 
        # docker 清理构建缓存
        docker builder prune
        ```
    + ### MIRRORS
        ```shell
        # /etc/docker/daemon.json
        {
            "experimental": true,
            "runtimes": {
                "nvidia": {
                    "path": "nvidia-container-runtime",
                    "runtimeArgs": []
                }
            },
            "data-root": "/data/lib/docker",
            "registry-mirrors": [
                "https://docker.mirrors.ustc.edu.cn",
                "https://hub-mirror.c.163.com",
                "https://reg-mirror.qiniu.com",
                "https://osssx6ow.mirror.aliyuncs.com"
            ]
        }
        ```
    + ### CREDENTIAL
        [凭证官方文档](https://docs.docker.com/engine/reference/commandline/login/#credential-helper-protocol)
        ```shell
        # /etc/docker/config.json
        {
            "auths": {
                "harbor.meixiu.mobi": {},
                "https://index.docker.io/v1/": {},
                "registry.cn-hangzhou.aliyuncs.com": {}
            },
            "credsStore": "desktop"
        }
        ```
        !> 可以使用命令`file $(which docker-credential-desktop)`查看工具位置
        |ope    |  <div style="width: 610px">payload</div>   |   output  |
        -|-|-
        |list   | | {"https://gitlab.pixdaddy.com:5050":"w****","https://harbor.meixiu.mobi":"zh****",</br>"https://registry.cn-hangzhou.aliyuncs.com":"5127****@qq.com"} |
        |get    | https://registry.cn-hangzhou.aliyuncs.com ***CTRL+D*** | {"ServerURL":"https://registry.cn-hangzhou.aliyuncs.com","Username":"5127***@qq.com","Secret":"***"}
        |store  | {"ServerURL":"https://index.docker.io/v1","Username":"david","Secret":"passw0rd1"} ***CTRL+D***    | |
        |erase  | https://index.docker.io/v1    | |


- ## 使用样例
    1. ### 存储安装包
        * #### 简要说明
        ?> 将一些日常工作中用到的软件或者破解工具以及流程做个记录，利用公共仓库进行存储，使用的时候直接docker拉取镜像运行，然后挂载容器内文件到本地，直接进行安装。

        * #### 流程
            
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

    2. ### 项目打包
        * #### 简要说明
        ?> 将原来的项目通过docker build 打包成一个镜像，如果需要查看API，或者说项目功能的话就不用直接打开IDEA查看了。

        * #### 方式一：(把依赖组件(redis,zk,mysql)编排，一次性构建)
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
        * #### 方式2：（分开单独打包）
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

    3. ### 数据卷容器(MYSQL)
        * #### 简要说明
        ?> 将以往的mysql数据库中的数据目录`datadir`进行保存，制作成数据卷容器，方便保存及后续供mysql镜像挂载

        * #### 流程
            1. 获取 MySQL datadir目录中的数据
             
            ?> 第一种可以直接获取到存储数据的mysql`${datadir}`目录。</br>
            第二种是根据文件进行导入的，如下： 
            ```shell
            # offical site: https://hub.docker.com/_/mysql
            # 启动对应版本的mysql空实例
            docker run -d -p 3336:3336 -e MYSQL_ALLOW_EMPTY_PASSWORD=yes --name mysql-instance mysql:5.6.49 --datadir=/mysql-data/mysql --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci --port=3336
            # 链接实例，导入数据，生成数据目录
            # 从容器中导出数据
            docker cp d6b8e9a751e7:/mysql-data ./mysql-data
            ```
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
            3. build 数据卷镜像 `$ docker build -t mysql-data:1.0.0 -f Dockerfile . `
                > push到仓库
                ```shell
                $ docker login --username=5127*****@qq.com registry.cn-hangzhou.aliyuncs.com
                $ docker tag mysql-data:1.0.0 registry.cn-hangzhou.aliyuncs.com/eli_w/busybox-with-mysql-datadir:1.0.0
                $ docker push registry.cn-hangzhou.aliyuncs.com/eli_w/busybox-with-mysql-datadir:1.0.0
                ```
            4. 启动数据卷容器 `$ docker run -dit --rm --name mysql-data mysql-data:1.0.0`
            5. 挂载数据卷容器启动MySQL服务 
                ```shell
                docker run -d -p 3336:3336  --name mysql-5.6.49 \
                # -v /my/custom:/etc/mysql/conf.d \
                # -e MYSQL_ROOT_PASSWORD=my-secret-pw \
                --volumes-from mysql-data  mysql:5.6.49 \
                --datadir=/mysql-data/mysql \
                --character-set-server=utf8mb4 \
                --collation-server=utf8mb4_unicode_ci \
                --port=3336
                ```
            6. 使用docker内部`mysql客户端`进行测试
             
            ?> `docker run -it --rm --network=host mysql:5.6.49 mysql  -h 127.0.0.1 -u root -P 3336`

## 安装
?> 参考链接： </br>
https://docs.docker.com/engine/install/ubuntu/ </br>
https://zhuanlan.zhihu.com/p/628536643 </br>
https://help.aliyun.com/document_detail/51853.html

<!-- tabs:start -->
#### **Ubuntu:20.4**
```shell
# 卸载旧版
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Update the apt package index and install packages to allow apt to use a repository over HTTPS:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg

# Add Docker’s official GPG key:
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Use the following command to set up the repository:
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd
# sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo systemctl start docker
sudo systemctl enable docker
```

#### **Centos7.x**
```shell
# 运行以下命令，下载docker-ce的yum源。
sudo wget -O /etc/yum.repos.d/docker-ce.repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 运行以下命令，安装Docker。
sudo yum -y install docker-ce

sudo docker -v
sudo systemctl start docker
sudo systemctl status docker
sudo systemctl enable docker
```

#### **GPU**
```shell
# https://developer.nvidia.com/blog/gpu-containers-runtime/
# https://github.com/NVIDIA/nvidia-container-runtime
# http://imyhq.com/cloud-native/41210.html

curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update
sudo apt-get install -y nvidia-docker2
# sudo pkill -SIGHUP dockerd
# sudo systemctl restart docker
sudo nvidia-container-cli --load-kmods info
sudo docker run -it --rm --runtime=nvidia nvidia/cuda:12.2.0-base-ubuntu20.04
nvidia-smi   # output

# 另外一种访问方式：（也可以使用nvidia-smi）
docker run --rm -it -e NVIDIA_VISIBLE_DEVICES=all --runtime nvidia ubuntu:20.04 /bin/bash
# 或者
docker run --rm -it --gpus=all --runtime nvidia ubuntu:20.04 /bin/bash

# docker: Error response from daemon: could not select device driver "" with capabilities: [[gpu]].
```
![](/.images/devops/docker/docker-with-GPU.png)

#### **cuda**
```shell
# 检查是否可以访问到硬件设备:(apt install pciutils)
# https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#verify-you-have-a-cuda-capable-gpu
lspci | grep -i nvidia 
# lsmod | grep nvidia 可以验证驱动是否安装。

# https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html#ubuntu-lts
# apt-get install vim

# pip指定多个源:~/.pip/pip.conf
[global]
index-url=http://mirrors.aliyun.com/pypi/simple/
extra-index-url=https://pypi.tuna.tsinghua.edu.cn/simple/
                http://pypi.mirrors.ustc.edu.cn/simple/
                https://pypi.org/
[install]
trusted-host=mirrors.aliyun.com
             pypi.tuna.tsinghua.edu.cn
             pypi.mirrors.ustc.edu.cn
             pypi.org

# 运行stable-diffusion-ui
docker run -it --rm --gpus=all \
    -v ./miniconda3:/root/miniconda3 \
    -v /data/newbrush/ai/stable-diffusion-webui/models:/app/models \
    -v /data/newbrush/ai/stable-diffusion-webui/extensions:/app/extensions \
    -v /data/newbrush/ai/stable-diffusion-webui/github:/app/github \
    -v /data/newbrush/ai/stable-diffusion-webui/repositories:/app/repositories \
    -v ./venvs/sd/venv/:/app/venv \
    me/sd-ins:v1.1 /bin/bash
```
- Reference
    * https://linux.how2shout.com/how-to-install-cuda-on-ubuntu-20-04-lts-linux/
    * https://askubuntu.com/questions/1398568/installing-python-who-is-deadsnakes-and-why-should-i-trust-them
    * https://itsfoss.com/add-apt-repository-command-not-found/
    * https://gitlab.com/nvidia/container-images/cuda/blob/master/dist/12.2.0/ubuntu2004/base/Dockerfile
    * https://stackoverflow.com/questions/58269375/how-to-install-packages-with-miniconda-in-dockerfile
<!-- tabs:end -->

## Reference

* https://docs.docker.com/engine/reference/run
* https://cr.console.aliyun.com/cn-hangzhou/instance/repositories
* [docker volume 与bind的区别 和注意事项](https://codeantenna.com/a/YdJdiIFSh7)

