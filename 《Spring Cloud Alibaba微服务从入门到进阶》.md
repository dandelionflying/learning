

[TOC]



doc:

https://docs.spring.io/spring-boot/docs/2.5.x/reference/htmlsingle/

boot官方依赖格式 <spring-boot-starter-xxx>

非官方依赖格式	<xxx-spring-boot-starter>

依赖查找：

https://docs.spring.io/spring-boot/docs/2.5.x/reference/htmlsingle/#using.build-systems.starters

环境：

​	springcloudalibaba--2.2.6.RC1

​	springcloud--Hoxton.SR3

​	sentinel-dashboard-1.8.2.jar

​	nacos-server-1.3.1

# 一、springboot Actuator

应用监控

> Spring Boot includes a number of additional features to help you monitor and manage your application when you push it to production. You can choose to manage and monitor your application by using HTTP endpoints or with JMX. Auditing, health, and metrics gathering can also be automatically applied to your application.

## （1）基本使用：

- 依赖：

```xml
spring-boot-starter-actuator
```

- 展示所有端点

http://localhost:8080/actuator

- 常用端点：

  **1）/health**

  Shows application health information.关于应用的健康信息

  显示详细信息：

  ```properties
  management.endpoint.health.show-details=always
  ```

  

<img src="pictures\actuator-health-detail.png">

​				status取值

​					up：正常

​					down：不正常

​					out_of_service：资源未在使用

​					unknown

## 		**2）/info**

​		描述应用：以键值对形式配置

<img src="pictures\actuator-info.png" style="zoom:100%;" />



- 端点激活

  ```properties
  #激活所有端点 默认只有health(2.5版本)
  management.endpoints.web.exposure.include=*
  ```

# 二、springboot配置管理

## （1）配置管理常用方式

- 配置文件

- 环境变量

  eg:

  ```properties
  management.endpoints.web.exposure.include=${XXX}
  ```

  1）在idea 的Run/Debug Configuration中配置环境变量（Environment Variables）

  2）以jar方式启动时，在jar命令后带上参数 --XXX=xxx

  eg:

  ```
  java -jar xxxxxx.jar --XXX=xxx
  ```

- 外部配置文件

  ```
  java -jar xxxxxx.jar 
  ```

  jar包相同路径下的配置文件可以被直接读取，并且比jar中的配置文件优先级高

- 命令行参数

  1）在idea 的Run/Debug Configuration中Program 配置

  2）jar方式启动

  ```
  java -jar xxxxxx.jar --server.port=8888
  ```

## （2）profile

​	用于不同环境使用不同的配置，可在idea的Run/Debug Active profile 中配置

​	jar包形式使用方式：

```yaml
#连字符
---
# profile=x的专用属性，也就是某个环境下的专用属性 比如这是开发环境
# @deprecated since 2.4.0 for removal in 2.6.0 in favor of  原方式spring.profiles已废弃 2.5.x版本不支持
profiles: dev

---
# profile=y的专用属性，也就是某个环境下的专用属性 比如这是生产环境
profiles: prod
# eg:
server:
  tomcat:
    threads:
      max: 400

```

> 配置文件为properties时，不同环境的配置写在不同的文件里，文件格式为 application-xxx.properties

# 三、微服务拆分

（1）方法论:

- 领域驱动设计（**D**omain **D**riven **D**esign）

  对要解决的问题进行建模，拆分子领域，解决不同问题

- 面向对象 状态、行为

- 

（2）合理的粒度

​	良好的满足业务

​	团队工作压力

​	要考虑增量迭代：是否较容易地进行迭代，依次迭代涉及的微服务保持在相对合理的数量

​	持续进化：即，单个微服务进行优化是否可控，粒度太粗优化相对困难

（3）微服务拆分是动态的，随着开发的进行，适当地拆分、合并

# 四、springboot RestTemplate

（1）一个轻量级的http客户端，用于微服务之间的请求调用。

（2）基本使用

```java
// 二者区别：getForEntity可以拿到http响应体的内容，而getForObject只是拿到响应体数据
ResponseEntity<String> forEntity = restTemplate.getForEntity("http:host:port/api/{param1}", String.class);
restTemplate.getForEntity("http:host:port/api/{param1}",String.class, 2);

String forObject = restTemplate.getForObject("http:host:port/api/{param1}", String.class);
restTemplate.getForObject("http:host:port/api/{param1}",String.class, 2);
```

# 六、整合Spring Cloud Alibaba

```xml
<dependencyManagement>
    <dependencies>
        <!--整合springcloud-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Hoxton.SR3</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <!--整合springcloud alibaba-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>2.2.6.RC1</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <!--整合完毕后需要使用springcloudalibaba 的子项目时，不需要指定版本-->
    </dependencies>
</dependencyManagement>
```

> 最好使用官网推荐的版本搭配：
> https://github.com/alibaba/spring-cloud-alibaba/wiki/%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E

# 七、nacos

## （1）安装运行

去官网下载nacos的server和client，版本选择可点击`<artifactId>spring-cloud-alibaba-dependencies</artifactId>`根据里面的nacos版本号选择相应的版本下载使用

控制台地址 http://192.168.217.1:8848/nacos/index.html

## （2）服务注册

引入依赖：

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <!--            <groupId>org.springframework.cloud</groupId>-->
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    <version>2.2.5.RELEASE</version>
</dependency>
```

**一定保证该版本是与你当前项目的springboot版本自己springCloud版本是兼容的，按照官方推荐版本搭配。**

配置server地址

```yaml
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
```

> 引入com.alibaba.cloud包的依赖，不需要使用@EnableDiscoveryClient

## （3）元数据

提供描述信息

灵活控制微服务调用 如微服务版本控制

<img src="pictures\元数据.png" style="zoom:75%;" />

```yaml
  cloud:
    nacos:
      discovery:
        #        cluster-name:
        #        namespace:
        #元数据配置
#        metadata:
#          key1: value1
#          key2: value2
```

# 八、服务间通信

## （1）原始方式（低级，不使用）：

​		通过DiscoveryClient获取服务名关联的实例`client.getInstances("basis2")`，拼接后使用restTemplate发送请求`restTemplate.getForObject(url,SomeClass.class)`。

## （2）引入负载均衡：

​		声明RestTemplate Bean时，加上**@LoadBalanced**注解，就可以直接用服务名称进行服务间通信，不需要再去获取hostport等信息`restTemplate.getForObject("http://"+ ServiceList.basis2 + "/basis2/findBasis2", String.class)`。()

## （3）feign方式

- 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

- 统一接口管理

```java
/**
 * @author running4light
 * @description 统一接口管理：定义某个微服务的所有接口
 * @createTime 2021/7/12 16:45
 */
@FeignClient(value = "basis2")
public interface Basis2Web {
    @GetMapping("/basis2/findBasis2")
    String findBasis2();
}
```

- 调用

```java
// 自动装配接口
@Autowired
private Basis2Web basis2Web;

// 直接调用
@GetMapping("feignTestGet")
public String feignTestGet(){
    return basis2Web.findBasis2();
}
```

- 报错处理

​		<img src="pictures\feign服务调用报错1.png" style="zoom:75%;" />

> ​		接口方法需写全路径 “**controller+method**”



<img src="pictures\feign服务调用报错2.png" style="zoom:75%;" />

# 九、ribbon--基于客户端的负载均衡

> doc：
>
> 英文：https://cloud.spring.io/spring-cloud-netflix/multi/multi_spring-cloud-ribbon.html
>
> 中文：https://www.springcloud.cc/spring-cloud-greenwich.html#spring-cloud-ribbon

## **（1）springCloudNetfix为ribbon提供的beans：**

<img src="pictures\RibbonBeans.png" style="zoom:75%;" />

IclientConfig：读取配置

IRule：负载均衡规则

IPing：筛选实例

ServerList<Server>：服务实例列表

ServerListFilter<Server>：服务实例过滤器

ILoadBalancer：Ribbon 的入口

ServerListUpdater：更新策略

## **（2）IRule**



<img src="pictures\Ribbon-IRule继承关系图.png" style="zoom:75%;" />

- RoundRobinRule：继承AbstractLoadBalancerRule

  > The most well known and basic load balancing strategy, i.e. Round Robin Rule.
  >
  > 最著名和基本的负载平衡策略，即轮循规则。

  RoundRobinRule的choose方法：轮询找到可用的server返回。

- WeightedResponseTimeRule：继承自RoundRobinRule

- ClientConfigEnabledRoundRobinRule：继承AbstractLoadBalancerRule，本质上是在使用roundRobinRule。

- PredicateBasedRule：继承自ClientConfigEnabledRoundRobinRule，重写了choose方法，将server过滤逻辑交给AbstractServerPredicate，过滤后，以循环方式选择server返回。

server的选择逻辑：

```java
/**
 * Referenced from RoundRobinRule
 * Inspired by the implementation of {@link AtomicInteger#incrementAndGet()}.
 *
 * @param modulo The modulo to bound the value of the counter.
 * @return The next value.
 */
private int incrementAndGetModulo(int modulo) {
    for (;;) {
        int current = nextIndex.get();
        int next = (current + 1) % modulo;
        if (nextIndex.compareAndSet(current, next) && current < modulo)
            return current;
    }
}
```

AvailabilityFilteringRule：继承自PredicateBasedRule，

ZoneAvoidanceRule：继承自PredicateBasedRule

BestAvailableRule：继承自ClientConfigEnabledRoundRobinRule

RandomRule：继承AbstractLoadBalancerRule

RetryRule：继承AbstractLoadBalancerRule

## **（3）自定义配置**

- java配置类

```java
/**
 * @author running4light
 * @description 指定ribbonClient配置类
 * @createTime 2021/7/13 14:18
 */
@Configuration
@RibbonClient(name = "basis2", configuration = RibbonConfiguration.class)
public class Basis2RibbonConfiguration {

}
/**
 * @author running4light
 * @description 自定义配置
 * @createTime 2021/7/13 14:20
 */
@Configuration
public class RibbonConfiguration {
    @Bean
    public IRule ribbonRule(){
        return new RandomRule();
    }
}
```

- yml配置属性的方式（**优先级高于java配置类**）

  ```yml
  basis2:
    ribbon:
      NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule
      NFLoadBalancerClassName: ILoadBalancer实现类
      NFLoadBalancerPingClassName: IPing实现类
      NIWSServerListClassName: ServerList实现类
      NIWSServerListFilterClassName: ServerListFilter实现类
  ```

  格式：

  服务名.ribbon.XXClassName=包路径+你的规则类

  其中XX可为 `NFLoadBalancer` `NFLoadBalancerRule` `NFLoadBalancerPing`  `NIWSServerList` `NIWSServerListFilter`

- 使用：RestTemplate+@LoadBalanced

  link:    http://localhost:8001/basisTest/serviceGet

- 一个父子上下文的问题：该配置类所处包路径，不能被@SpringbootApplication注解或@ComponentScan注解所扫描的路径所包含。

)

<img src="pictures\RibbonConfiguration.png" style="zoom:100%;" />

​	会导致所有的RibbonCLients都使用该规则。

​	官方文档给出了警告：

> The `CustomConfiguration` clas must be a `@Configuration` class, but take care that it is not in a `@ComponentScan` for the main application context. Otherwise, it is shared by all the `@RibbonClients`. If you use `@ComponentScan` (or `@SpringBootApplication`), you need to take steps to avoid it being included (for instance, you can put it in a separate, non-overlapping package or specify the packages to scan explicitly in the `@ComponentScan`).

- 全局配置：

```java
@RibbonClients(defaultConfiguration = XXX.class)
```

- 饥饿加载--防止第一次请求过慢

  ```yaml
  ribbon:
    eager-load:
      enabled: true
      clients: basis2
  ```

## **（4）Ribbon拓展**



# 十、声明式Http客户端--Feign



基础使用方式见 服务间通信--feign方式

## （1）Feign日志级别配置

> NONE（default）：不记录
>
> BASIC：请求方法、URL响应状态码及执行时间
>
> HEADERS：BASIC+RequestHeader+ResponseHeader
>
> FULL：请求、响应 的 header、body、元数据

- java方式

```java
@FeignClient(value = "basis2", configuration = Basis2FeignConfiguration.class)
public interface Basis2Web {
    @GetMapping("/basis2/findBasis2")
    String findBasis2();
}
```

```java
public class Basis2FeignConfiguration {
    @Bean
    public Logger.Level level(){
        return Logger.Level.FULL;
    }
}
```



```yaml
logging:
  level:
#    feign日志级别建立在feign的接口的日志级别是在debug只上的,如果是info级别 feign日志级别设置不会生效
    cn.running4light.basis.web.feigns.Basis2Web: debug
#    cn.running4light.basis.web.feigns.Basis2Web: info
```

- yaml方式

**失效，暂未解决**



```yaml
feign:
  client:
    config:
      basis2:
        loggerLever: full
```

## （2）Feign可配置项

在Feign.class中可以查找：

<img src="pictures\Feign可配置项.png">

## **（3）feign性能优化**（未测试）

- 连接池

  依赖

  ```xml
  <dependency>
      <groupId>io.github.openfeign</groupId>
      <artifactId>feign-httpclient</artifactId>
  </dependency>
  ```

  配置

  ```yaml
  feign:
    httpclient:
      enabled: true
      # max-connections max-connections-per-route 的具体值需根据实际项目压测结果确定相对最优配置
      # feign 最大连接池数
      max-connections: 200
      # feign 单个路径的最大连接数
      max-connections-per-route: 50
  ```

  > **max-connections max-connections-per-route 的具体值需根据实际项目压测结果确定相对最优配置**

- feign日志级别不设置为full

# 十一、服务容错--Sentinel

## （1）常见容错方案

- 超时

- 限流

- 仓壁模式

- 断路器模式

  https://martinfowler.com/bliki/CircuitBreaker.html

## （2）getstarted



- 依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

- 配置：

```yaml
# 配置actuator
management:
  #激活所有端点 默认只有health
  endpoints:
    web:
      exposure:
        include: "*"
```



- sentinel控制台

  下载（jar包版本最好跟依赖包中的版本一致）

  https://github.com/alibaba/Sentinel/releases

  运行

  `java -jar sentinel-dashboard-1.8.2.jar`

- 项目整合控制台

```yaml
spring:
  cloud:
    sentinel:
      transport:
        # 指定sentinel 控制台地址
        dashboard: 8080
        port: 8719
```

## （3）流控

- 流控模式

1. 直接
2. 关联--对A设置关联到B，B达到阈值时，限流A
3. 链路--只记录指定链路上的流量

<img src="pictures\sentinel控制台--流控1.png" style="zoom:80%">

- 流控效果

1. 快速失败

2. warm up

   根据codeFactor（默认3），从阈值/codeFactor，经过预热时长，才达到设置的QPS阈值

   > https://github.com/alibaba/Sentinel/wiki/限流---预热

3. 排队等待

   让请求以均匀的速度通过，阈值类型必须是QPS



<img src="pictures\sentinel控制台--流控2.png" style="zoom:80%">

## （4）熔断降级

> https://github.com/alibaba/Sentinel/wiki/%E7%86%94%E6%96%AD%E9%99%8D%E7%BA%A7

**源码位置：com.alibaba.csp.sentinel.slots.block.degrade.DegradeRule.class**

- 慢调用比例

  选择以慢调用比例作为阈值，需要设置允许的慢调用 RT（即**最大的响应时间**），请求的响应时间大于该值则统计为慢调用。当**单位统计时长**（`statIntervalMs`）内请求数目大于设置的**最小请求数**目，并且慢调用的比例大于**阈值**，则接下来的**熔断时长**内请求会自动被熔断。经过熔断时长后熔断器会进入**探测恢复状态**（HALF-OPEN 状态），若接下来的一个请求响应时间小于设置的慢调用 RT 则结束熔断，若大于设置的慢调用 RT 则会再次被熔断。

> 几个关键词：最大响应时间、单位统计时长、最小请求数、阈值（慢调用比例）、熔断时长

eg：当3000ms内请求数大于3，且慢调用比例大于0.2，则接下来2s内请求会自动被熔断，2s后熔断器进入探测恢复状态。

<img src="pictures\sentinel控制台--熔断1.png">

- 异常比例

  当单位统计时长（`statIntervalMs`）内请求数目大于设置的最小请求数目，并且异常的比例大于阈值，则接下来的熔断时长内请求会自动被熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求成功完成（没有错误）则结束熔断，否则会再次被熔断。异常比率的阈值范围是 `[0.0, 1.0]`，代表 0% - 100%。

  eg：当2000ms内请求数大于3，且异常比例大于0.6，则接下来2s内请求会自动被熔断，2s后熔断器进入探测恢复状态。

  <img src="pictures\sentinel控制台--熔断2.png">

- 异常数

  当单位统计时长内的异常数目超过阈值之后会自动进行熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求成功完成（没有错误）则结束熔断，否则会再次被熔断。

  eg：当3000ms内请求数大于10，且异常数大于4，则接下来2s内请求会自动被熔断，2s后熔断器进入探测恢复状态。

  <img src="pictures\sentinel控制台--熔断3.png">



## （5）热点参数限流

> https://github.com/alibaba/Sentinel/wiki/%E7%83%AD%E7%82%B9%E5%8F%82%E6%95%B0%E9%99%90%E6%B5%81

**源码位置：com.alibaba.csp.sentinel.slots.block.flow.param.ParamFlowChecker**

- 引入依赖

```xml
<!--热点参数限流-->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-parameter-flow-control</artifactId>
</dependency>
```

- 在需要进行热点参数限流配置的接口上添加注解

```java
@GetMapping("hotTest")
@SentinelResource("hotTest")
public String hotTest(@RequestParam(required = false) String a, @RequestParam(required = false) String b){
    return a + " " + b;
}
```

<img src="pictures\sentinel控制台--热点规则1.png">





## （6）授权规则--系统自适应限流

> https://github.com/alibaba/Sentinel/wiki/%E7%B3%BB%E7%BB%9F%E8%87%AA%E9%80%82%E5%BA%94%E9%99%90%E6%B5%81

源码位置：com.alibaba.csp.sentinel.slots.system.SystemRuleManager

“Sentinel 系统自适应限流从整体维度对应用入口流量进行控制，结合应用的 Load、CPU 使用率、总体平均 RT、入口 QPS 和并发线程数等几个维度的监控指标，通过自适应的流控策略，让系统的入口流量和系统的负载达到一个平衡，让系统尽可能跑在最大吞吐量的同时保证系统整体的稳定性。”

“Sentinel 在系统自适应保护的做法是，用 load1 作为启动自适应保护的因子，而允许通过的流量由处理请求的能力，即请求的响应时间以及当前系统正在处理的请求速率来决定。”

<img src="pictures\sentinel控制台--系统规则1.png">

## （7）授权规则--黑白名单控制

> https://github.com/alibaba/Sentinel/wiki/%E9%BB%91%E7%99%BD%E5%90%8D%E5%8D%95%E6%8E%A7%E5%88%B6

<img src="pictures\sentinel控制台--授权规则1.png">

## （8）代码方式配置规则（待整理）



## （9）@SentinelResource注解

源码：com.alibaba.csp.sentinel.annotation.aspectjSentinelResourceAspect  **（待看）**

```java
    @GetMapping("testSentinelAPI")
    @SentinelResource(
            value = "testSentinelAPI",
            blockHandler = "block",
//            blockHandlerClass =
//            fallbackClass =
            fallback = "fallback"
    )
    public String testSentinelAPI(@RequestParam(required = false) String a){
        if(StringUtils.isBlank(a))
            throw new IllegalArgumentException("a is null");
        return a;
    }
    /**
     * @Description 处理限流或降级
     * @Author running4light朱泽雄
     * @CreateTime 10:24 2021/7/20
     * @Return
     */
    public String block(String a, BlockException e){
        log.warn("blocking...", e);
        return "blocking...";
    }
    /**
     * @Description 处理降级
     * @Author running4light朱泽雄
     * @CreateTime 10:24 2021/7/20
     * @Return
     */
    public String fallback(String a){
        log.warn("fallback...");
        return "fallback...";
    }
```

## （10）RestTemplate整合Sentinel

注入bean时添加@SentinelRestTemplate

yaml配置开关：resttemplate.sentinel.enabled

源码：com.alibaba.cloud.sentinel.custom.SentinelBeanPostProcessor    **（待看）**

## （11）Feign整合Sentinel

源码：com.alibaba.cloud.sentinel.feign.SentinelFeign   **（待看）**

```yaml
feign:
  sentinel:
    enabled: true
```

获取异常：

```java
@FeignClient(value = "basis2",
//    fallback = XXX.class,
    fallbackFactory = Basis2FeignClientFallBackFactory.class
)
```

```java
@Component
public class Basis2FeignClientFallBackFactory implements FallbackFactory<Basis2Web> {
    private Logger logger = LoggerFactory.getLogger(Basis2FeignClientFallBackFactory.class);
    @Override
    public Basis2Web create(Throwable cause) {

        return new Basis2Web() {
            @Override
            public String findBasis2() {
                logger.warn("发生限流", cause);
                return "发生限流";
            }
        };
    }
}
```

## （12）Sentinel规则持久化--动态规则

“我们推荐**通过控制台设置规则后将规则推送到统一的规则中心，客户端实现** `ReadableDataSource` **接口端监听规则中心实时获取变更**”

> https://github.com/alibaba/Sentinel/wiki/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%99%E6%89%A9%E5%B1%95

<img src="pictures\sentinel--动态规则.png" style="zoom:75%;">

- 拉模式（Pull-based:）：客户端主动向规则中心拉取规则，规则中心可以是RDBMS/文件/VCS等。
  - 缺点：无法及时获取变更
  - 使用：继承 `AutoRefreshDataSource`,实现 readSource()



- 推模式（Push-based:）:规则中心推送，客户端监听获取
  
- 使用：继承 `AbstractDataSource`，在构造方法中添加监听器，并实现readSource()从指定数据源读取string格式的配置数据。
  
- 注册数据源

  - eg:

    ```java
    package com.test.init;
    
    public class DataSourceInitFunc implements InitFunc {
    
        @Override
        public void init() throws Exception {
            final String remoteAddress = "localhost";
            final String groupId = "Sentinel:Demo";
            final String dataId = "com.alibaba.csp.sentinel.demo.flow.rule";
    
            ReadableDataSource<String, List<FlowRule>> flowRuleDataSource = new NacosDataSource<>(remoteAddress, groupId, dataId,
                source -> JSON.parseObject(source, new TypeReference<List<FlowRule>>() {}));
            FlowRuleManager.register2Property(flowRuleDataSource.getProperty());
        }
    }
    ```

  - 接着将对应的类名添加到位于资源目录（通常是 `resource` 目录）下的 `META-INF/services` 目录下的 `com.alibaba.csp.sentinel.init.InitFunc` 文件中,如

    ```properties
    cn.running4light.basis.init.DataSourceInitFunc
    ```

- 示例

  - 拉模式

    **规则存储路径需提前建好**

    依赖

    ```xml
  <dependency>
        <groupId>com.alibaba.csp</groupId>
        <artifactId>sentinel-datasource-extension</artifactId>
    </dependency>
    ```
  
    https://github.com/dandelionflying/SpringCLoudAlibaba/tree/master/basis/src/main/java/cn/running4light/basis/init/FileDataSourceInitFunc.java
  
    注册规则数据源：存在文件中
  
    ```java
    
    public class FileDataSourceInitFunc implements InitFunc {
        @Override
        public void init() throws Exception {
    //        String ruleDir = System.getProperty("user.home") + "/sentinel/rules";
    //        String ruleDir = System.getProperty("user.dir") + "/sentinel/rules";
    //        String ruleDir =ResourceUtils.getURL("classpath:").getPath() + File.pathSeparator + "sentinel"+ File.pathSeparator +"rules";
            String ruleDir = "D:"+ File.separator + "zzx" + File.separator +"sentinelRules";
            String flowRulePath = ruleDir + File.separator+ "flow-rule.json";
            // 流控规则
            ReadableDataSource<String, List<FlowRule>> flowRuleRDS = new FileRefreshableDataSource<>(
                    flowRulePath,
                    flowRuleListParser
            );
            // 将可读数据源注册至FlowRuleManager
            // 这样当规则文件发生变化时，就会更新规则到内存
            FlowRuleManager.register2Property(flowRuleRDS.getProperty());
            WritableDataSource<List<FlowRule>> flowRuleWDS = new FileWritableDataSource<>(
                    flowRulePath,
                    this::encodeJson
            );
            // 将可写数据源注册至transport模块的WritableDataSourceRegistry中
            // 这样收到控制台推送的规则时，Sentinel会先更新到内存，然后将规则写入到文件中
            WritableDataSourceRegistry.registerFlowDataSource(flowRuleWDS);
        }
    
        private Converter<String, List<FlowRule>> flowRuleListParser = source -> JSON.parseObject(
              source,
                new TypeReference<List<FlowRule>>() {
              }
        );
        private <T> String encodeJson(T t) {
            return JSON.toJSONString(t);
        }
    }
    
    ```
  
  - 推模式：nacos
  
    **如果只是按照文档操作，测试失败，没有成功将sentinel规则写入nacos配置中心。**
  
    ```java
    public class NacosDataSourceInitFunc implements InitFunc {
        final String remoteAddress = "localhost:8848";
        final String groupId = "SENTINEL_GROUP";
        final String dataId = "basis-nacos-flow";
        @Override
        public void init() throws Exception {
            // remoteAddress 代表 Nacos 服务端的地址
            // groupId 和 dataId 对应 Nacos 中相应配置
            ReadableDataSource<String, List<FlowRule>> flowRuleDataSource = new NacosDataSource<>(remoteAddress, groupId, dataId,
                    source -> JSON.parseObject(source, new TypeReference<List<FlowRule>>() {}));
    
    
            FlowRuleManager.register2Property(flowRuleDataSource.getProperty());
    
        }
    }
    ```
  
    ```xml
    <dependency>
        <groupId>com.alibaba.csp</groupId>
        <artifactId>sentinel-datasource-nacos</artifactId>
    </dependency>
    ```
  
    ```yaml
    #      datasource:
    #        # 名称随意
    #        flow:
    #          nacos:
    #            server-addr: localhost:8848
    #            dataId: ${spring.application.name}-flow-rules
    #            groupId: SENTINEL_GROUP
    #            # 规则类型，取值见：
    #            # org.springframework.cloud.alibaba.sentinel.datasource.RuleType
    #            rule-type: flow
    ```

## （13）集群流控（待看）

> https://github.com/alibaba/Sentinel/wiki/%E9%9B%86%E7%BE%A4%E6%B5%81%E6%8E%A7





# 十二、SpringCloudGateway

## （1）官方文档

https://docs.spring.io/spring-cloud-gateway/docs/2.2.9.RELEASE/reference/html/#gateway-starter

## （2）初始搭建

依赖

```xml
<properties>
    <spring-cloud.version>Hoxton.SR3</spring-cloud.version>
    <spring-cloud-alibaba.version>2.2.6.RC1</spring-cloud-alibaba.version>
</properties>
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-gateway</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <!--            <groupId>org.springframework.cloud</groupId>-->
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        <!--<version>2.2.5.RELEASE</version>-->
    </dependency>

</dependencies>
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <!--整合springcloud alibaba-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>${spring-cloud-alibaba.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

配置

```yaml
server:
  port: 1111
spring:
  application:
    name: gateway
  cloud:
    nacos:
      server-addr: localhost:8848
    gateway:
      discovery:
        locator:
#          让gateway通过服务发现组件找到其他微服务
          enabled: true
management:
  endpoints:
    web:
      exposure:
        include: '*'
  endpoint:
    health:
      show-details: always
```

## （3）基本概念

Route路由

Predicate谓词

Filter过滤器

基本流程

<img src="pictures\gateway.png" style="zoom:75%;">

## （4）路由配置[Configuring Route ](https://docs.spring.io/spring-cloud-gateway/docs/2.2.9.RELEASE/reference/html/#configuring-route-predicate-factories-and-gateway-filter-factories)

两种方式：快捷（shortcuts）配置、完整的配置--谓词（predicates）配置方式的区别

快捷（shortcuts）配置

eg:

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: after_route
        uri: https://example.org
        predicates:
        - Cookie=mycookie,mycookievalue
```

完整的配置（Fully Expanded Arguments）

eg:

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: after_route
        uri: https://example.org
        predicates:
        - name: Cookie
          args:
            name: mycookie
            regexp: mycookievalue
```

## （5）路由谓词工厂[Route Predicate Factories](https://docs.spring.io/spring-cloud-gateway/docs/2.2.9.RELEASE/reference/html/#gateway-request-predicates-factories) （待测试）

### After

此路由匹配  当前配置的时间  之后发出的任何请求

参数类型为：datetime（a java `ZonedDateTime` 即带时区信息的时间）

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: after_route
        uri: https://example.org
        predicates:
        - After=2017-01-20T17:42:47.789-07:00[America/Denver]
```

eg:

```yaml
spring:
  application:
    name: gateway
  cloud:
    nacos:
      server-addr: localhost:8848
    gateway:
      discovery:
        locator:
#          让gateway通过服务发现组件找到其他微服务
          enabled: true
      routes:
        - id: basis_route
#          uri: localhost:8001
          uri: lb://basis
          predicates:
#            - After=2017-01-20T17:42:47.789-07:00[America/Denver]
#            - After=2021-07-23T00:00:00+08:00[Asia/Shanghai]
#            - After=2021-07-21T18:59:40.888481300+08:00[Asia/Shanghai]
#            - Query=a, 1.
            - Path=/*/basisTest/**
        - id: basis2_route
          uri: lb://basis2
          predicates:
#            - After=2021-07-21T18:59:40.888481300+08:00[Asia/Shanghai]
            - Path=/*/basis2Test/**
```

测试接口： http://localhost:1111/basis/basisTest/getPath

期初配置一直不生效，怀疑是依赖有问题，后来将路径中的服务名去掉:http://localhost:1111/basisTest/getPath

生效了。。。。

**注意！！！！**

这里的path只是请求路径，**不用刻意去强调开头是不是服务名**，需要请求basis服务的`/basisTest/getPath`时，根据匹配规则`/*/basisTest/**`匹配通过后，将整个/basisTest/getPath请求转发的uri配置的 lb://basis 上去

服务controller的mapping统一格式为 `/服务名/xxx`

### Before

此路由匹配  当前配置的时间  之前发出的任何请求

参数类型为：datetime（a java `ZonedDateTime` 即带时区信息的时间）

```yaml
# ……省略
        predicates:
        - Before=2017-01-20T17:42:47.789-07:00[America/Denver]
```



### Between

此路由匹配  当前配置起始时间  之间发出的任何请求

参数类型为：两个datetime（a java `ZonedDateTime` 即带时区信息的时间）

```yaml
# ……省略
        predicates:
        - Between=2017-01-20T17:42:47.789-07:00[America/Denver], 2017-01-21T17:42:47.789-07:00[America/Denver]
```



### Cookie

此路由匹配  包含**名为Chocolate的cookie并且值能够与ch.p 正则匹配**的请求。

参数类型为：一个普通string+一个java正则表达式

```yaml
# ……省略
        predicates:
        - Cookie=chocolate, ch.p
```



### Header

此路由匹配  **请求头包含名为 `参数1`且值能够与 `参数2` 正则匹配** 的请求

```yaml
# ……省略
        predicates:
        - Header=X-Request-Id, \d+
```



### Host

根据请求的host过滤

```yaml
# ……省略
        predicates:
        - Host=**.somehost.org,**.anotherhost.org
```



### Method

匹配所配置的request method

```yaml
# ……省略
        predicates:
        - Method=GET,POST
```

### Path

根据请求路径匹配

```yaml
# ……省略
        predicates:
        - Path=/red/{segment},/blue/{segment}
```



### Query

根据请求参数匹配

```yaml
# ……省略
        predicates:
        - Query=red, gree.
```

此路由匹配  **请求参数包含 `参数1`  且 `参数1` 的值与`参数2`正则匹配**  的请求

参数2非必需

### RemoteAddr

根据ip地址匹配

```yaml
# ……省略
        predicates:
        - RemoteAddr=192.168.1.1/24
```



### Weight

根据设置的权重转发到不同的uri

```yaml
# ……省略
      - id: weight_high
        uri: https://weighthigh.org
        predicates:
        - Weight=group1, 8
      - id: weight_low
        uri: https://weightlow.org
        predicates:
        - Weight=group1, 2
```

参数1：划分组  参数2：比例

（将约 80% 的流量转发到 weighthigh.org，将约 20% 的流量转发到 weightlow.org）

## （6）网关过滤器工厂[`GatewayFilter` Factories](https://docs.spring.io/spring-cloud-gateway/docs/2.2.9.RELEASE/reference/html/#gatewayfilter-factories)

“**Route filters allow the modification of the incoming HTTP request or outgoing HTTP response in some manner**. Route filters are scoped to a particular route. Spring Cloud Gateway includes many built-in GatewayFilter Factories.”

路由过滤器允许以某种方式修改request和response。

### AddRequestHeader

转发时，下游的http requestheader中会携带配置的键值对

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: basis_route
          uri: lb://basis
          predicates:
            - Path=/*/basisTest/**
          filters:
            - AddRequestHeader=X-Request-red, blue
```

验证在NettyRoutingFilter中打断点可以查看header内容

```
org.springframework.cloud.gateway.filter.NettyRoutingFilter
```

<img src="pictures\gateway-filter-test.png" style="zoom:70%">





暂时只测试这一项，剩余二十多种用到再说

相关值得一看的源码：

org.springframework.cloud.gateway.filter.factory.RequestSizeGatewayFilterFactory



## （7）监控 API [Actuator API](https://docs.spring.io/spring-cloud-gateway/docs/2.2.9.RELEASE/reference/html/#actuator-api)



| 路径                                  | 内容             |
| ------------------------------------- | ---------------- |
| /actuator/gateway/routes              | 路由列表         |
| /actuator/gateway/globalfilters       | 全局过滤器列表   |
| /actuator/gateway/routefilters        | 所有过滤器工厂   |
| /actuator/gateway/routes/{id}--GET    | 根据route_id查询 |
| /actuator/gateway/routes/{id}--POST   | 新增路由         |
| /actuator/gateway/routes/{id}--DELETE | 删除路由         |

监控路由有利于错误排查

另外，也可以配置某些包的日志级别为debug或trace

```properties
org.springframework.cloud.gateway
org.springframework.http.server.reactive
org.springframework.web.reactive
org.springframework.boot.autoconfigure.web
reactor.netty
redisratelimiter
```

也可以使用**wiretap**（需要高版本的支持）

```properties
spring.cloud.gateway.httpserver.wiretap=true
spring.cloud.gateway.httpclient.wiretap=true
```

# 十三、认证授权

## （1）AOP实现登录状态检查

依赖

```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-aop</artifactId>
    </dependency>
```
自定义注解和切面

```java
public @interface CheckLogin {
}
/**
 * @author running4light
 * @description 定义用于登录校验的切面
 * @createTime 2021/7/22 18:55
 */
@Aspect
@Component
public class CheckLoginAspect {
    @Around("@annotation(cn.running4light.basis.auth.CheckLogin)")
    public Object checkLogin(ProceedingJoinPoint point) throws Throwable {
        System.err.println("进来了。。。。。");
        // 从header获取token
        RequestAttributes requestAttributes = RequestContextHolder.getRequestAttributes();
        ServletRequestAttributes attributes = (ServletRequestAttributes) requestAttributes;
        HttpServletRequest request = attributes.getRequest();
        String token = request.getHeader("token");

        // 校验token  这里简单测试一下
        if(!"test".equals(token)){
            throw new SecurityException("token不合法");
        }
        request.setAttribute("userInfo","zzx");
        // 放行
        return point.proceed();
    }
}
```

测试：http://localhost:1111/basis/testLogin/getMsg

结果：

<img src="pictures\AOP登录校验测试1.png" style="zoom:70%">

<img src="pictures\AOP登录校验测试2.png" style="zoom:60%">

## （2）token的传递

### feign：使用拦截器转发token

> @RequestHeader("")--了解即可，一般不采用这种方式

TokenRequestInterceptor

```java
public class TokenRequestInterceptor implements RequestInterceptor {
    @Override
    public void apply(RequestTemplate template) {
        System.err.println("token转发ing");
        // 获取token
        RequestAttributes requestAttributes = RequestContextHolder.getRequestAttributes();
        ServletRequestAttributes attributes = (ServletRequestAttributes) requestAttributes;
        HttpServletRequest request = attributes.getRequest();
        String token = request.getHeader("token");

        // 传递token
        if(!"".equals(token)){
            template.header("token", token);
        }
    }
}
```

```yaml
feign:
  client:
    config:
      basis2:
        loggerLever: full
        requestInterceptors:
          - cn.running4light.basis.auth.interceptor.TokenRequestInterceptor
```

测试：http://localhost:8555/basis/testLogin/testTokenTransfer

### RestTemplate的token传递

普通方式--exchange（）

```java
public ResponseEntity<String> tokenTransfer2(HttpServletRequest request) {
    // 根据服务名获取目标服务的主机名端口号等信息
    List<ServiceInstance> basis2 = client.getInstances("basis2");

    HttpHeaders headers = new HttpHeaders();
    headers.add("token", request.getHeader("token"));
    ResponseEntity<String> exchange = restTemplate.exchange(
            "http://" + ServiceList.basis2 + "/basis2/basis2Test/testTokenTransfer2",
            HttpMethod.GET,
            new HttpEntity<>(headers),
            String.class
    );
    return exchange;
}
```

拦截器方式

```java
public class TokenRestTemplateInterceptor implements ClientHttpRequestInterceptor {
    private Logger logger = LoggerFactory.getLogger(TokenRestTemplateInterceptor.class);
    @Override
    public ClientHttpResponse intercept(HttpRequest request, byte[] body, ClientHttpRequestExecution execution) throws IOException {
        logger.info("token转发ing");
        // 获取token
        RequestAttributes requestAttributes = RequestContextHolder.getRequestAttributes();
        ServletRequestAttributes attributes = (ServletRequestAttributes) requestAttributes;
        HttpServletRequest httpRequest = attributes.getRequest();
        String token = httpRequest.getHeader("token");
        request.getHeaders().add("token", token);

        // 保证请求继续执行（保证其他拦截器正常执行）
        return execution.execute(request, body);
    }
}
```

```java
@Bean
@LoadBalanced // 加上@LoadBanlanced注解，就可以直接用服务名称进行服务间通信，不需要再去获取hostport等信息
public RestTemplate restTemplate2(){
    RestTemplate restTemplate = new RestTemplate();
    restTemplate.setInterceptors(
            Collections.singletonList(
                    new TokenRestTemplateInterceptor()
            )
    );
    return restTemplate;
}
```
测试：http://localhost:8555/basis/testLogin/testTokenTransfer3

## （3）JWT [JWT官方说明](https://jwt.io/introduction)

- jwt由三部分组成：header、payload、signature
  - header：算法类型和token类型

    eg:
    {  "alg": "HS256",  "typ": "JWT" }

  - payload:

    “The second part of the token is the payload, which contains the claims. Claims are statements about an entity (typically, the user) and additional data. There are three types of claims: *registered*, *public*, and *private* claims.”

    payload存放额外的信息，例如用户信息等

  - signature：将base64后的header、base64后的payload，特定的字符串做签名

    HMACSHA256(  base64UrlEncode(header) + "." +  base64UrlEncode(payload),  secret)

- 完整的jwt为

  **base64(header).base64(payload).base64(signature)**

  

## （4）AOP方式实现用户权限验证

https://github.com/dandelionflying/SpringCLoudAlibaba/tree/master/basis/src/main/java/cn/running4light/basis/auth

> **未使用jwt，仅测试基础逻辑**

定义注解

```java
@Documented
@Target({METHOD})
@Retention(RUNTIME)
public @interface CheckAuth {
    String value() default "";
}
```

定义切面

```java
@Around("@annotation(cn.running4light.basis.auth.CheckAuth)")
public Object checkAuth(ProceedingJoinPoint point) throws Throwable {
    try {
        checkToken();
        HttpServletRequest request = getHttpServletRequest();
        String role = (String) request.getAttribute("role");
        // 实际需要通过jwt获取claim  拿到权限信息
        MethodSignature signature = (MethodSignature)point.getSignature();
        Method method = signature.getMethod();
        CheckAuth annotation = method.getAnnotation(CheckAuth.class);
        String value = annotation.value();
        if (!StringUtils.equals(value,role))
            throw new SecurityException("Access fail");
    } catch (Throwable throwable) {
        throw new SecurityException("Access fail", throwable);
    }
    logger.info("Access Success");
    return point.proceed();
}
```

测试：http://localhost:8555/basis/role/testRole header携带：token:test  role:admin

# 十四、nacos配置中心

## （1）从nacos配置中心获取配置

依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

```yaml
spring:
  cloud:
    nacos:
      config:
        server-addr: localhost:8848
        file-extension: yaml
#        默认是application.name 配置了则以此配置为准
        prefix: basis
#        如果不是默认的分组需要加上分组名
        group: config_group
        #加上控制台会不停输出config changed信息
#        namespace: public
  application:
    name: basis
  profiles:
    active: dev
```

测试：http://localhost:8555/basis/testConfig/getConfigFromNacos

## （2）共享nacos配置中心配置

配置

```yaml
spring:
  cloud:
    nacos:
      config:
        server-addr: localhost:8848
        file-extension: yaml
        prefix: basis
        group: config_group
        # 公共配置
        shared-configs[0]:
          data-id: common1.yaml # 配置文件名-Data Id
          group: config_group   # 默认为DEFAULT_GROUP
          refresh: true   # 是否动态刷新，默认为false
```

测试：http://localhost:8555/basis/testConfig/getCommonConfigFromNacos