### 消息队列

1. 常见消息队列有哪里，什么工作模式，相互区别？

   kafka 是可水平伸缩，支持分布式消费 很难实现事务？

   一对一消费 不可伸缩 可很好支持事务？ 

2. 消息队列 有什么特点，异步通信？ 不适合哪些场景？

3. kafka 工作机理，offset 保存在哪里

4. API 熟悉程度

5. 如何避免 重复消费



### 生产者

```properties
#producer.properties
bootstrap.servers=192.168.1.13:9092 #kafka broker地址
acks=all
retries=1
batch.size=16384
linger.ms=1
buffer.memory=33554432
key.serializer=org.apache.kafka.common.serialization.StringSerializer
value.serializer=org.apache.kafka.common.serialization.StringSerializer
```

```java
Properties props = new Properties();
InputStream in = Main.class.getResourceAsStream("/producer.properties");
props.load(in); //properties 对象记录broker信息、连接配置、key 和 value 的序列化器
Producer<String, String> producer = new KafkaProducer<>(props);
ProducerRecord<String, String>  record = new ProducerRecord<String, String>("主题名", Integer.toString(key), jsonStr); // record对象记录 topic key value
producer.send(record, new Callback{ void onCompletion(RecordMetadata metadata, Exception exception){}} );
producer.close();
```



## 消费者

```properties
#producer.properties 基本配置
bootstrap.servers=192.168.1.13:9092
group.id=consumer
enable.auto.commit=true #什么阶段自动提交？
auto.commit.interval.ms=1000
key.deserializer=org.apache.kafka.common.serialization.StringDeserializer
value.deserializer=org.apache.kafka.common.serialization.StringDeserializer
```

### 消费方式  事件循环

```java
KafkaConsumer<String,String> consumer = new KafkaConsumer<>(props);
consumer.subscribe(Arrays.asList(topic));
while(true){
	ConsumerRecords<String, String> records = consumer.poll(100);
	for (ConsumerRecord<String, String> record : records) {
		record.value();
	}			
}
consumer.close();
//使用该方法关闭consumer
consumer.wakeup();
```

1. 无后台线程？

中断事件循环有两种方式:

- 较小的timeout, 通过使用标志位来控制
- 较长的timeout, 调用wakeup来退出循环



### 活跃度

kafka的消费组协调协议使用心跳机制监测消费者是否崩溃，如果检测到崩溃，触发rebalance.

所有的网络IO操作在调用poll或者其他的阻塞API,都是在前台完成的.消费者并不使用任何的后台线程. 这就意味着消费者的心跳只有在调用poll的时候才会发送给协调者.如果应用程序停止polling(不管是处理代码抛出异常或者下游系统崩溃了),就不会再发送心跳了,最终就会导致session超时(没有收到心跳,计时器开始增加), 然后消费组就会开始平衡操作.