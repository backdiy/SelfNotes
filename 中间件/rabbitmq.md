# 一、介绍

# 二、安装
## 使用Docker安装
再用Docker安装RabbitMq之前，得保证主机安装并运行了Docker，
### 1.拉取镜像
采用下面的命令拉取RabbitMq镜像：
```
docker pull rabbitmq:3.13.7
```
### 2.启动容器
需要创建RabbitMq容器挂载目录，再执行容器启动命令：
```bash
#创建挂载目录
mkdir -p /usr/local/docker/rabbitmq

#启动容器
docker run -d -p 15672:15672 -p 5672:5672 \  
        --restart=always \  
        -v /usr/local/docker/rabbitmq:/var/lib/rabbitmq \
        -e RABBITMQ_DEFAULT_VHOST=default \  
        -e RABBITMQ_DEFAULT_USER=admin \  
        -e RABBITMQ_DEFAULT_PASS=admin \  
        --hostname rabbitmq1 \  
        --name rabbitmq1 \  
        rabbitmq:latest

#查看容器日志
docker logs -f rabbitmq1
```

**注意**：在映射的端口号的时候不要映射`5671`端口，端口`5671`是 RabbitMQ 的默认AMQP over TLS/SSL端口。AMQP（Advanced Message Queuing Protocol）是一种消息传递协议，用于在应用程序之间进行可靠的消息传递。

**参数说明**：
* -d：表示在后台运行容器；  
* -p：将主机的端口 15672（Web访问端口号）对应当前rabbitmq容器中的`15672`端口，将主机的`5672`（应用访问端口）端口映射到rabbitmq中的5672端口；  
* --restart=alawys：设置开机自启动  
* -v /usr/local/docker/rabbitmq:/var/lib/rabbitmq：将主机目录挂载到容器内的 `/var/lib/rabbitmq`，用于持久化数据。
* -e：指定环境变量：  
    RABBITMQ_DEFAULT_VHOST：默认虚拟机名；  
    RABBITMQ_DEFAULT_USER：默认的用户名；  
    RABBITMQ_DEFAULT_PASS：默认的用户密码；  
* --hostname：指定主机名（RabbitMQ 的一个重要注意事项是它根据所谓的`节点名称`存储数据，默认为主机名）；  
* --name rabbitmq-new：设置容器名称。

### 3.常见问题
1.启动web客户端
启动容器后执行下面的命令：
```bash
docker exec -it rabbitmq1 rabbitmq-plugins enable rabbitmq_management
```
然后再浏览器中访问ip:15672即可打开rabbitmq的web客户端。
2.在web客户端发现提示`Stats in management UI are disabled on this node`
解决该问题需要进入容器中修改配置文件：
```bash
#进入容器
docker exec -it rabbitmq1 /bin/bash

#修改配置
echo management_agent.disable_metrics_collector = false > management_agent.disable_metrics_collector.conf

#重启容器
docker restart rabbitmq1
```
