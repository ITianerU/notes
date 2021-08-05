

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

### 帮助

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

#### 提交镜像

发布到阿里云上 

- 登录阿里云
- 创建仓库 
- 在仓库列表点击管理按钮， 会有提交文档

```
sudo docker login --username=[用户手机号] registry.cn-beijing.aliyuncs.com
sudo docker tag [ImageId] registry.cn-beijing.aliyuncs.com/itianeru/centos:[镜像版本号]
sudo docker push registry.cn-beijing.aliyuncs.com/itianeru/centos:[镜像版本号]
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
  # 使用
  WORKDIR  $[变量名]
  ```

- ADD   将构建过程中要使用的压缩包, 复制进去, 并解压缩到镜像中指定的文件夹

- COPY    将构建过程中要使用的压缩包, 复制到镜像中指定的文件夹

- VOLUME   指定容器数据卷

- CMD   指定容器启动时, 要执行的命令, 多个CMD只有最后一个生效,  在终端使用docker run后面接的命令会替换掉       CMD命令

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

### 例子

#### mysql

- docker search mysql   # 查找

- docker pull mysql   # 下载

- docker images mysql   # 查看

- docker run   # 运行实例 

  - -p  3306:3306    # 端口映射
  -  --name mysql    # 容器命名

  -  -v /mysql/conf:/etc/mysql/conf.d   # 主机与容器共享配置文件
  -  -v /mysql/logs:/logs    # 主机与容器共享日志
  -  -v /mysql/data:/var/lib/mysql   # 主机与容器共享数据
  -  -e MYSQL_ROOT_PASSWORD=123456   # 初始化root用户密码
  -  -d mysql   # 后台运行

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

#### redis

- docker search redis   # 查找

- docker pull redis   # 下载

- docker images redis   # 查看

- docker run   # 运行实例 

  - -p  3306:3306    # 端口映射
  -  --name redis    # 容器命名

  -  -v /home/li/redis-data/data:/data   # 主机与容器共享数据
  -  -v /home/li/redis-data/conf/redis.conf:/usr/local/etc/redis/redis.conf    # 主机与容器共享配置文件
  -  -d redis   # 后台运行
  - redis-server /usr/local/etc/redis/redis.conf --appendonly yes   # 执行启动命令

```
docker run -p 6379:6379 --name redis -v /home/li/redis-data/data:/data -v /home/li/redis-data/conf/redis.conf:/usr/local/etc/redis/redis.conf  -d redis  redis-server /usr/local/etc/redis/redis.conf --appendonly yes --requirepass 123456
```

测试安装

- docker exec -it redis redis-cli