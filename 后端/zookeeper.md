# 客户端

## 命令

```bash
# 在bin目录下, 有zookeeper的客户端连接工具
./zkCLi.sh
# 查看注册进来的服务
# 客户端连接后使用
ls /services
# 查询某微服务的全部实例id
ls /services/[微服务名]
# 查看某微服务某一实例详情
get /services/[微服务名]/[实例id]
```

