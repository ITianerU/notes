# linux（centos）

#### 查看当前目录下文件及对应的文件属性

```shell
ls -l
```

#### 处理目录常用属性

```shell
ls: #列出目录 
	-a ：#全部的文件，连同隐藏档( 开头为 . 的文件) 一起列出来(常用)
    -d ：#仅列出目录本身，而不是列出目录内的文件数据(常用)
    -l ：#长数据串列出，包含文件的属性与权限等等数据；(常用)
cd：#切换目录
pwd：#显示目前的目录
mkdir：#创建一个新的目录
rmdir：#删除一个空的目录
cp: #复制文件或目录
rm: #移除文件或目录
mv: #移动文件与目录，或修改文件与目录的名称
```

#### 文件内容查看

```shell
cat  #由第一行开始显示文件内容
	-A ：#相当於 -vET 的整合选项，可列出一些特殊字符而不是空白而已；
    -b ：#列出行号，仅针对非空白行做行号显示，空白行不标行号！
    -E ：#将结尾的断行字节 $ 显示出来；
    -n ：#列印出行号，连同空白行也会有行号，与 -b 的选项不同；
    -T ：#将 [tab] 按键以 ^M 显示出来；
    -v ：#列出一些看不出来的特殊字符
tac  #从最后一行开始显示，可以看出 tac 是 cat 的倒著写！
nl   #显示的时候，顺道输出行号！
more #一页一页的显示文件内容
less #与 more 类似，但是比 more 更好的是，他可以往前翻页！
head #只看头几行
tail #只看尾巴几行
```

#### 文件内容搜索

```
/contont: 搜索内容
n: 下一个
N: 上一个
```



#### vim

```shell
vim filename
```

​	输入模式

```shell
i #进入输入模式
Esc #推出输入模式
```

​	一般模式

```shell
:w #保存
:wq #保存并退出
:q! #不保存强制退出
/word #向下查找word
?word #向上查找
:n1,n2s/word1/word2/g #在n1行到n2行将word1替换为word2
:%s/word1/word2/g #全局替换
```

#### yum

```shell
yum check-update #列出所有可更新的软件清单命令
yum update #更新所有软件命令
yum install <package_name> #仅安装指定的软件命令
yum list installed <package_name> #查看有没有安装过包
yum update <package_name> #仅更新指定的软件命令
yum list #列出所有可安裝的软件清单命令
yum remove <package_name> #删除软件包命令
yum search <keyword> #查找软件包命令
#清除缓存命令
yum clean packages: #清除缓存目录下的软件包
yum clean headers: #清除缓存目录下的 headers
yum clean oldheaders: #清除缓存目录下旧的 headers
yum clean, yum clean all (= yum clean packages; yum clean oldheaders) :#清除缓存目录下的软件包及旧的headers
```

#### 防火墙

```shell
systemctl status firewalld #查看防火墙状态 
systemctl start firewalld #开启
systemctl stop firewalld #关闭
systemctl restart firewalld #重启
firewall-cmd --list-ports #查看所有放开的端口
firewall-cmd --permanent --zone=public --add-port=端口/tcp #放开端口
```

#### 解压

```shell
tar -zxvf 目录文件名.tar.gz # 解压gz文件
```

#### 查看端口占用

```shell
netstat -tupln
```

#### 杀进程

```shell
kill -s 9 PID号
```

#### 修改权限

```shell
# 添加最高权限
chmod -R 777 文件夹名
```

#### 常用命令

```shell
# 查看内核版本
uname -a
# 查看系统详细信息
cat /proc/version
```



# Shell 

#### 首行

```shell
#!/bin/bash  #!是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行
```

#### 输出

```shell
echo 
```

#### 运行

```shell
chmod +x ./test.sh  #使脚本具有执行权限
./test.sh #运行脚本
```

```shell
sh test.sh #运行脚本
```

# mysql

#### 安装

```
官网查看最新安装包 https://dev.mysql.com/downloads/repo/yum/
执行命令下载 wget http://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
安装mysql源 yum -y install mysql57-community-release-el7-11.noarch.rpm
查看一下安装效果 yum repolist enabled | grep mysql.*
安装MySQL服务器 yum install mysql-community-server
```

#### 启动

```
启动MySQL服务 systemctl start  mysqld.service
运行一下命令查看一下运行状态 systemctl status mysqld.service
设置自动启动 systemctl enable mysqld 
		   systemctl daemon-reload
```

#### 设置密码

```
查看一下初始密码 grep "password" /var/log/mysqld.log
登录 mysql -uroot -p
修改密码 ALTER USER 'root'@'localhost' IDENTIFIED BY '****************'; *密码必须包含大小写字母特殊服务及数字
```

#### 设置远程连接权限

```
授权远程连接 GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '密码' WITH GRANT OPTION;
刷新权限 FLUSH PRIVILEGES;
```

#### 放开防火墙端口3306限制

```
firewall-cmd --permanent --zone=public --add-port=3306/tcp
之后重启防火墙
systemctl restart firewalld 
```

# JDK

#### 安装（rpm安装）

```
到如下地址 http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html 获取下载连接
下载链接获取方法，打开控制台，选择想要下载的版本，点击下载，查看network中的下载请求， *下载链接有时效性
执行下载命令 wget 下载连接
安装 rpm -ivh jdk-8u231-linux-x64.rpm 下载的包名
```

#### 配置环境变量

```
打开环境变量文件 vim /etc/profile 和 sudo vim /etc/bash.bashrc
添加如下配置
JAVA_HOME=/usr/java/安装的版本名
CLASSPATH=%JAVA_HOME%/lib:%JAVA_HOME%/jre/lib
PATH=$PATH:$JAVA_HOME/bin:$JAVA_HOME/jre/bin
export PATH CLASSPATH JAVA_HOME
重新载入 source /etc/profile 和 source /etc/bash.bashrc
```

#### 查看当前版本

```
java -version
```

#### 卸载

```
rpm -qa|grep jdk  #查看版本  
rpm -e jdk版本 
```

# Tomcat

#### 配置

```
修改bin目录下的catalina.sh
vim catalina.sh
添加两行
export JAVA_HOME=java安装目录
export JRE_HOME=java安装目录/jre
```

#### 启动/关闭

```shell
进入bin目录下
sh startup.sh #启动
sh shutdown.sh #关闭
如果遇到 cannot find catalina.sh， 执行如下命令
chmod +x *.sh #给sh文件授权，x为可执行权限
```

#### 查看日志

```
进入logs目录下
tail -1000f catalina.log # 查看最后1000行日志
```

#### 测试

```shell
进入webapps下
mkdir test #创建文件夹
vim index.html #创建index.html,内容随便填写
启动tomcat
在浏览器输入云服务器的公网ip:8080/test/index.html
```

# Nodejs

#### 安装

```
确认是否安装epel-release源
yum info epel-release
如果有信息显示则已安装，没有执行如下命令安装
yum install epel-release
安装nodejs
yum install nodejs
```

#### 查看版本

```
node -v
```

#### 升级版本

```
安装nodejs版本管理工具 n
npm install -g n
安装最新版本
n latest
```

#### 安装cnpm

```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

# python

#### 安装

```
yum install python36
```

#### 安装pip

```
curl -O https://bootstrap.pypa.io/get-pip.py
sudo /usr/bin/python3.6 get-pip.py
```

# Redis

#### 安装

```
yum install redis
```

#### 启动/停止/重启

```shell
systemctl start redis #启动
systemctl stop redis #停止
systemctl restart redis #重启
```

#### 查看redis状态

```shell
systemctl status redis
```

#### 查看redis进程

```
ps -ef | grep redis
```

#### 设置开机启动

```shell
systemctl enable redis
```

#### 开放端口

```
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --add-port=6379/tcp --permanent
之后重启防火墙
systemctl restart firewalld
```

#### 设置远程连接/添加密码

```
vim /etc/redis.conf
注释掉 bind 127.0.0.1
protected-mode yes 修改为 no
打开注释 requirepass 后面为密码
保存后重启
systemctl restart redis
```

#### 进入redis

```
输入 redis-cli -h 127.0.0.1 -p 6379
输入 info
输入 auth 密码
成功进入后再次输入 info
```

# Mongodb

#### 下载

```
进入 https://www.mongodb.com/download-center/community
OS 选择 RHEL 7.0
Package 选择 TGZ
复制出现的下载链接
使用命令行 wget 下载链接
```

#### 安装

```
解压
tar -zxvf 目录文件名.tar.gz /usr/local
```

#### 启动

```
mongod --bind_ip 0.0.0.0 --auth
```

#### 使用

```
mongo
```

#### 帮助

```
mongod -h
```

#### 关闭

```
mongod --shutdown
```

# lrzsz

远程传输文件

#### 安装

```shell
yum install lrzsz
```

