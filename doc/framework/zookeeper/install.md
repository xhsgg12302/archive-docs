* ## Intro(Zookeeper)

    + ## 压缩包安装

        <!-- tabs:start -->
        ####  **Docker**
        1. 配置环境

        ```shell
        # 启动服务器
        $ docker run -d -p 2181:2182 --name zookeeper-3.6.3 zookeeper:3.6.3

        # 使用自定义的配置文件
        $ docker run -d -p 2181:2182 -v $(pwd)/zoo.cfg:/conf/zoo.cfg --restart always --name zookeeper-3.6.3 zookeeper:3.6.3
        ```
        
        2. 测试

        ```shell
        # 使用redis容器内部客户端进行访问
        $ docker run -it --rm --network host zookeeper:3.6.3 zkCli.sh -server 127.0.0.1[:2181]
        ```
        
        ![](/.images/doc/framework/redis/redis-install-dcoker-01.png ':size=100%')

        - ##### Reference
            * https://hub.docker.com/_/zookeeper

        ####  **Centos7**
        ####  **Ubuntu**
        <!-- tabs:end -->

    ## 修改密码的几种方式

    ## 注意事项

## Reference
* https://hub.docker.com/_/zookeeper
* https://stackoverflow.com/questions/13316776/zookeeper-connection-error