
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
- RibbonConfiguration @RibbonClient(name="  ",configuration=RibbonConfiguration.class)
### 使用属性自定义ribbon配置
- 支持的属性。配置的格式：<clientName>.ribbon.xx=xxx。
- 如 microservice-provider-user.ribbon.NFLoadBalancerRuleClassName=com.netflix.loadbalancer.RandomRule
 
### 脱离Eureka使用ribbon
- 在服务掉用方的application.yml中配置  服务提供方服务名.ribbon.listOfServers:localhost:8000,localhost:8001(服务方的地址)。
 
## 使用feign实现声明式的REST调用

### 为服务整合feign
- pom.xml中增加spring-cloud-starter-feign   创建一个接口并添加@FeignClient(name='')注解，name为任意一个客户端名称  新增controller调用接口中的方法，启动类中增加@EnableFeignClients注解
### 自定义Feign配置
- 和ribbon类似 参考默认配置，在FeignConfiguration类中定义相关配置 然后将其放到@FeignClient中的configuration
### 手动创建Feign
- 





