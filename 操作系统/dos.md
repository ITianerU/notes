# 常用命令

## 目录操作

### 创建文件夹

```
md 文件夹名称
```

### 切换文件夹

```bash
C:
```

### 查看所有目录

```bash
dir
```

### 切换目录

```bash
cd 目录名
# 跨盘符切换
cd /d D:目录名
```

### 删除目录

```
rd 目录名
```



## 文件操作

### 创建文件

```bash
cd>文件名
```

### 删除文件

```bash
del 文件名
```

## 终端

### 清空屏幕

```bash
cls
```

### 退出终端

```bash
exit
```

## 网络

### 查看ip

```bash
ipconfig
```

### 测试ip

```bash
ping ip地址
```

### 配置静态路由 

- route -p  add 0.0.0.0 mask 255.255.255.0 192.168.3.1

- route -p  add 0.0.0.0 mask 255.255.0.0 192.168.3.1

  

- route -p  add 192.168.20.0 mask 255.255.255.0 192.168.3.1
  route -p  add 192.168.3.0 mask 255.255.255.0 192.168.253.105

## 打开软件

### 计算器

```bash
calc
```

### 画图工具

```
mspaint
```

### 记事本

```sh
notepad
```

## telnet

win10默认没有启动telnet

**启动步骤:**  控制面板->程序->启用或关闭Windows功能->Telnet Clinet

### 命令

#### 连接服务

```sh
telnet ip 端口号   # 连接
# 按 ctrl + ] 进入telnet模式
```

#### 发送消息

```shell
send 消息
```



## 其他

### 修复虚拟机

部分情况, 可能导致虚拟器无法启动的情况, 后台显示一直被占用, 使用如下命令

```
netsh winsock reset
```

