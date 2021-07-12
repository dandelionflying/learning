**《面向未来微服务Spring Cloud Alibaba微服务从入门到进阶》**

doc:

https://docs.spring.io/spring-boot/docs/2.5.x/reference/htmlsingle/

boot官方依赖格式 <spring-boot-starter-xxx>

非官方依赖格式	<xxx-spring-boot-starter>

依赖查找：

https://docs.spring.io/spring-boot/docs/2.5.x/reference/htmlsingle/#using.build-systems.starters



一、springboot Actuator

应用监控

> Spring Boot includes a number of additional features to help you monitor and manage your application when you push it to production. You can choose to manage and monitor your application by using HTTP endpoints or with JMX. Auditing, health, and metrics gathering can also be automatically applied to your application.

（1）基本使用：

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

  

![](pictures\actuator-health-detail.png)

​				status取值

​					up：正常

​					down：不正常

​					out_of_service：资源未在使用

​					unknown

​		**2）/info**

​		描述应用：以键值对形式配置

​		![](pictures\actuator-info.png)

- 端点激活

  ```properties
  #激活所有端点 默认只有health(2.5版本)
  management.endpoints.web.exposure.include=*
  ```

二、springboot配置管理

（1）配置管理常用方式

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

（2）profile

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

三、微服务拆分

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

四、springboot RestTemplate

（1）一个轻量级的http客户端，用于微服务之间的请求调用。

（2）基本使用

```java
// 二者区别：getForEntity可以拿到http响应体的内容，而getForObject只是拿到响应体数据
ResponseEntity<String> forEntity = restTemplate.getForEntity("http:host:port/api/{param1}", String.class);
restTemplate.getForEntity("http:host:port/api/{param1}",String.class, 2);

String forObject = restTemplate.getForObject("http:host:port/api/{param1}", String.class);
restTemplate.getForObject("http:host:port/api/{param1}",String.class, 2);
```

六、整合Spring Cloud Alibaba

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

七、nacos

（1）安装运行

去官网下载nacos的server和client，版本选择可点击`<artifactId>spring-cloud-alibaba-dependencies</artifactId>`根据里面的nacos版本号选择相应的版本下载使用

控制台地址 http://192.168.217.1:8848/nacos/index.html

（2）服务注册

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

（3）元数据

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

八、服务间通信

（1）原始方式（低级，不使用）：

​		通过DiscoveryClient获取服务名关联的实例`client.getInstances("basis2")`，拼接后使用restTemplate发送请求`restTemplate.getForObject(url,SomeClass.class)`。

（2）引入负载均衡：

​		声明RestTemplate Bean时，加上**@LoadBalanced**注解，就可以直接用服务名称进行服务间通信，不需要再去获取hostport等信息`restTemplate.getForObject("http://"+ ServiceList.basis2 + "/basis2/findBasis2", String.class)`。()

（3）feign方式

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

![](pictures\feign服务调用报错2.png)

