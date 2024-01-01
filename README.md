# Nacos配置

<br/>

**Nacos地址默认为localhost:8081，若不是则添加环境变量NACOS_CONFIG_SERVER**

或找到 src/main/resources/bootstrap.properties 添加

```
spring.cloud.nacos.config.server-addr=localhost:8081
```

<br/>

## 配置说明

<br/>

**服务端配置：maozi-cloud-monitor.yml**

```
application-port: 8000

spring:
  security:
    user:
      name: admin
      password: admin
      
  boot:
    admin:
      discovery: 
        services: maozi-cloud-*
      ui:
        external-views:
        
          - label: "注册中心"
            url: "http://localhost:8848/nacos/#/configurationManagement?dataId=&group=&appName=&namespace=&pageSize=&pageNo="
            iframe: true
            order: 2002

          - label: "接口文档"
            url: "https://localhost:10000/doc.html"
            iframe: true
            order: 2003

          - label: "容器管理"
            url: "http://localhost:9000"
            iframe: true
            order: 2004

          - label: "Arthas管理"
            url: "http://localhost:9080"
            iframe: true
            order: 2005
```

<br/>

**客户端配置：boot-monitor.yml**

```
spring:  
  security:
    user:
      name: admin
      password: admin
  boot: 
    admin: 
      discovery: 
        services: maozi-cloud-*
      client:
        # url: http://localhost:8000
        username: admin
        password: admin
        # auto-deregistration: true
        # instance: 
        #   prefer-ip: true
        #   metadata: 
        #     tags: 
        #       environment: 本地

endpoints:
  dubbo:
    enabled: true

management:
  info.env.enabled: true
  health:
    dubbo:
      status:
        defaults: memory
        extras: load,threadpool
  endpoints:
    web:
      exposure:
        include: '*'
  endpoint:
    dubbo:
      enabled: true
    dubbo-shutdown:
      enabled: true
    dubbo-configs:
      enabled: true
    dubbo-services:
      enabled: true
    dubbo-references:
      enabled: true
    dubbo-properties:
      enabled: true
    health:
      show-details: ALWAYS
```

<br/>

**参数说明：https://codecentric.github.io/spring-boot-admin/2.6.9/**

<br/>

<br/>

# 即可启动

**访问localhost:8000即可进入监控面板**

<br/>

<br/>

<br/>

# 扩展Arthas

<br/>

编写docker-compose.yml

```yaml
version: '3'
services:
  arthas-tunnel-server:
    environment:
      # 开放管理页面有风险！管理页面没有安全拦截功能，务必自行增加安全措施
      PARAMS: '--arthas.enable-detail-pages=true --spring.cache.type=redis --arthas.embedded-redis.enabled=true --spring.redis.host=localhost --spring.redis.port=6379 --spring.redis.password=812840531zhang'  
    restart: always
    image: registry.cn-shanghai.aliyuncs.com/wanfei/arthas-tunnel-server:v1
    container_name: arthas-tunnel-server
    ports:
      - 9080:8080
      - 7777:7777
```

**将PARAMS里的Redis参数改成自己的**

执行docker-compose up -d即可

<br/>

## Arthas Nacos配置

**boot-arthas.yml**

```
arthas: 
  agent-id: ${spring.application.name}
  # Arthas地址
  tunnel-server: ws://localhost:7777/ws
  telnet-port: -1
  http-port: -1
```