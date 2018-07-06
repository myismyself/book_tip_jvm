
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
## 使用hystrix实现微服务的容错处理

### 实现容错的手段
- 雪崩效应  基础服务故障导致级联故障
- 如何容错   为网络请求设置超时，使用断路器模式（请求失败达到一定阈值，打开断路器，打开一段时间就半开状态，允许一个请求进去成功就关闭短路器）
### hystrix实现延迟和容错
- 包裹请求，跳闸机制，资源隔离，监控，回退机制，自我修复
### 通用方式整合hystrix
- 启动类增加@EnableCircuitBreak或@EnableHystrix
- 给controller中调用服务的方法增加@HystrixCommand(fallbackMethod = "findUserFallBack",
commandProperties = { @HystrixProperty(name="execution.isolation.thread.timeoutInMilliseconds",value = "5000"),   @HystrixProperty(name="metrics.rollingStats.timeInMilliseconds",value = "10000")},
 threadPoolProperties = {@HystrixProperty(name="coreSize",value = "1"),@HystrixProperty(name="maxQueueSize",value = "10")})，findUserFallBack为请求被拒绝，失败，超时或者断路器打开就调用的自定义方法。
 ### hystrix线程隔离策略与传播上下文
 - hystrix有两种隔离策略：线程隔离和信号量隔离
 - 使用在commandProperties中配置@HystrixProperty(name="execution.isolation.strategy",value = "THREAD/SEMAPHORE") 来实现线程隔离策略的选择
 - 当调用负载量特别高时才用SEMAPHORE
 ### 在feign中使用hystrix
 - springcloud 默认feign整合了hystrix，只要hystrix在项目的classpath中feign默认就会用断路器包裹的所有方法
 -  在feign的接口UserFeiginClient上@FeignClient（name="microservice-provider-user",fallback="FeiginClientFallBack.class"）
 - 回退类FeiginClientFallBack需要实现FeignClientj接口
 - 在在feign的接口上直接@Component   class FeiginClientFallBack implements UserFeiginClient 实现相应的方法
 ### 通过Fallback Factory检查回退的原因
 -  在feign的接口UserFeiginClient上@FeignClient（name="microservice-provider-user",fallbackFactory="FeiginClientFallBackFactory.class"）
 -  在feign的接口UserFeiginClient上直接@Component   class FeiginClientFallBackFactory implements FallBackFactory<UserFeiginClient> 实现相应的方法
 
### feign禁用hystrix
- 借助feign的自定义配置，可以轻松为指定名称的feign客户端禁用hystrix 
- ![feign禁用hystrix的操作](https://github.com/myismyself/book_tip_jvm/blob/master/springcloud_img/1530783939(1).png)
- 全局禁用hystrix则在application.yml中配置feign.hystrix.enabled = false即可
### hystrix的监控
- hystrix的hystrix-metrics-event-stream就可以将这些监控信息以text/event-stream的格式暴露给外部系统。
- spring-cloud-starter-hystrix已经包含了此模块，结合actuator就可以以http://hostname:port/hystrix.stream 端点获得hystrix的监控信息。
### feign项目的hystrix监控
- 项目中引入spring-cloud-starter-hystrix  启动类增加@EnableCricuitBreaker  就可以通过/ystrix.stream获得监控信息
###  使用hystrix Dashboard可视化监控数据
- 引入spring-cloud-starter-hysterix-dashboard ,在启动类中增加@EnableHystrixDashboard
- dashboard只能实现对单个实例的监控
### 使用turbine实现聚合监控
将所有hystrix.stream的端点数据聚合到turbine.stream上
#### 使用turbine监控多个微服务
- 在消费端引入spring-cloud-starter-turbine,并在其启动类上增加@EnableTurbing，并在application.yml中增加turbine.appConfig:服务名，服务名  turbine.clusterNameExpress="'default'"
这样turbine就会在eureka service中招到上面appConfig配置的服务名服务，并聚合两个服务的监控数据，可通过/turbine.stream获取聚合的监控数据
### 使用消息中间件收集数据
- 安装rabbitMQ依赖ERlang，再安装RibbitMQ-server
- 再到RibbitMQ的sbin目录执行ribbitmq-plugins enable ribbitmq-management，就可以访问localhost：15672进入ribbit的web页面
- 具体查看mq改造微服务




