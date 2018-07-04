
## Eureka的相关知识点
 只是做大纲，总结相关的知识点
### 编写单机的EurekaService
- eureka.client.registerWithEureka 表示是否将自己注册到Eureka Service
- eureka.client.fetchRegister  表示是否从Eureka service 获取注册信息 
- eureka.client.serviceUrl.dafaultZone 设置与Eureka service进行交互的地址查询和注册服务都依赖这个地址
- @EnableEurekaServicce @EnableDiscoveryClient @EnableEurekaClient   中EnableEurekaClient只能和eureka一起使用  EnableDiscoveryClient可以和eureka，zookeeper，consul一起使用
###  Eureka service 的高可用
- eureka.instancce.prefer-ip-address 表示将自己的ip注册到eureka service上 如果为false则是将操作系统的hostname注册到eureka service
### 为Eureka service 增加用户认证
- pom.xml中增加spring-boot-starter-security 
- security.basic.enable 是否开启http basic的认证
- security.user.name security.user.password 配置登陆的用户名和密码
- 将服务注册到需要认证的Eureka Service 需要将eureka.client.serviceUrl.dafaultZone配置为http://user:password@Eureka_host:Eureka_port/eureka
### Eureka 的元数据
- 标准元数据和自定义元数据  eureka.intance.metadata-map[]=   以key/value的形式来操作
- 客户端可以通过discorveryClient.getInstance(服务名) 来获取元数据
### Eureka service 的rest端点
- 其他语言可用利用eureka service的rest端点 通过xml/json进行通信 编写 Eurekaservice 的client
### Eureka service 的自我保护模式
- eureka.server.enable-self-preservation 来开启和关闭自我保护模式 
### 多网卡环境下的ip选择
### Eureka 的健康检查
- eureka.client.healthcheck.enable=ture  开启健康检查

## ribbon实现客户端侧负载均衡
### 为服务消费者整合ribbon
- @loadBalance 
### 使用java代码自定义ribbon配置


