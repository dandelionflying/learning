《Spring Cloud分布式微服务实战，养成应对复杂业务的综合技术能力》

- [x] 微服务架构中统一的api管理设计

- [x] swagger2的使用

- [ ] 短信登录注册

- [ ] 短信验证码发送与限制

- [ ] 分布式会话

- [x] AOP日志监控

  所有符合包路径下的所有类的所有方法

  ```java
  @Around("execution(* com.imooc.*.service.impl..*.*(..))")
  ```

- [x] Cap理论（一致性+可用性+分区容错性）

  无法同时满足CAP

  CP：

  AP：主流，数据的弱一致性 

  CA：单体，不符合分布式应用

- [x] 缓存之双写数据不一致

  解决：缓存双删--在更新数据库之前删一次缓存，数据库更新结束时再次删除缓存，那么不论用户读操作在数据库更新之前到达还是之后到达，都会重新从数据库同步最新的内容。

- [ ] FastDFS 待学习

  

- [ ] OSS

- [ ] 阿里ai文本检测 待学习

- [x] 页面静态化

  基本示例：

  ```java
      @Value("${freemarker.html.target}")
      private String htmlTarget;
  
      @GetMapping("/createHTML")
      @ResponseBody
      public String createHTML(Model model) throws Exception {
  
          // 0. 配置freemarker基本环境
          Configuration cfg = new Configuration(Configuration.getVersion());
          // 声明freemarker模板所需要加载的目录的位置
          String classpath = this.getClass().getResource("/").getPath();
          cfg.setDirectoryForTemplateLoading(new File(classpath + "templates"));
  
  //        System.out.println(htmlTarget);
  //        System.out.println(classpath + "templates");
  
          // 1. 获得现有的模板ftl文件
          Template template = cfg.getTemplate("stu.ftl", "utf-8");
  
          // 2. 获得动态数据
          String stranger = "慕课网 imooc.com";
          model.addAttribute("there", stranger);
          model = makeModel(model);
  
          // 3. 融合动态数据和ftl，生成html
          File tempDic = new File(htmlTarget);
          if (!tempDic.exists()) {
              tempDic.mkdirs();
          }
  
          Writer out = new FileWriter(htmlTarget + File.separator + "10010" + ".html");
          template.process(model, out);
          out.close();
  
          return "ok";
      }
  ```

  作用：

  1.便于SEO

  2.加速用户访问

  3.降低数据库压力

- [x] 静态化解耦

  生成html，上传到GridFS（或FastDFS之类的分布式文件系统中间件）中

  ```java
  	@Autowired
      private GridFSBucket gridFSBucket;
      // 文章生成HTML
      public String createArticleHTMLToGridFS(String articleId) throws Exception {
          Configuration cfg = new Configuration(Configuration.getVersion());
          String classpath = this.getClass().getResource("/").getPath();
          cfg.setDirectoryForTemplateLoading(new File(classpath + "templates"));
          Template template = cfg.getTemplate("detail.ftl", "utf-8");
          // 获得文章的详情数据
          ArticleDetailVO detailVO = getArticleDetail(articleId);
          Map<String, Object> map = new HashMap<>();
          map.put("articleDetail", detailVO);
          // 生成html
          String htmlContent = FreeMarkerTemplateUtils.processTemplateIntoString(template, map);
  //        System.out.println(htmlContent);
          InputStream inputStream = IOUtils.toInputStream(htmlContent);
          // 上传到GridFS 参数：文件名+文件流
          ObjectId fileId = gridFSBucket.uploadFromStream(detailVO.getId() + ".html",inputStream);
          return fileId.toString();
      }
  ```

  获得mongodbFileId，关联数据库相关信息（如文章表）

  调用消费端（前端服务器），下载GriDFS的html进行发布

  ```java
      @Autowired
      private GridFSBucket gridFSBucket;
  
      @Value("${freemarker.html.article}")
      private String articlePath;
  
      @Override
      public Integer download(String articleId, String articleMongoId)
              throws Exception {
  
          // 拼接最终文件的保存的地址
          String path = articlePath + File.separator + articleId + ".html";
  
          // 获取文件流，定义存放的位置和名称
          File file = new File(path);
          // 创建输出流
          OutputStream outputStream = new FileOutputStream(file);
          // 执行下载
          gridFSBucket.downloadToStream(new ObjectId(articleMongoId), outputStream);
  
          return HttpStatus.OK.value();
      }
  ```

  

  > 顺便尝试插入图片（相对路径）：

  ![](D:/zzx/codes/learning/pictures/静态化解耦.png)

  

- [x] rabbitMQ异步解耦

  配置

  ```yaml
    rabbitmq:
      host: 192.168.1.204
      port: 5672
      username: admin
      password: admin
      virtual-host: imooc-news-dev
  ```

  ```java
  // 定义交换机的名字
      public static final String EXCHANGE_ARTICLE = "exchange_article";
  
      // 定义队列的名字
      public static final String QUEUE_DOWNLOAD_HTML = "queue_download_html";
  
      // 创建交换机
      @Bean(EXCHANGE_ARTICLE)
      public Exchange exchange(){
          return ExchangeBuilder
                  .topicExchange(EXCHANGE_ARTICLE)
                  .durable(true)
                  .build();
      }
  
      // 创建队列
      @Bean(QUEUE_DOWNLOAD_HTML)
      public Queue queue(){
          return new Queue(QUEUE_DOWNLOAD_HTML);
      }
  
      // 队列绑定交换机
      @Bean
      public Binding binding(
              @Qualifier(QUEUE_DOWNLOAD_HTML) Queue queue,
              @Qualifier(EXCHANGE_ARTICLE) Exchange exchange){
          return BindingBuilder
                  .bind(queue)
                  .to(exchange)
                  .with("article.#.do")// 路由规则
                  .noargs();      // 执行绑定
      }
  ```

  路由规则示例

  ```
  * RabbitMQ 的路由规则 routing key
  *  display.*.* -> * 代表一个占位符
  *      例：
  *          display.do.download  匹配
  *          display.do.upload.done   不匹配
  *
  * display.# -> # 代表任意多个占位符
  *      例:
  *          display.do.download  匹配
  *          display.do.upload.done.over   匹配
  ```

  生产者生产消息：

  ```java
      @Autowired
      private RabbitTemplate rabbitTemplate;
  
      public Object produce() {
          rabbitTemplate.convertAndSend(
                  RabbitMQConfig.EXCHANGE_ARTICLE,
                  "article.publish.download.do",
                  "1001");
          return GraceJSONResult.ok();
      }
  ```

  消费

  ```java
  @RabbitListener(queues = {RabbitMQConfig.QUEUE_DOWNLOAD_HTML})
  public void watchQueue(String payload, Message message) {
      System.out.println(payload);
  
      String routingKey = message.getMessageProperties().getReceivedRoutingKey();
  }
  ```

- [x] rabbitMQ-- 延迟队列

  安装插件

  > https://github.com/rabbitmq/rabbitmq-delayed-message-exchange/releases

  配置

  ```java
  @Bean(EXCHANGE_DELAY)
  public Exchange exchange(){
      return ExchangeBuilder
              .topicExchange(EXCHANGE_DELAY)
              .delayed()          // 开启支持延迟消息
              .durable(true)
              .build();
  }
  ```

  生产延时消息

  ```java
  @GetMapping("/delay")
  public Object delay() {
      MessagePostProcessor messagePostProcessor = new MessagePostProcessor() {
          @Override
          public Message postProcessMessage(Message message) throws AmqpException {
              // 设置消息的持久
              message.getMessageProperties()
                      .setDeliveryMode(MessageDeliveryMode.PERSISTENT);
              // 设置消息延迟的时间，单位ms毫秒
              message.getMessageProperties()
                      .setDelay(5000);
              return message;
          }
      };
      rabbitTemplate.convertAndSend(
              RabbitMQDelayConfig.EXCHANGE_DELAY,
              "delay.demo",
              "这是一条延迟消息~~",
              messagePostProcessor);
      return "OK";
  }
  ```

  

- [x] web项目架构演变

  单体

  ![](D:/zzx/codes/learning/pictures/架构演变1--单体.png)

  

  垂直

  ![](D:/zzx/codes/learning/pictures/架构演变2--垂直应用架构.png)

  

  soa

  ![](D:/zzx/codes/learning/pictures/架构演变3--分布式SOA.png)

  

  微服务

  ![](D:/zzx/codes/learning/pictures/架构演变4--微服务架构.png)

  

  

- [x] Eureka配置

  server:

  ```yaml
  eureka:
    instance:
      # eureka 实例的hostname，可以是hostname，也可以自定义配置hostname
      hostname: eureka
    client:
      # 是否要把当前的eureka server注册到自己
      register-with-eureka: false
      # 从注册中心获得检索服务实例，server没有必要，直接false即可
      fetch-registry: false
      # 单实例配置自己的服务地址，高可用集群则配置多个地址
      service-url:
        defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
    server:
      enable-self-preservation: false   # 关闭eureka的自我保护功能
      eviction-interval-timer-in-ms: 5000   # 清理无效节点的时间，可以缩短为5s，默认60s
  ```

  ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
  </dependency>
  ```

  client:

  ```yaml
  eureka:
    # 自定义eureka server的信息
    server:
      hostname: eureka
      port: 7000
    client:
      # 所有的微服务都必须注册到eureka中
      register-with-eureka: true
      # 从注册中心获得检索服务实例
      fetch-registry: true
      # 注册中心的服务地址
      service-url:
  #      defaultZone: http://${eureka.server.hostname}:${eureka.server.port}/eureka/
        defaultZone: http://eureka-cluster-7001:7001/eureka/,http://eureka-cluster-7002:7002/eureka/,http://eureka-cluster-7003:7003/eureka/
    instance:
      lease-renewal-interval-in-seconds: 3      # 调整微服务（eureka client）和注册中心（eureka server）的心跳时间
      lease-expiration-duration-in-seconds: 5   # eureka 举例最近的一次心跳等待提出的时间，默认90s
  ```

  ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
  </dependency>
  ```


- [x] 服务间通信


```java
// 方式1：使用discoveryClient根据服务名称获取主机名及端口号

// 注入服务发现，可以获得已经注册的服务相关信息
@Autowired
private DiscoveryClient discoveryClient;
@Autowired
private RestTemplate restTemplate;

func(){
    
    String serviceId = "SERVICE-USER";
    List<ServiceInstance> instanceList = discoveryClient.getInstances(serviceId);
    ServiceInstance userService = instanceList.get(0);
    String userServerUrlExecute
                = "http://" + userService.getHost() +
                ":"
                + userService.getPort()
                + "/user/queryByIds?userIds=" + JsonUtils.objectToJson(idSet);
	ResponseEntity<GraceJSONResult> responseEntity
                = restTemplate.getForEntity(userServerUrlExecute, GraceJSONResult.class);
    GraceJSONResult bodyResult = responseEntity.getBody();      
}

// 方式2:在restTemplate配置类中增加@LoadBalanced，默认轮询
@Configuration
public class CloudConfig {
    /**
     * 会基于OKHttp3的配置来配置RestTemplate
     * @return
     */
    @Bean
    @LoadBalanced       // 默认的负载均衡算法：轮询
    public RestTemplate restTemplate() {
        return new RestTemplate(new OkHttp3ClientHttpRequestFactory());
    }
}


func2(){
    String userServerUrlExecute
               = "http://" + serviceId + "/user/queryByIds?userIds=" + JsonUtils.objectToJson(idSet);
    ResponseEntity<GraceJSONResult> responseEntity
                = restTemplate.getForEntity(userServerUrlExecute, GraceJSONResult.class);
    GraceJSONResult bodyResult = responseEntity.getBody(); 
}

```

```java
// 方式3：feign方式
// 启动类配置--调用端
@EnableFeignClients({"com.imooc"})

// controller配置--被调用端
@FeignClient(value = MyServiceList.SERVICE_USER, fallbackFactory = UserControllerFactoryFallback.class) value = "application里配置的servicename"
    
// 使用

func3(){
    GraceJSONResult bodyResult = userControllerApi.queryByIds(JsonUtils.objectToJson(idSet));
    
}

@Api(value = "用户信息相关Controller", tags = {"用户信息相关Controller"})
@RequestMapping("user")
@FeignClient(value = MyServiceList.SERVICE_USER, fallbackFactory = UserControllerFactoryFallback.class)
public interface UserControllerApi {
        @ApiOperation(value = "根据用户的ids查询用户列表", notes = "根据用户的ids查询用户列表", httpMethod = "GET")
    @GetMapping("/queryByIds")
    public GraceJSONResult queryByIds(@RequestParam String userIds);
}
    
```



feign

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

声明式http工具



- [x] 动态构建eureka集群

```yaml
# eureka 集群的注册中心
# web访问端口号  约定：7001~7003
#
server:
  port: ${port:7001}
    
······

eureka:
  instance:
    # 集群中每个eureka的名字都是唯一的
    hostname: eureka-cluster-${server.port}
  other-node-port2: ${p2:7002}
  other-node-port3: ${p3:7003}
  client:
#    register-with-eureka: false
#    fetch-registry: false
    # 单实例配置自己的服务地址，高可用集群则配置多个地址
    service-url:
      defaultZone: http://eureka-cluster-${eureka.other-node-port2}:${eureka.other-node-port2}/eureka/,http://eureka-cluster-${eureka.other-node-port3}:${eureka.other-node-port3}/eureka/
  server:
    enable-self-preservation: false   # 关闭eureka的自我保护功能
    eviction-interval-timer-in-ms: 5000   
```

- [x] 微服务注册到eureka集群

  将所有eureka集群的地址添加到 **eureka.client.service-url.defaultZone** 中

  ```yaml
      service-url:
  #      defaultZone: http://${eureka.server.hostname}:${eureka.server.port}/eureka/
        defaultZone: http://eureka-cluster-7001:7001/eureka/,http://eureka-cluster-7002:7002/eureka/,http://eureka-cluster-7003:7003/eureka/
  ```

- [x] 构建微服务集群

  只需改成动态端口配置

  ```yaml
  server:
    port: ${port:8003}
  ```

- [x] 基于客户端的负载均衡--ribbon

  启动类配置

  ```java
  @RibbonClient(name = "SERVICE-USER", configuration = MyRule.class)
  ```

  当前的ribbonClient发送请求到name时，使用configuration配置的负载均衡机制

  或 配置在yml中：

  ```yaml
  # 配置指定自定义的ribbon规则
  SERVICE-USER:
    ribbon:
      NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule
  ```

  重试机制

  配置(调用端)：

  ```xml
  <dependency>
      <groupId>org.springframework.retry</groupId>
      <artifactId>spring-retry</artifactId>
  </dependency>
  ```

  ```yaml
  ribbon:
    ConnectTimeout: 5000          # 创建连接的超时时间，单位：ms
    ReadTimeout: 5000             # 在连接创建好以后，调用接口的超时时间，单位：ms
    MaxAutoRetries: 1             # 最大重试次数
    MaxAutoRetriesNextServer: 2   # 切换到下个微服务实例的重试次数
    # 当请求到某个微服务5s，超时后会进行重试，先重试连接自己当前的这个实例
    # 如果当前重试失败1次，则会切换到访问集群中的下一个微服务实例，切换最大为2次
  ```

- [x] 微服务链路问题--hystrix（熔断器）--提供容错机制，避免微服务系统雪崩

  ![](D:/zzx/codes/learning/pictures/熔断器工作原理.png)

  配置

  ```xml
  // 依赖
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
  </dependency>
  ```

  ```java
  // 启动类
  @EnableCircuitBreaker   // 开启hystrix的熔断机制
  ```

  ```yaml
  # 配置hystrix
  hystrix:
    command:
      default:
        execution:
          isolation:
            thread:
              timeoutInMilliseconds: 2000   # 设置hystrix超时时间，超过2秒触发降级
  ```

  熔断降级

  ```java
  @HystrixCommand(fallbackMethod = "queryByIdsFallback")
  
  ```

  全局降级

  ```java
  // controller配置
  @DefaultProperties(defaultFallback = "defaultFallback")
  
  // 方法注解
  @HystrixCommand // 这里就不用指明方法名了
  ```

  服务调用者降级

  ```yaml
  feign:
    hystrix:
      enabled: true   # 打开feign客户端的内置hystrix
  ```

  ```java
  // 1.在配调用端配置 fallbackFactory
  @FeignClient(value = MyServiceList.SERVICE_USER, fallbackFactory = UserControllerFactoryFallback.class)
  // 2.自定义一个类，用来处理当前被调用端controller的所有方法的降级
  // eg:
  @Component
  public class UserControllerFactoryFallback implements FallbackFactory<UserControllerApi> {
      @Override
      public UserControllerApi create(Throwable throwable) {
          return new UserControllerApi() {
              @Override
              public GraceJSONResult getUserInfo(String userId) {
                  return GraceJSONResult.errorCustom(ResponseStatusEnum.SYSTEM_ERROR_FEIGN);
              @Override
              public GraceJSONResult queryByIds(String userIds) {
                  System.out.println("进入客户端（服务调用者）的降级方法");
                  List<AppUserVO> publisherList = new ArrayList<>();
                  return GraceJSONResult.ok(publisherList);
              }
          };
      }
  }
  
  ```

  服务降级规则配置

  ```yaml
  hystrix:
    command:
      default:
        circuitBreaker:   # 配置断路器
          enabled: true
          requestVolumeThreshold: 10    # 触发熔断最小请求次数，默认：20
          sleepWindowInMilliseconds: 15000    # 熔断后过几秒后尝试半开状态（请求重试），默认：5s
          errorThresholdPercentage: 50  # 触发熔断的失败率（异常率/阈值），默认：50
  ```

- [x] 微服务网关Zuul

  初始化

  ```xml
  // 依赖
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
  </dependency>
  ```

  ```java
  // 网关微服务启动类配置
  @EnableEurekaClient
  //@EnableZuulServer
  @EnableZuulProxy       // @EnableZuulProxy是@EnableZuulServer的一个增强升级版，当zuul和eureka、ribbon等组件共同使用，则使用增强版即可
  // 
  ```

  配置路由

  ```yaml
  # 路由规则: http://[网关地址]:[端口号]/[prefix]/[微服务实例id]/[请求地址路径]
  zuul:
    routes:
      # 由于路由id和微服务实例id相同，我们可以简化转发的配置
      service-article: /service-article/**
    #    service-article:                  # 配置微服务的路由id，微服务的实例id
  #      path: /service-article/**       # 请求路径(前缀)
  #      service-id: service-article     # 请求转发的微服务实例id
  #      url: http://192.168.1.2:8001    # 请求转发到指定的微服务所在的ip地址
    prefix: /api                        # 请求前缀
  ```

  **需要结合eureka做服务发现，所以eureka客户端配置也要加上**

  网关过滤器

  ```java
  /**
   * 构建zuul的自定义过滤器
   */
  @Component
  public class MyFilter extends ZuulFilter {
      /**
       * 定义过滤器的类型
       *      pre：    在请求被路由之前执行
       *      route：  在路由请求的时候执行
       *      post：   请求路由以后执行
       *      error：  处理请求时发生错误的时候执行
       * @return
       */
      @Override
      public String filterType() {
          return "pre";
      }
      /**
       * 过滤器执行的顺序，配置多个有顺序的过滤
       * 执行顺序从小到大
       * @return
       */
      @Override
      public int filterOrder() {
          return 1;
      }
      /**
       * 是否开启过滤器
       *      true：开启
       *      false：禁用
       * @return
       */
      @Override
      public boolean shouldFilter() {
          return true;
      }
      /**
       * 过滤器的业务实现
       * @return
       * @throws ZuulException
       */
      @Override
      public Object run() throws ZuulException {
          System.out.println("display pre zuul filter...");
          return null;    // 没有意义可以不用管。
      }
  }
  ```

- [x] 基于zuul+redis的ip黑名单拦截设计

  配置

  ```yaml
  # ip请求限制的参数配置
  blackIp:
    continueCounts: ${counts:10}    # ip连续请求的次数
    timeInterval: ${interval:10}    # ip判断的事件间隔，单位：秒
    limitTimes: ${times:15}         # 限制的事件，单位：秒
  ```

  > ```java
  > // 获得上下文对象
  > RequestContext context = RequestContext.getCurrentContext();
  > HttpServletRequest request = context.getRequest();
  > ```

  ```java
  @Component
  @RefreshScope
  public class BlackIPFilter extends ZuulFilter {
      @Value("${blackIp.continueCounts}")
      public Integer continueCounts;
      @Value("${blackIp.timeInterval}")
      public Integer timeInterval;
      @Value("${blackIp.limitTimes}")
      public Integer limitTimes;
      @Autowired
      private RedisOperator redis;
  
      ······
  
      @Override
      public Object run() throws ZuulException {
          System.out.println("执行【ip黑名单】过滤器...");
          // 获得上下文对象
          RequestContext context = RequestContext.getCurrentContext();
          HttpServletRequest request = context.getRequest();
  
          // 获得ip
          String ip = IPUtil.getRequestIp(request);
  
          final String ipRedisKey = "zuul-ip:" + ip;
          final String ipRedisLimitKey = "zuul-ip-limit:" + ip;
  
          // 获得当前ip这个key的剩余时间
          long limitLeftTime = redis.ttl(ipRedisLimitKey);
          // 如果当前限制ip的key还存在剩余时间，说明这个ip不能访问，继续等待
          if (limitLeftTime > 0) {
              stopRequest(context);
              return null;
          }
  
          // 在redis中累加ip的请求访问次数
          long requestCounts = redis.increment(ipRedisKey, 1);
          // 从0开始计算请求次数，初期访问为1，则设置过期时间，也就是连续请求的间隔时间
          if (requestCounts == 1) {
              redis.expire(ipRedisKey, timeInterval);
          }
  
          // 如果还能取得请求次数，说明用户连续请求的次数落在10秒内
          // 一旦请求次数超过了连续访问的次数，则需要限制这个ip的访问
          if (requestCounts > continueCounts) {
              // 限制ip的访问时间
              redis.set(ipRedisLimitKey, ipRedisLimitKey, limitTimes);
              stopRequest(context);
          }
  
          return null;
      }
  
      private void stopRequest(RequestContext context) {
          // 停止zuul继续向下路由，禁止请求通信
          context.setSendZuulResponse(false);
          context.setResponseStatusCode(200);
          String result = JsonUtils.objectToJson(
                  GraceJSONResult.errorCustom(
                          ResponseStatusEnum.SYSTEM_ERROR_ZUUL));
          context.setResponseBody(result);
          context.getResponse().setCharacterEncoding("utf-8");
          context.getResponse().setContentType(MediaType.APPLICATION_JSON_VALUE);
      }
  }
  ```

- [x] 分布式配置中心

  基本配置--server

  ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-config-server</artifactId>
  </dependency>
  ```

  ```yaml
  spring:
    cloud:
      config:
        server:
          git:
            uri: https://github.com/leechenxiang/imooc-news-config.git #存放配置文件的远程仓库
  ```

  ```java
  // 启动类配置
  @EnableEurekaClient
  @EnableConfigServer
  ```

  基本配置--client(以zuul-server微服务示例)

  ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-config</artifactId>
  </dependency>
  ```

  ```yaml
  spring:  
    cloud:
      config:
        label: master
        name: zuul
        profile: prod
  #      uri: http://192.168.1.2:7080
        discovery:
          enabled: true
          service-id: springcloud-config
  ```

  动态刷新git配置

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-actuator</artifactId>
  </dependency>
  ```

  ```java
  // ZuulFilter中增加注解
  @RefreshScope
  ```

  ```yaml
  # 配置动态刷新git配置的路径终端请求地址
  management:
    endpoints:
      web:
        exposure:
          include: refresh
  ```

  > 请求刷新
  >
  > http://ip:port/actuator/bus-refresh

- [x] springcloud bus -- 消息总线

  

  ```xml
  <!-- springcloudConfig微服务及其他普通微服务（消息接受端）均需要引入-->
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-bus-amqp</artifactId>
  </dependency>
  ```

  ```yaml
  # springcloudConfig微服务及其他普通微服务（消息接受端）均需要配置
  spring:
      rabbitmq:
        host: 192.168.1.204
        port: 5672
        username: admin
        password: admin
        virtual-host: imooc-news-dev
  ```

  ```yaml
  # 配置动态刷新git配置的路径终端请求地址
  management:
    endpoints:
      web:
        exposure:
          include: "*"
  ```

  > 请求刷新(只需向config微服务发送刷新请求，其余消费端即可更新)
  >
  > http://ip:port/actuator/bus-refresh

  也可定向刷新：

  > 请求刷新
  >
  > http://ip:port/actuator/bus-refresh/微服务实例id:端口

- [ ] 消息驱动--springcloud Stream(待看)

- [ ] zipkin+sleuth(待看)


