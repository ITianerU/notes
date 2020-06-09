## 启动docker容器

```bash
docker run -it --name rocketmq01 -p 9876:9876 -p 10911:10911 -p 11011:11011 rocketmqinc/rocketmq
docker run -it --name rocketmq02 -p 9877:9876 -p 10912:10911 -p 11012:11011 rocketmqinc/rocketmq
```

## 创建文件夹

1号机

```bash
mkdir /usr/local/rocketmq
mkdir /usr/local/rocketmq/store
mkdir /usr/local/rocketmq/store/broker-a
mkdir /usr/local/rocketmq/store/broker-a/commitlog
mkdir /usr/local/rocketmq/store/broker-a/consumequeue
mkdir /usr/local/rocketmq/store/broker-a/index
mkdir /usr/local/rocketmq/store/broker-b-s
mkdir /usr/local/rocketmq/store/broker-b-s/commitlog
mkdir /usr/local/rocketmq/store/broker-b-s/consumequeue
mkdir /usr/local/rocketmq/store/broker-b-s/index
```

2号机

```bash
mkdir /usr/local/rocketmq
mkdir /usr/local/rocketmq/store
mkdir /usr/local/rocketmq/store/broker-b
mkdir /usr/local/rocketmq/store/broker-b/commitlog
mkdir /usr/local/rocketmq/store/broker-b/consumequeue
mkdir /usr/local/rocketmq/store/broker-b/index
mkdir /usr/local/rocketmq/store/broker-a-s
mkdir /usr/local/rocketmq/store/broker-a-s/commitlog
mkdir /usr/local/rocketmq/store/broker-a-s/consumequeue
mkdir /usr/local/rocketmq/store/broker-a-s/index
```

## 修改配置文件

```bash
vi ../conf/2m-2s-sync/broker-a.properties
```

```properties
# broker-a

brokerClusterName=rocketmq-cluster
brokerName=broker-a
brokerId=0
namesrvAddr=172.17.0.2:9876;172.17.0.3:9876
defaultTopicQueueNums=4
autoCreateTopicEnable=true
autoCreateSubscriptionGroup=true
listenPort=10911
deleteWhen=04
fileReservedTime=120
mapedFileSizeCommitLog=1073741824
mapedFileSizeConsumeQueue=300000
diskMaxUsedSpaceRatio=88
storePathRootDir=/usr/local/rocketmq/store/broker-a
storePathCommitLog=/usr/local/rocketmq/store/broker-a/commitlog
storePathConsumeQueue=/usr/local/rocketmq/store/broker-a/consumequeue
storePathIndex=/usr/local/rocketmq/store/broker-a/index
storeCheckpoint=/usr/local/rocketmq/store/broker-a/checkpoint
abortFile=/usr/local/rocketmq/store/broker-a/abort
maxMessageSize=65536
brokerRole=SYNC_MASTER
flushDiskType=SYNC_FLUSH
```

```bash
vi ../conf/2m-2s-sync/broker-b-s.properties
```

```properties
# broker-b-s

brokerClusterName=rocketmq-cluster
brokerName=broker-b
brokerId=1
namesrvAddr=172.17.0.2:9876;172.17.0.3:9876
defaultTopicQueueNums=4
autoCreateTopicEnable=true
autoCreateSubscriptionGroup=true
listenPort=11011
deleteWhen=04
fileReservedTime=120
mapedFileSizeCommitLog=1073741824
mapedFileSizeConsumeQueue=300000
diskMaxUsedSpaceRatio=88
storePathRootDir=/usr/local/rocketmq/store/broker-b-s
storePathCommitLog=/usr/local/rocketmq/store/broker-b-s/commitlog
storePathConsumeQueue=/usr/local/rocketmq/store/broker-b-s/consumequeue
storePathIndex=/usr/local/rocketmq/store/broker-b-s/index
storeCheckpoint=/usr/local/rocketmq/store/broker-b-s/checkpoint
abortFile=/usr/local/rocketmq/store/broker-b-s/abort
maxMessageSize=65536
brokerRole=SLAVE
flushDiskType=ASYNC_FLUSH
```

```bash
vi ../conf/2m-2s-sync/broker-b.properties
```

```properties
# broker-b

brokerClusterName=rocketmq-cluster
brokerName=broker-b
brokerId=0
namesrvAddr=172.17.0.2:9876;172.17.0.3:9876
defaultTopicQueueNums=4
autoCreateTopicEnable=true
autoCreateSubscriptionGroup=true
listenPort=10911
deleteWhen=04
fileReservedTime=120
mapedFileSizeCommitLog=1073741824
mapedFileSizeConsumeQueue=300000
diskMaxUsedSpaceRatio=88
storePathRootDir=/usr/local/rocketmq/store/broker-b
storePathCommitLog=/usr/local/rocketmq/store/broker-b/commitlog
storePathConsumeQueue=/usr/local/rocketmq/store/broker-b/consumequeue
storePathIndex=/usr/local/rocketmq/store/broker-b/index
storeCheckpoint=/usr/local/rocketmq/store/broker-b/checkpoint
abortFile=/usr/local/rocketmq/store/broker-b/abort
maxMessageSize=65536
brokerRole=SYNC_MASTER
flushDiskType=SYNC_FLUSH
```

```bash
vi ../conf/2m-2s-sync/broker-a-s.properties
```

```properties
# broker-a-s

brokerClusterName=rocketmq-cluster
brokerName=broker-a
brokerId=1
namesrvAddr=172.17.0.2:9876;172.17.0.3:9876
defaultTopicQueueNums=4
autoCreateTopicEnable=true
autoCreateSubscriptionGroup=true
listenPort=11011
deleteWhen=04
fileReservedTime=120
mapedFileSizeCommitLog=1073741824
mapedFileSizeConsumeQueue=300000
diskMaxUsedSpaceRatio=88
storePathRootDir=/usr/local/rocketmq/store/broker-a-s
storePathCommitLog=/usr/local/rocketmq/store/broker-a-s/commitlog
storePathConsumeQueue=/usr/local/rocketmq/store/broker-a-s/consumequeue
storePathIndex=/usr/local/rocketmq/store/broker-a-s/index
storeCheckpoint=/usr/local/rocketmq/store/broker-a-s/checkpoint
abortFile=/usr/local/rocketmq/store/broker-a-s/abort
maxMessageSize=65536
brokerRole=SLAVE
flushDiskType=ASYNC_FLUSH
```

## 修改启动脚本

```bash
vi runbroker.sh
vi runserver.sh
# 修改
JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn128m"
```

## 启动

```bash
# A机
nohup sh mqnamesrv &
# B机
nohup sh mqnamesrv &
# A机
nohup sh mqbroker -c ../conf/2m-2s-sync/broker-a.properties &
# B机
nohup sh mqbroker -c ../conf/2m-2s-sync/broker-b.properties &
# A机
nohup sh mqbroker -c ../conf/2m-2s-sync/broker-b-s.properties &
# B机
nohup sh mqbroker -c ../conf/2m-2s-sync/broker-a-s.properties &
```

## 开放宿主机端口

```bash
firewall-cmd --zone=public --add-port=9876/tcp --permanent
firewall-cmd --zone=public --add-port=9877/tcp --permanent
firewall-cmd --zone=public --add-port=10911/tcp --permanent
firewall-cmd --zone=public --add-port=10912/tcp --permanent
firewall-cmd --zone=public --add-port=11011/tcp --permanent
firewall-cmd --zone=public --add-port=11012/tcp --permanent

firewall-cmd --reload
```

