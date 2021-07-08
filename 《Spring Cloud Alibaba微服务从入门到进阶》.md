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

