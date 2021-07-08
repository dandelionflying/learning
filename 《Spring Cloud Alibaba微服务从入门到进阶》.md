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