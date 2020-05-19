# nodejs

#### 安装

- 从下面给的网址下载安装包


```
https://nodejs.org/en/download/
```

- 解压到 /usr/local/文件夹下


创建链接

```
sudo ln -s /usr/local/node文件夹名/bin/node /usr/local/bin/
sudo ln -s /usr/local/node文件夹名/bin/npm  /usr/local/bin/
```

- 检验


```
node -v
npm -v
```

- 安装cnpm


```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

- 创建软链接


```
sudo ln -s /usr/local/node文件夹名/bin/cnpm /usr/local/bin/
```

# dirmngr

gpg

#### 安装

apt install dirmngr

# Mongodb

#### 安装

- 导入MongoDB公钥

```shell
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 9DA31620334BD75D9DCB49F368818C72E52529D4
```

- 创建MongoDB的软件源

```shell
echo "deb http://repo.mongodb.org/apt/debian stretch/mongodb-org/4.0 main" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.0.list
```

- 安装MongoDB

```shell
sudo apt-get update
sudo apt-get install -y mongodb-org
```

- 解决没有/home/mongodb目录的问题

```shell
sudo mkdir /home/mongodb
sudo chown -R mongodb:mongodb /home/mongodb
```

#### 使用

- 以可远程连接的方式启动

```
mongod --bind_ip 0.0.0.0 --auth
```

- 启动 MongoDB 服务

```shell
sudo systemctl start mongod 
```

- 查看MongoDB的日志文件

```shell
/var/log/mongodb/mongod.log
```

- 停止 MongoDB 服务

```shell
sudo systemctl stop mongod
```

- 重启 MongoDB 服务

```shell
sudo systemctl restart mongod
```

- 设置开机启动

```shell
sudo systemctl enable mongod
```

- 禁用开机启动

```shell
sudo systemctl disable mongod
```

- 使用

```
mongo
```

#### 删除

- 先停止 MongoDB 服务再删除

```shell
sudo apt-get purge mongodb-org*
```

- 删除数据目录.

```shell
udo rm -r /var/log/mongodb
sudo rm -r /var/lib/mongodb
```

#### 常用命令

- 创建用户

```shell
db.createUser({user: "admin", pwd: "123456", roles:[{role: "readWriteAnyDatabase", db: "admin"}]})
- 常用的role值记录: 
1. 数据库用户角色：read、readWrite; 
2. 数据库管理角色：dbAdmin、dbOwner、userAdmin； 
3. 集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager； 
4. 备份恢复角色：backup、restore； 
5. 所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase 
6. 超级用户角色：root 
7. 内部角色：__system 

- 相应的功能 
- Read：允许用户读取指定数据库 
- readWrite：允许用户读写指定数据库 
- dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile 
- userAdmin：允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户 
- clusterAdmin：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。 
- readAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读权限 
- readWriteAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读写权限 
- userAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限 
- dbAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限。 
- root：只在admin数据库中可用。超级账号，超级权限
```



# Git

#### 安装

```shell
apt-get install git
```

#### 配置

```shell
git config --global user.name "用户名"
git config --global user.email "邮箱"
```

#### 生成ssh公匙、私匙

```shell
ssh-keygen -t rsa 
```

#### 查看公匙

```
cd ~/.ssh
vim id_rsa.pub
```

#### 复制公匙到github上

# SVN

#### 安装

```shell
apt-get install subversion
```

#### 配置

```shell
删除~/.subversion/auth/下的文件
svn ls svn路径
```

# Anaconda

#### 安装

```shell
sh 安装包.sh
```

#### 创建软连接

```shell
sudo ln -s anaconda路径/bin/anaconda-navigator /usr/local/bin
```

#### 启动图形化界面

```shell
anaconda-navigator
```

# Docker

#### 安装

```shell
sudo apt install docker*
```

#### 查看版本

```shell
docker version
```

#### 添加用户权限

为了避免每次使用Docker命令时都需要切换到特权身份，可以将当前用户加入安装中自动创建的docker用户组

添加后, 重新登录生效

```shell
sudo usermod -aG docker 登录的用户名
```

#### 启动

```shell
sudo systemctl start docker
```

