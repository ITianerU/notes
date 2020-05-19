# nginx

Nginx是一个web服务器，可做反向代理， 需配合tomcat进行使用。

#### 启动

```
start nginx
```

#### 关闭

```
nginx -s stop
```

#### 重启

```
nginx -s reload
```

#### 配置文件

```
~ conf/nginx.conf

listen 80      # 修改端口号
root html;     # 页面存放在html路径下
index  index.html index.htm;      # 默认访问页面
```

#### 反向代理

```conf
~ conf/nginx.conf

location / {                                    # 表示处理所有请求
	proxy_pass http://127.0.0.1:8111;           # 表示把请求都交给http://127.0.0.1:8111来处理
}
```

#### 动静分离

Nginx对静态资源的吞吐量比tomcat大，所以对静态请求给Nginx处理，动态请求转给tomcat

```
~ conf/nginx.conf

location ~\.(css|js|png)$ { 							# 不替换反向代理的location
	root C:/Users/X7TI/Downloads/tomcat_8111/webapps/ROOT; # 表示项目所有的css js png访问都由nginx来做
}
```

#### 负载均衡

当访问量很大的时候，一个 Tomcat 吃不消了，这时候就准备多个 Tomcat，由Nginx按照权重来对请求进行分配，从而缓解单独一个Tomcat受到的压力

```
~ conf/nginx.conf

upstream tomcat_8111_8222{   # 名字随便 写在配置文件server{}的外面
    server  127.0.0.1:8111  weight=1; # 第一个tomcat的地址
    server  127.0.0.1:8222 weight=2; # wight表示权重，数值越大，被分配的请求越多
}

location / {   # 替换上面反向代理的配置
    proxy_pass http://tomcat_8111_8222; # 服务名为上面自定义的名称
}
```

#### session 共享

负载均衡导致，用户在tomcat1登录过的session，不会被tomcat2试别到，所以通过session共享解决该问题

解决方案：使用redis保存tomcat的session

```
将jedis-2.5.2.jar，commons-pool2-2.0.jar，tomcat-redis-session-manager1.2.jar 这三个jar包放到tomcat的lib目录下
每个toncat配置conf/context.xml
添加如下配置代码
<Valve className="com.orangefunction.tomcat.redissessions.RedisSessionHandlerValve" />  
<Manager className="com.orangefunction.tomcat.redissessions.RedisSessionManager"  
	host="127.0.0.1"  port="6379"  database="0"  maxInactiveInterval="60" /> 
```

