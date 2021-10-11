

## 简述

解决服务在部署时,  开发环境和生产环境不一致, 导致服务无法正常运行的问题, 

docker让代码,配置, 环境, 数据等一起打包,交给服务器部署

ps: 构建, 安装, 运行任何应用在任何地点

**三要素**:  仓库, 镜像, 容器

docker的软件镜像会包含基础运行环境

```
# 例 tomcat镜像(例子不精确)
kernel内核 > centos系统 > jdk8 > tomcat
```



## 安装(centos7 以上)

官网:   https://docs.docker.com/engine/install/centos/#install-using-the-repository

- 安装yum-utils, 提供yum-config-manager命令

```shell
sudo yum install -y yum-utils
```

- 设置**稳定的**存储库

```shell
# sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo   # 阿里云
```

- 更新软件包索引

```shell
yum makecache fast
```

- 安装*最新版本*的Docker Engine和容器

```
sudo yum install docker-ce docker-ce-cli containerd.io
```

- 启动Docker

```
sudo systemctl start docker
```

- 配置docker 阿里云镜像加速器(**可选**)

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["阿里云镜像加速地址"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

- 查看docker进程

  centos7 在进程中不显示阿里云的镜像地址

```
ps -ef|grep docker
```

- 运行`hello-world` 映像来验证是否正确安装了Docker Engine

```
sudo docker run hello-world
```

## 命令

### 系统

#### 查看版本

```
docker version
```

#### 查看docker软件信息

```
docker info
```

#### 帮助

```
docker --help
```

#### 网络

##### 查看所有docker网络

```bash
docker network ls
```



### 镜像

#### 本地镜像列表

参数

- -a   全部镜像, 含中间镜像层
- -q   只显示镜像ID
- --digests   显示镜像摘要信息
- --no-trunc    显示镜像完整信息

列表内容

- REPOSITORY   镜像仓库源
- TAG   镜像的标签(版本)
- IMAGE ID    镜像ID
- CREATED   镜像创建的时间
- SIZE   镜像大小

```
docker images [参数]
```

#### 查询

从docker hub上查找, 从配置的阿里云上下载

参数

- --filter=stars=n 列出点赞数大于等于n的镜像
- --no-trunc    显示镜像完整信息
- --automated  只列出automated build类型的镜像

```
docker search [镜像名]
```

#### 下载

```
docker pull [镜像名:[版本号]]
```

#### 删除

- -f   强制删除

```
docker rmi -f [镜像名]
```

#### 批量删除

- -f   强制删除

```
docker rmi -f [镜像名] [镜像名] [镜像名]...
```

#### 全部删除

组合命令,  先查出本地镜像的id,  再删除

```
docker rmi -f $(docker images -qa)
```

#### 创建镜像

创建容器并运行, 基于创建的容器副本,  使之成为一个新的镜像

docker commit

- -a="[作者名]"
- -m="[注释]"

```
# 例
docker commit -a="itianeru" -m="demo" [容器id] [命名空间/镜像名:版本号]
```

docker build

通过DockerFile文件创建镜像

```
# 例 DockerFile 文件
FROM centos
VOLUME ["/inContainerData1", "/inContainerData2"]
CMD echo "success"
CMD /bin/bash
```

```
# 创建镜像
docker build -f [DockerFile文件路径] -t [创建的镜像名:版本号] [创建的镜像存放路径]
```

#### 查看镜像历史

docker history

查看镜像由dockerFile创建的过程

```
docker history [镜像名/镜像ID]
```

#### 修改镜像名称 | tag

给镜像修改名称和tag

```bash
docker tag [镜像id] [新的名称:版本]
```

#### 提交镜像

##### 发布到阿里云上 

- 登录阿里云
- 创建命名空间
- 创建仓库 
- 在仓库列表点击管理按钮， 会有提交文档

```
sudo docker login --username=[用户手机号] registry.cn-beijing.aliyuncs.com
sudo docker tag [镜像id] registry.cn-beijing.aliyuncs.com/itianeru/[镜像名]:[镜像版本号]
sudo docker push registry.cn-beijing.aliyuncs.com/itianeru/[镜像名]:[镜像版本号]
```

##### 发布到DockerHub

```bash
docker login --username=[账号]
docker push [账号]/[镜像名]:[镜像版本号]
```

#### 分享镜像

##### 打包镜像为压缩包

```bash
docker save -o [路径/压缩包] [镜像名]
```

##### 加载压缩包为镜像

```bash
docker load -i [路径/压缩包]
```



### 容器

#### 创建并运行

docker run

- --name [名字]    给容器创建名字

- -d   后台创建容器, 并返回容器ID,   启动守护式容器

  ​      因为后台创建容器, 与前台没有交互, 所以创建后就会自动停止, 需要以前台进程的形式运行

  ```
  docker run -d centos /bin/sh -c "while true;do echo hello;sleep 2;done"
  ```

- -i   以交互模式启动容器, 通常与-t  同时使用

- -t   为容器重新分配一个伪输入终端, 通常与-i 同时使用

- -P   随机端口映射

- -p   指定端口映射

  - ip:hostPort:containerPort
  - ip::containerPort
  - hostPort:containerPort
  - containerPort

```
docker run -it [镜像名/镜像id]
# 随机端口映射
docker run -it -P [镜像名/镜像id]
# 访问docker内的服务, 需要先访问docker的端口, 再访问容器内的端口
docker run -it -p [容器端口]:[容器内的服务端口] [镜像名/镜像id]
```

#### 查询

docker ps

- -a  列出当前所有正在运行的容器和历史运行过的容器
- -l    显示最近创建的容器(last)
- -n  [数字]  显示最近创建的n个容器
- -q   只显示容器编号
- --no-trunc   显示完整信息

```
docker ps
```

#### 退出

exit

停止容器退出

```
exit
```

ctrl + p + q

不停止容器退出

#### 启动

docker start

```
docker start [容器名/容器id]
```

#### 重启

docker restart

```
docker restart [容器名/容器id]
```

#### 停止

docker stop

普通停止

```
docker stop [容器名/容器id]
```

docker kill

强制停止

```
docker kill [容器名/容器id]
```

#### 删除

docker rm

- -f  强制删除

删除已停止的容器

```
docker rm [容器名/容器id]
```

docker rm -f  $(docker ps -a -q)  /  docker ps -a -q | xargs docker rm

批量删除

```
# 批量删除两种方式
docker rm -f  $(docker ps -a -q)
docker ps -a -q | xargs docker rm
```

#### 进入

docker exec -it [容器名/id] /bin/bash

以交互模式, 进入容器, 创建新的进程(终端)

```
docker exec -it [容器名/id] /bin/bash
```

后面可加上想要在容器内部操作的命令

```
docker exec [名/id] ls -l
```

docker attach

不会创建新的进程(终端) ,  多个用户登录时, 会造成共享界面

```
docker attach [容器名/id]
```



#### 查看容器日志

docker logs

- -t   加入时间戳
- -f   跟随最新日志打印
- --tail [n]   显示最后多少条

```
docker logs -t -f --tall 100 [容器名/容器id]
```

#### 查看容器进程

docker top

```
docker top [容器名/容器id]
```

#### 查看容器内部配置

docker inspect

以json串格式描述细节

```
docker inspect [容器名/容器id]
```

#### 拷贝容器中的文件到宿主机

docker cp

```
docker cp [容器名/容器id]:[目标文件路径] [拷贝到的位置]
```

#### 容器数据卷

使容器中的数据持久化, 主机与容器互通数据

docker run -it -v     (v 表示 volumes  卷)

##### 指定路径挂载

```
# 在宿主机创建一个数据存储目录,  并在容器内创建一个数据存储目录,  两个目录连接进行数据共享
docker run -it -v  [宿主机绝对路径目录]:[容器内目录] [镜像名]
# ro表示read-only 宿主机的数据, 容器只能读, 不能写 rw表示read-write可读写
docker run -it -v  [宿主机绝对路径目录]:[容器内目录]:ro [镜像名]
```

##### 匿名挂载

```bash
# 不指定宿主机的地址或名称
docker run -it -v [容器内目录] [镜像名]
```

##### 具名挂载

```bash
# 数据卷的名称不是地址, 前面不带 /
docker run -it -v [数据卷的名称]:[容器内目录] [镜像名]
```

##### 常用命令
```bash
# 查看全部数据卷
docker volume ls
# 查看数据卷详情
docker volume inspect [数据卷名称 | id]
```



#### DockerFile

文件名最好就叫DockerFile, 在build的时候, 会自动寻找这个文件, 如果不是这个名字需要加 -f

用于描述docker镜像,  由一系列命令和参数构成的脚本

DockerFIle 镜像指定容器数据卷时, 为防止镜像在多主机使用, 宿主机指定的文件夹不存在,镜像会只指定容器内的目录

使用镜像创建容器时, 会自动在宿主机创建目录与容器的目录进行绑定, 绑定的文件夹, 可使用docker inspect [容器]查看

使用步骤:  

```
编写dockerFile文件->docker build 生成镜像->  run镜像,  生成容器 
```

编写注意事项

- 每个保留字指令都要大写, 后面的参数不能为空
- 指令从上到下依次执行
- \# 表示注释
- 每条指令都会创建一个新的镜像层, 并对镜像进行提交

docker执行dockerFile的大致步骤

```
docker从基础镜像运行一个容器 -> 执行一条指令, 并对容器做出修改 -> 对修改后的容器进行提交, 生成新的镜像 -> 再基于新的镜像创建新的容器 -> 执行下一条指令, 直到所有指令执行完成
```

保留字指令

- FROM     基础镜像, 当前镜像是基于什么镜像

  - Base镜像(scratch)

- MAINTAINER   镜像维护者的姓名, 邮箱

  ```
  MAINTAINER  name<mail>
  ```

- RUN   容器构建需要的命令  

- EXPOSE   对外暴露的端口号

- WORKDIR  指定再创建容器后, 终端进入的目录

- ENV   用来再构建镜像过程中, 设置环境变量,  该变量, 可在其他命令中使用

  ```
  ENV [变量名] [变量值]
  # 使用时不带中括号
  WORKDIR  $[变量名]
  ```

- ADD   将构建过程中要使用的压缩包, 复制进去, 并解压缩到镜像中指定的文件夹

  ```bash
  ADD [/路径/安装包] [安装到docker内的路径]
  ```

  

- COPY    将构建过程中要使用的压缩包, 复制到镜像中指定的文件夹

- VOLUME   指定容器数据卷

- CMD   在构建镜像时会全部执行, 指定容器启动时, 要执行的命令, 多个CMD只有最后一个生效,  在终端使用docker run后面接的命令会替换掉CMD命令

  ```bash
  CMD ["ls"]
  # 或
  CMD /usr/local/apache-tomcat/bin/startup.sh && tail -f /usr/local/apache-tomcat/logs/catalina.out
  ```

  

- ENTRYPOINT  指定容器启动时, 要执行的命令, 可以有多个, 不会被替换， docker run后面接的命令会添加到ENTRYPOINT后面

- ONBUILD  构建一个被继承的镜像触发的指令,  父镜像被子镜像继承后, 父镜像的ONBUILD触发

```shell
# 例 创建带有vim的镜像

FROM centos

MAINTAINER itianeru<1438687182@qq.com>

ENV PATH /home

WORKDIR $PATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD ["ls"]

ENTRYPOINT  ["ls"]

VOLUME ["volume01", "volume02"]

CMD /bin/bash
```

```shell
# 例 tomcat

```



#### 数据卷容器

A容器挂载数据卷,  其他容器挂载A容器(父容器)实现数据共享, A容器称为数据卷容器,  多个子容器继承父容器后, 删除父容器, 子容器之间也可以数据共享

```
# 先创建父容器, 该镜像需要带有数据卷
docker run -it --name container01 centos
# container02 继承container01, 数据可互通共享
docker run -it --name container02 --volumes-from container01 centos  
```

## docker网络

### 理解docker0

docker0相当于一个路由器

每启动一个容器, 会在宿主机和容器内部创建一对对应的虚拟网卡, 每个容器分配和docker0同一网段的ip

### 最多能开启多少个容器

有65535个ip

### 如何解决ip在容器启动, 随机分配的问题

#### link(不推荐使用, 过于笨重)

容器在每次启动的时候, 所分配的ip可能是不同的, 如果不同的容器互相通过ip地址访问, 可能会发生ip地址变化导致找不到服务的问题

使用link参数可以让, 服务通过容器名称去调用服务, 而不是通过ip

```bash
docker run -d -P --name tomcat01 tomcat
# 使用link
docker run -d -P --name tomcat02 --link tomcat01 tomcat
# 这样在tomcat02的内部, 使用ping即可ping通, 但是tomcat01无法ping通tomcat02
# 原理: 是在tomcat02的hosts配置文件中, 加入了tomcat01和这个容器id的地址配置
ping tomcat01
```

#### 自定义网络

自定义的网络会解决默认的docker0不支持使用名称访问的问题

#### 默认设置

```bash
# 开启一个容器, 默认会--net bridge, 这里是使用docker0
docker run -d -P --name tomcat01 tomcat
docker run -d -P --name tomcat01 --net bridge tomcat
```

#### 自定义设置

```bash
# --driver bridge 设置为桥接
# --subnet 192.168.0.0/16 设置网段
# --gateway 192.168.0.1 网关
# mynet 配置名称
docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
# 查看
docker network inspect mynet
# 使用自定义的设置
docker run -d -P --name tomcat01 --net mynet tomcat
docker run -d -P --name tomcat02 --net mynet tomcat
# tomcat01 和 tomcat02 都使用了自定义的网络, 可以互相ping通
```

#### 使用场景

在部署集群服务时, 不用的集群使用不同的网络, mysql集群一个网络, redis集群一个网络

### 网络联通

两个不同的网络下的容器可以互联, 本质是让, 一个网络的容器, 能联通另一个网络

```bash
# mynet是自定义的网络
# tomcat-docker0是默认网络docker0中一个容器
# 内部是将tomcat-docker0这个容器加到了mynet下, 相当于tomcat-docker0有了两个IP
docker network connect mynet tomcat-docker0
```

## 例子

### mysql

- docker search mysql   # 查找

- docker pull mysql   # 下载

- docker images mysql   # 查看

- docker run   # 运行实例 

  - -p  3306:3306    # 端口映射
  - --name mysql    # 容器命名

  - -v /mysql/conf:/etc/mysql/conf.d   # 主机与容器共享配置文件
  - -v /mysql/logs:/logs    # 主机与容器共享日志
  - -v /mysql/data:/var/lib/mysql   # 主机与容器共享数据
  - -e MYSQL_ROOT_PASSWORD=123456   # 初始化root用户密码
  - -d mysql   # 后台运行

```cmd
docker run -p 3306:3306 --name mysql -v /home/li/mysql-data/conf:/etc/mysql/conf.d -v /home/li/mysql-data/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql
```

测试安装

- docker exec -it mysql /bin/bash   # 进入mysql容器终端
- mysql -uroot -p   
- 输入密码

备份

```
docker exec [容器名] sh -c ' exec mysqldump --all-databases -uroot -p"123456" ' > 【备份路径】
```

### redis

#### 单节点

- docker search redis   # 查找

- docker pull redis   # 下载

- docker images redis   # 查看

- docker run   # 运行实例 

  - -p  3306:3306    # 端口映射
  - --name redis    # 容器命名

  - -v /home/li/redis-data/data:/data   # 主机与容器共享数据
  - -v /home/li/redis-data/conf/redis.conf:/usr/local/etc/redis/redis.conf    # 主机与容器共享配置文件
  - -d redis   # 后台运行
  - redis-server /usr/local/etc/redis/redis.conf --appendonly yes   # 执行启动命令

```
docker run -p 6379:6379 --name redis -v /home/li/redis-data/data:/data -v /home/li/redis-data/conf/redis.conf:/usr/local/etc/redis/redis.conf  -d redis  redis-server /usr/local/etc/redis/redis.conf --appendonly yes --requirepass 123456
```

测试安装

- docker exec -it redis redis-cli -a 123456

#### 集群

三主三从

##### 创建网卡

```bash
docker network create --subnet 172.38.0.0/16 redis
```

##### shell脚本

直接粘贴到命令行执行

```shell
for port in $(seq 1 6); \
do \
mkdir -p /mydata/redis/node-${port}/conf
touch /mydata/redis/node-${port}/conf/redis.conf
cat  EOF /mydata/redis/node-${port}/conf/redis.conf
port 6379 
bind 0.0.0.0
cluster-enabled yes 
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.38.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done
```

##### 创建容器

```bash
docker run -p 6371:6379 -p 16371:16379 --name redis-1 \
    -v /mydata/redis/node-1/data:/data \
    -v /mydata/redis/node-1/conf/redis.conf:/etc/redis/redis.conf \
    -d --net redis --ip 172.38.0.11 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
   
docker run -p 6372:6379 -p 16372:16379 --name redis-2 \
    -v /mydata/redis/node-2/data:/data \
    -v /mydata/redis/node-2/conf/redis.conf:/etc/redis/redis.conf \
    -d --net redis --ip 172.38.0.12 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
    
# 剩余4个省略
```

##### 创建集群

```bash
# 进入容器, redis内部没使用/bin/bash, 使用的/bin/sh
docker exec -it redis-1 /bin/sh
# 创建, 选择yes 即可创建
redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379 --cluster-replicas 1
```

##### 测试

```bash
# 进入容器
docker exec -it redis-1 /bin/sh
# 连接 -c表示使用集群
redis-cli -c
	# 查看信息
	cluster info
	# 查看节点
	cluster nodes
```

### SpringBoot微服务打包Docker镜像

#### 创建Dockerfile

```dockerfile
FROM java:8
COPY *.jar /app.jar
CMD ["--server.port=8080"]
EXPOSE 8080
ENTRYPOINT ["java","-jar", "/app.jar"]
```

#### 创建镜像

将SpringBoot项目和Dockerfile文件放在一起

```bash
docker build -t [镜像名]
```

### Zookeeper

#### 下载

```bash
docker pull zookeeper
```

#### 创建容器

```bash
docker run -d -p 2181:2181 --name zookeeper zookeeper
```

### Consul

#### 下载

```bash
docker pull consul
```

#### 创建容器

```bash
docker run -d -p 8500:8500 --name consul consul agent -server -bootstrap -ui -node=1 -client='0.0.0.0'
```

- agent: 表示启动 Agent 进程。
- server：表示启动 Consul Server 模式
- client：表示启动 Consul Cilent 模式。
- bootstrap：表示这个节点是 Server-Leader ，每个数据中心只能运行一台服务器。技术角度上讲 Leader 是通过 Raft 算法选举的，但是集群第一次启动时需要一个引导 Leader，在引导群集后，建议不要使用此标志。
- ui：表示启动 Web UI 管理器，默认开放端口 8500，所以上面使用 Docker 命令把 8500 端口对外开放。
- node：节点的名称，集群中必须是唯一的，默认是该节点的主机名。
- client：consul服务侦听地址，这个地址提供HTTP、DNS、RPC等服务，默认是127.0.0.1所以不对外提供服务，如果你要对外提供服务改成0.0.0.0
- join：表示加入到某一个集群中去。 如：-json=192.168.0.11。

#### 进入容器

```bash
docker exec -it consul /bin/sh
```

### RabbitMQ

#### 下载

```bash
# 不带web控制台的版本
docker pull rabbitmq
# 带web控制台的版本
docker pull rabbitmq:3.9.5-management
```

#### 创建容器

```bash
docker run -d -p 15672:15672  -p  5672:5672  -e RABBITMQ_DEFAULT_USER=root -e RABBITMQ_DEFAULT_PASS=123456 --name rabbitmq rabbitmq
```

- -d 后台运行
- -p 隐射端口
- –name 指定rabbitMQ名称
- RABBITMQ_DEFAULT_USER 指定用户账号
- RABBITMQ_DEFAULT_PASS 指定账号密码

#### 控制台

```bash
# 如果不指定账号密码, 默认为guest/guest
http://localhost:15672/
```

### Zipkin

#### 下载

```bash
docker pull openzipkin/zipkin
```

#### 创建容器

```bash
docker run -d -p 9411:9411 openzipkin/zipkin
```

#### 控制台

```bash
http://your_host:9411
```

### Nacos

#### 单机

##### 下载

```bash
docker pull nacos/nacos-server
```

##### 创建容器

```bash
docker run -d  --name nacos -p 8848:8848 --env MODE=standalone  nacos/nacos-server
```

#### 集群

##### 拉取源代码

```bash
git clone https://github.com/nacos-group/nacos-docker.git
```

##### 改造集群搭建文件

在example/cluster-hostname.yaml文件中, 添加nginx配置

```yml
version: "3"
services:
  nacos1:
    hostname: nacos1
    container_name: nacos1
    image: nacos/nacos-server:${NACOS_VERSION}
    volumes:
      - ./cluster-logs/nacos1:/home/nacos/logs
      - ./init.d/custom.properties:/home/nacos/init.d/custom.properties
    ports:
      - "8848:8848"
      - "9848:9848"
      - "9555:9555"
    env_file:
      - ../env/nacos-hostname.env
    restart: always
    depends_on:
      - mysql

  nacos2:
    hostname: nacos2
    image: nacos/nacos-server:${NACOS_VERSION}
    container_name: nacos2
    volumes:
      - ./cluster-logs/nacos2:/home/nacos/logs
      - ./init.d/custom.properties:/home/nacos/init.d/custom.properties
    ports:
      - "8849:8848"
      - "9849:9848"
    env_file:
      - ../env/nacos-hostname.env
    restart: always
    depends_on:
      - mysql
  nacos3:
    hostname: nacos3
    image: nacos/nacos-server:${NACOS_VERSION}
    container_name: nacos3
    volumes:
      - ./cluster-logs/nacos3:/home/nacos/logs
      - ./init.d/custom.properties:/home/nacos/init.d/custom.properties
    ports:
      - "8850:8848"
      - "9850:9848"
    env_file:
      - ../env/nacos-hostname.env
    restart: always
    depends_on:
      - mysql
  mysql:
    container_name: nacos_mysql
    image: nacos/nacos-mysql:5.7
    env_file:
      - ../env/mysql.env
    volumes:
      - ./mysql:/var/lib/mysql
    ports:
      - "3306:3306"
  # 添加nginx配置
  nginx:
    container_name: nginx
    image: nginx:latest
    volumes:
      - D:\env\docker\nginx\conf\default.conf:/etc/nginx/conf.d/default.conf
    ports:
      - "8851:8851"
```

##### nginx配置文件

```properties
# 配置nacos集群, 负载均衡
upstream nacos {
    server nacos1:8848;
    server nacos2:8849; 
    server nacos3:8850;
}

server {
	# 修改端口号
    listen       8851;
    listen  [::]:8851;
    server_name  localhost;

    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        # root   /usr/share/nginx/html;
        # index  index.html index.htm;
        # 代理nacos
        proxy_pass http://nacos;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

##### 创建容器

```bash
# example/cluster-hostname.yaml 是拉取的项目中的配置文件, 可按需修改
docker-compose -f example/cluster-hostname.yaml up
```

#### 控制台

```bash
# 默认账号密码 nacos/nacos
http://localhost:8848/nacos
```

### Nginx

#### 下载

```bash
docker pull nginx
```

#### 运行

```bash
docker run --name nginx -d -p 80:80 -v D:\env\docker\nginx\conf\default.conf:/etc/nginx/conf.d/default.conf nginx
```

### Sentinel

#### 下载

```bash
docker pull bladex/sentinel-dashboard
```

#### 运行

```bash
docker run --name sentinel -d -p 8858:8858 bladex/sentinel-dashboard
```

##### 访问路径

```bash
# 账号/密码  sentinel/sentinel
http://localhost:8858
```

