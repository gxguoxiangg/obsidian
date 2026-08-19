####	docker网络



docker有如下几种网络模式：

- **Host**：容器不会虚拟出自己的网卡，配置自己的IP等，而是使用宿主机的IP和端口。
- **Container**：创建的容器不会创建自己的网卡，配置自己的IP，而是和一个指定的容器共享IP，端口范围。
- **None**：该模式关闭容器的网络功能。
- **Bridge（Default）**：默认为该格式，此模式会为每个容器分配独立的地址。使用一个Docker内置的网桥Docker0作为容器的网络接口，容器之间互相隔离，但可以通过网络互相通信。这种模式下，容器和宿主机的地位是平等的，都连接到了一个叫作“网桥”的东西上。这种模式适用于构建复杂的多容器应用程序，容器之间需要互相通信，同时需要保持网络隔离的场景。
- **自定义**：



容器默认权限较低，无法更改网络配置，更改内核参数等等，需要使用以下选项来对容器本身的能力进行开放或限制：

- --privileged
- --cap-add
- --cap-drop





> 配置自定义网络

```bash
docker network create --driver=bridge --gateway=192.168.137.1 --subnet=192.168.137.0/16 myNet2
```



> 查看自定义网络详细信息

```bash
docker network inspect <xx>
```



> 使用自定义网络并固定IP启动容器

```bash
docker run -itd --name <xx> --net <xx> --ip <ip_address> <images> /bin/bash
```

















###	docker



> 查看本地镜像

```bash
docker images
```





>  获取镜像

```bash
docker pull xxx
```



> 运行容器

```bash
docker run xxx:lates	# 包含了 docker pull 和 docker start 两个操作 
```





> 查看运行的容器

```bash
docker ps
```



> 查看容器运行历史

```bash
docker ps -a
```



> 以分离模式运行容器

```bash
docker run -d xxx
```



> 设置容器的端口映射

```bash
docker run -p6000:6389 xxx
```



> 查看容器运行日志

```bash
docker logs {NAME, CONTAINER ID}   # []可选 {}从中选一个 <>必选
```



> 自定义容器名字

```bash
docker run --name my_name xxx
```



> 进入运行的容器内部

```bash
docker exec -it mycontainer /bin/bash
# -i --interactive	防止bash进程立即退出
# -t -tty	打开虚拟终端
```



> 查看网络

```bash
docker network ls
```



> 创建网络

```bash
docker network create xxx
```

****



###	command

可能通用的选项

| 名称          | 值   | 描述                             |
| ------------- | ---- | -------------------------------- |
| --human，-H   |      | 以人类可读的格式打印尺寸和日期   |
| --no-trunc    |      | 不要截断输出                     |
| --quiet，-q   |      | 仅显示id                         |
| --change，-c  |      | 将Dockerfile指令应用到创建的镜像 |
| --http-proxy  |      | 配置代理                         |
| --https-proxy |      |                                  |
| --no-proxy    |      |                                  |
| --filter，-f  |      | 过滤                             |
|               |      |                                  |







####	系统操作



> 查看docker版本信息

```bash
docker version
```



> 查看docker当前的存储驱动类型

```bash
docker info
```







####	镜像操作

> 查看docker主机上的镜像

```cmd
docker images	# 输出所有顶层镜像,以及它们的存储库和标签还有大小

docker images --digests	# 显示sha256sum摘要信息

docker images xxx # 只显示完全匹配存储库名称的镜像

docker images -f "dangling=true" # 显示未标记的镜像
# -f,--filter 可以通过时间,标签，模式匹配来过滤
```



> 查看镜像的构建历史

```bash
docker history IMAGE

docker image inspect IMAGE
```



> 从包（tar)导出内容创建镜像

```bash
docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]
# file | URL | - : 可以是本地,远程,或者是从stdin中的包
# [OPTIONS] : -c, --message|-m
# 如果没有REPOSITORY:[TAG] 将是一个未标记镜像
```





> 拉取镜像

```bash
docker pull [OPTIONS] NAME[:TAG|@DIGEST]

# 如果没有提供tag,docker 默认使用:latest
docker pull ubuntu


#通过sha256sum摘要拉取镜像 摘要是不可变的
docker pull busybox@3fbc632167424a6d997e74f52b878d7cc478225cffac6bc977eedfe51c7f4e79

# Digest 可以使用在Dockerfile中
FROM ubuntu@sha256:26c68657ccce2cb0a31b330cb0be2b5e108d467f641c62e13ab40cbec258c68d

# 从不同的仓库中提取
docker pull myregistry.local:xxx/xxx/xxx
# 凭据由docker login管理

# 拉取多个镜像
docker pull --all-tags|-a ubuntu

# CTRL-c终止docker pull进程
```



> 修建镜像

```bash
docker image prune [OPTIONS]

# 删除所有未使用的镜像,而不仅仅是悬空的镜像
docker image prune -a

# --filter 
# --force, -f
```





####	docker inspect

返回docker对象的底层信息

```bash
docker inspect [OPTIONS] NAME|ID [NAME|ID...]
```



指定目标类型

```bash
docker inspect --type=volume myvolume
```



增加容器大小字段

```bash
docker inspect -s xxx
# SizeRootFs : 容器中所有文件的总大小
# SizeRw : 容器中已创建或更改的文件与其镜像相比的大小
```



设置输出格式

```bash
docker --format,-f={'xxx'} myinstance
```



####	docker kill

杀死一个或多个正在运行的容器

```bash
docker kill [OPTIONS] CONTAINER [CONTAINER...]
```

docker kill 会强制终止容器的运行，就像是发送了一个强制终止信号给容器。这会立即停止容器，不会给容器执行任何清理操作的机会，可能会导致正在进行的操作中断或数据丢失



默认（SIGKILL）信号将终止容器，但设置的信号`--signal`可能是非终止，具体取决于容器的主进程。

```bash
docker kill --signal=SIGHUP my_container
docker kill --signal=1 my_container
```



####	docker load

从tar包或stdin中加载镜像，会还原被保存的镜像层和元数据。常用于备份和恢复镜像

```bash
docker load [OPTIONS]
```

从STDIN加载镜像

```bash
docker load < busybox.tar.gz
```

从文件加载镜像

```bash
docker load --input fedora.tar
```



####	docker login

登录注册表

```bash
docker login [OPTIONS] [SERVER]
```

其中，选项有：

| 名称             | 描述              |
| ---------------- | ----------------- |
| --password，-p   | 密码              |
| --password-stdin | 从stdin中获取密码 |
| --username，-u   | 用户名            |

```bash
cat ~/my_password.txt | docker login --username foo --password-stdin
```



####	docker logout

从注册表中注销

```bash
docker logout [SERVER]
```



####	docker logs

获取容器的日志

```bash
docker logs [OPTIONS] CONTAINER
```

其中，选项有：

| 名称      | 描述               |
| --------- | ------------------ |
| --details | 显示日志的额外信息 |
|           |                    |
|           |                    |



####	docker network

管理网络

```bash
docker network COMMAND
```

可以使用子命令来创建，检查，列出，删除，修建，连接和断开网络



**连接**

```bash
docker network connect [OPTIONS] NETWORK CONTIANER
```



**创建**

```bash
docker network create [OPTIONS] NETWORK
```



**断开**

```bash
docker network disconnect [OPTIONS] NETWORK CONTIANER
```

可用选项：

| 名称        | 描述                 |
| ----------- | -------------------- |
| --force，-f | 强制容器端口网络连接 |



```bash
docker network disconnect multi-host-network container1
```





**检查**

```bash
docker network inspect [OPTIONS] NETWORK [NETWORK...]
```

默认情况下，所有结果将以JSON对象呈现



**遍历**

```bash
docker network ls [OPTIONS]
```



**修剪**

```bash
docker network prune [OPTIONS]
```

删除所有未使用的网络，未使用的网络是那些没有被任何容器引用的容器



**删除**

```bash
docker network rm NETWORK [NETWORK...]
```

