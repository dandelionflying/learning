#### 订单超时未支付，自动取消

- **数据库轮询**：对服务器内存、数据库消耗大

- **jdk延迟队列**：DelayQueue。集群扩展麻烦，服务器重启数据丢失，容易OOM
- **时间轮询**：使用netty的HashedWheelTimer（或其他类似的），将线程丢到HashedWheelTimer示例中去，设置延迟的时间、时间单位 `timer.newTimeout(timerTask, 5, TimeUnit.SECONDS);`。效率比DelayQueue高，但依旧会出现上述问题
- **redis缓存**
  - zset：利用“分值”的特性，分值取当前时间戳，起一个消费者去获取当前最小分值（使用排序命令zrange）的数据，判断是否过期。过期就移出，未过期就进入下一次循环
  - 需要注意分布式场景下重复获取的问题，可以再移除时做校验，zrem返回值大于0才表示之前没有人操作过。
  - 键空间机制（`Keyspace Notifications`）。
- **消息队列**
  - rabbitMQ的延时队列

