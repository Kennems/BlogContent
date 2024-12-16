---
title : 'RabbitMQ'
date : 2024-11-05T22:30:13+08:00
lastmod: 2024-11-06T22:20:13+08:00
description : "RabbitMQ"  
categories : ["RabbitMQ"]
tags : ["RabbitMQ"]
---

# RabbitMQ

同步通讯 和 异步通讯

## 同步调用

同步调用的问题：

- 拓展性差
- 性能下降
- 级联失败

## 异步调用

异步调用方式其实就是基于消息通知的方式，一般包含三个角色：

- 消息发送者：投递消息的人，就是原来的调用方
- 消息代理：管理、暂存、转发消息，可以理解为微信服务器
- 消息接收者：接受和处理消息的人，就是原来的服务提供方

支付服务不再同步调用业务关联度低的服务，而是发送消息通知到Broker。

具备下列**优势**：

- 解除耦合，拓展性强
- 无需等待，性能好
- 故障隔离
- 缓存消息，流量削峰填谷

**缺点**：

- 不能立刻得到调用结果，时效性差
- 不确定下游业务执行是否成功
- 业务安全依赖于Broker的可靠性

## MQ

MQ（MessageQueue），消息队列，存放消息的队列，也就是异步调用中的Broker。

| 特性       | RabbitMQ                | ActiveMQ                          | RocketMQ   | Kafka        |
| ---------- | ----------------------- | --------------------------------- | ---------- | ------------ |
| 公司/社区  | Rabbit                  | Apache                            | 阿里       | Apache       |
| 开发语言   | Erlang                  | Java                              | Java       | Scala & Java |
| 协议支持   | AMQP, XMPP, SMTP, STOMP | OpenWire, STOMP, REST, XMPP, AMQP | 自定义协议 | 自定义协议   |
| 可用性     | 高                      | 一般                              | 高         | 非常高       |
| 单机吞吐量 | 一般                    | 差                                | 高         | 非常高       |
| 消息延迟   | 微秒级                  | 毫秒级                            | 毫秒级     | 毫秒以内     |
| 消息可靠性 | 高                      | 一般                              | 高         | 一般         |

## RabbitMQ的整体架构及核心概念

- virtual-host：虚拟主机，起到数据隔离的作用
- publisher：消息发送者
- consumer：消息的消费者
- queue：队列，存储消息
- exchange：交换机，负责路由消息

![image-20241105133632006](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241105133632006.png)

## 快速入门

### 启动MQ

```shell
docker run \
 -e RABBITMQ_DEFAULT_USER=itheima \
 -e RABBITMQ_DEFAULT_PASS=123321 \
 -v mq-plugins:/plugins \
 --name mq \
 --hostname mq \
 -p 15672:15672 \
 -p 5672:5672 \
 --network hmall \
 -d \
 rabbitmq:3.8-management
```

### 程序控制

#### AMQP

Advanced Message Queuing Protocol， 是用于在应用程序之间传递业务消息的开放标准。改协议与语言和平台无关，更符合微服务中独立性的要求。

### Spring AMQP

Spring AMQP 是基于AMQP协议定义的一套API规范，提供了模板来发送和接受消息。包含两部分，其中spring-amqp是基础抽象，spring-rabbit是底层的默认实现。

#### 发送者

```java
@SpringBootTest
public class SpringAmqpTest {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    void testSendMessage2Queue(){
        String queueName = "simple.queue";
        String msg = "hello, rabbitmq";
        rabbitTemplate.convertAndSend(queueName, msg);
    }
}

```

#### 监听者

```java
@Slf4j
@Component
public class MqListener {
    @RabbitListener(queues = "simple.queue")
    public void listenSimpleQueue(String msg) {
        System.out.println("消费者收到消息：【" + msg + "】");
    }
}
```

Work模型的使用：

- 多个消费者绑定到一个队列，可以加快消息处理速度
- 同一条消息只会被一个消费者处理
- 通过设置prefetch来控制消费者预取的消息数量，处理完一条再处理下一条，实现能者多劳

## 交换机

真正生产环境都会经过exchange来发送消息，而不是直接发送到队列，交换机的类型有以下三种：

- Fanout：广播
- Direct：定向
- Topic：话题

### Fanout交换机

Fanout Exchange会将接受到的消息广播到每一个跟其绑定的queue，所以也叫广播模式

### Direct交换机

Direct Exchange会将接收到的消息根据规则路由到指定的Queue，因此称为定向路由。

- 每一个Queue都与Exchange设置一个`BindingKey`
- 发布者发送消息时，指定消息的`RoutingKey`
- Exchange将消息路由到`BindingKey`与消息`RoutingKey`一致的队列

### Topic交换机

`TopicExchange`与`DirectExchange`类似，区别在于`routingKey`可以时多个单词的列表，并且以`，`分割。

Queue与Exchange指定`BindingKey`时可以使用通配符：

- `# `：代指0个或多个单词
- `*`：代指一个单词

## 声明队列和交换机

`SpringAMQP`提供了几个类，用来声明队列、交换机及其绑定关系：

- Queue：用于声明队列，可以用工厂类`QueueBuilder`构建
- Exchange：用于声明交换机，可以用工厂类`ExchangeBuilder`构建
- Binding：用于声明队列和交换机的绑定关系，可以用工厂类`BindingBuilder`构建

```java
@Configuration
public class DirectConfiguration {
    @Bean
    public DirectExchange directExchange() {
        return new DirectExchange("hmall.direct");
    }

    @Bean
    public Queue directQueue1() {
        return new Queue("direct.queue1");
    }

    @Bean
    public Binding directQueue1BindingRed(Queue directQueue1, DirectExchange directExchange) {
        return BindingBuilder.bind(directQueue1).to(directExchange).with("red");
    }

    @Bean
    public Binding directQueue1BindingBlue(Queue directQueue1, DirectExchange directExchange) {
        return BindingBuilder.bind(directQueue1).to(directExchange).with("blue");
    }

    @Bean
    public Queue directQueue2() {
        return new Queue("direct.queue2");
    }

    @Bean
    public Binding directQueue2BindingRed(Queue directQueue2, DirectExchange directExchange) {
        return BindingBuilder.bind(directQueue2).to(directExchange).with("red");
    }

    @Bean
    public Binding directQueue2BindingYellow(Queue directQueue2, DirectExchange directExchange) {
        return BindingBuilder.bind(directQueue2).to(directExchange).with("yellow");
    }
}
```



### 基于注解声明

```java
    @RabbitListener(bindings = @QueueBinding(
            value = @Queue(name = "direct.queue1", durable = "true"),
            exchange = @Exchange(name = "hmall.direct", type = ExchangeTypes.DIRECT),
            key = {"red", "blue", "Kennem"}
    ))
    public void listenDirectQueue1(String msg) throws InterruptedException {
        System.out.println("消费者1 收到 direct.queue1 消息：【" + msg + "】.......");
    }

    @RabbitListener(bindings = @QueueBinding(
            value = @Queue(name = "direct.queue2", durable = "true"),
            exchange = @Exchange(name = "hmall.direct", type = ExchangeTypes.DIRECT),
            key = {"red", "yellow"}
    ))
    public void listenDirectQueue2(String msg) throws InterruptedException {
        System.out.println("消费者2 收到 direct.queue1 消息：【" + msg + "】.......");
    }
```

## 消息转换器

导入依赖

```xml
        <!--Jackon-->
        <dependency>
            <groupId>com.fasterxml.jackson.dataformat</groupId>
            <artifactId>jackson-dataformat-xml</artifactId>
        </dependency>
```

定义转换器

```java
    @Bean
    public MessageConverter jackonMessageConverter() {
        return new Jackson2JsonMessageConverter();
    }
```

## 业务改造

需求：改造余额支付功能，不再同步调用交易服务的OpenFeign接口，而是采用异步MQ通知交易服务更新订单状态

# MQ高级

## 消息可靠性问题

### 发送者的可靠性

#### 生产者重连

由于网络波动，可能会出现客户端连接MQ失败的情况。通过配置我们可以开启连接失败后的重连机制。

```yml
spring:
  rabbitmq:
    connection-timeout: 1s
    template:
      retry:
        enabled: true
```

`SpringAMQP`提供的重试机制是**阻塞式**的重试，也就是说多次重试等待的过程中，当前线程是被阻塞的，会影响业务性能。

如果对于业务性能有要求，建议**禁用**重试机制。如果一定要用，请合理配置等待时长和重试次数，也可以考虑使用异步线程来执行发送消息的代码

#### 生产者确认

RabbitMQ提供了**Publisher Confirm**和**Publisher Return**两种确认机制。开启确认机制后会返回确认消息给生产者。返回的结果有以下几种情况：

- 消息投递到了MQ，但是路由失败。此时会通过`PublisherReturn`返回路由异常原因，然后返回ACK，告知投递成功
- 临时消息投递到了MQ，并且入队成功，返回ACK，告知投递成功
- 持久消息投递到了MQ，并且入队完成持久化，返回ACK，告知投递成功
- 其他情况都会返回NACK，告知投递失败

![image-20241106115429064](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241106115429064.png)

1. 在publisher这个微服务的application.yml中添加配置：

```yml
spring:
  rabbitmq:
    publisher-confirm-type: correlated
    publisher-returns: true
```

配置说明：

publisher-comfirm-type有三种模式可选：

- none：关闭confirm机制
- simple：同步阻塞等待MQ的回执消息
- correlated：MQ异步回调方式返回回执消息

2. 每个`RabbitTemplate`只能配置`ReturnCallback`，因此需要在项目启动过程中配置：

```java
@Slf4j
@Configuration
public class CommonConfig implements ApplicationContextAware {

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        // 获取RabbitTemplate
        RabbitTemplate rabbitTemplate = applicationContext.getBean(RabbitTemplate.class);
        // 设置ReturnCallback
        rabbitTemplate.setReturnCallback((message, replyCode, replyText, exchange, routingKey) -> {
            log.info("消息发送失败, 应答码{}, 原因{}, 交换机{}, 路由键{},消息{}",
                    replyCode, replyText, exchange, routingKey, message.toString());
        });
    }
}
```

3. 发送消息，指定消息ID、消息`ConfirmCallback`

```java
@Test
void testPublisherConfirm() throws InterruptedException {
    // 1. 创建CorrelationData
    CorrelationData cd = new CorrelationData();
    // 2. 给Future添加ConfirmCallback
    cd.getFuture().addCallback(new ListenableFutureCallback<CorrelationData.Confirm>() {
        @Override
        public void onFailure(Throwable ex) {
            // 2.1.Future发生异常时的处理逻辑,基本不会触发
            log.error("handle message ack fail", ex);
        }
        
        @Override
        public void onSuccess(CorrelationData.Confirm result) {
            // 2.2.Future接收到回执的处理逻辑,参数中的result就是回执内容
            if(result.isAck()){ // result.isAck(), boolean类型, true代表ack回执, false 代表 nack回执
                log.debug("发送消息成功, 收到 ack!");
            }else{ // result.getReason(), String类型, 返回nack时的异常描述
                log.error("发送消息失败, 收到 nack, reason : {}", result.getReason());
            }
        }
    });
    // 3. 发送消息
    rabbitTemplate.convertAndSend("hmall.direct", "red1", "hello", cd);d
}
```

- 生产者确认需要额外的网络和系统资源开销，尽量不要使用
- 如果一定要用，无需开启`Publisher-Return`机制，因为一般路由失败是自己业务问题
- 对于**nack**消息可以有限次数重试，依然失败则记录异常消息

### MQ的可靠性

在默认情况下，RabbitMQ会将接收到的消息保存在内存中以降低消息收发的延迟。这样会导致两个问题：

- 一旦MQ宕机，内存中的消息会丢失
- 内存空间有限，当消费者故障或处理过慢时，会导致消息积压，引发MQ阻塞

#### 数据持久化

Rabbit MQ实现的数据持久化包括3个方面：

- 交换机持久化 **指定durable**
- 队列持久化  **指定durable**
- 消息持久化 **指定persistent**



#### LazyQueue

从RabbitMQ的3.6.0版本开始，就增加了Lazy Queue的概念，也就是惰性队列

惰性队列的特征如下：

- 接收到消息后直接存入磁盘而非内存（内存中只保留最近的消息，默认2048条）
- 消费者要消费消息时才会从磁盘中读取并加载到内存
- 支持数百万条的消息存储

在3.12版本之后，所有队列都是Lazy Queue模式，无法更改

定义queue形式

```java
@Bean
public Queue lazyQueue(){
    return QueueBuilder
            .durable("lazy.queue")
            .lazy() // 开启Lazy模式
            .build();
}
```

基于注解形式：

```java
@RabbitListener(queuesToDeclare = @Queue(
        name = "lazy.queue",
        durable = "true",
        arguments = @Argument(name = "x-queue-mode", value = "lazy")
))
public void listenLazyQueue(String msg){
    log.info("接收到 lazy.queue的消息: {}", msg);
}
```

- 开启持久化和生产者确认时，RabbitMQ只有在消息持久化完成后才会给生产者返回ACK回执。

### 消费者的可靠性

#### 消费者确认机制

为了确认消费者是否成功处理消息，RabbitMQ提供了消费者确认机制（Consumer Acknowledgement）。当消费者处理消息结束后，应该向RabbitMQ发送一个回执，告知RabbitMQ自己消息处理状态。回执有三种可选值：

- **ack**：成功处理消息，RabbitMQ从队列中删除消息
- **nack**：消息处理失败，RabbitMQ需要再次投递消息
- **reject**：消息处理失败并拒绝该消息，RabbitMQ从队列中删除该消息

![image-20241106152545586](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241106152545586.png)

SpringAMQP已经实现了消息确认功能。并允许我们通过配置文件选择ACK处理方式，有三种方式：

- none：不处理。即消息投递给消费者后立刻ack，消息会立刻从MQ删除。非常不安全，不建议使用
- manual：手动模式。需要自己在业务代码中调用api，发送ack和reject，存在业务入侵，但更灵活
- **auto**：**自动模式**。`SpringAMQP`利用AOP对我们的消息处理逻辑做了环绕增强，当业务正常执行时刻自动返回ack，当业务出现异常时，根据异常判断返回不同结果：
  - 如果业务异常，会自动返回nack
  - 如果是消息处理或校验异常，自动返回reject

#### 失败消息处理策略

在开启重试模式后，重试次数耗尽，如果消息依然失败，则需要有`MessageRecoverer`接口来处理，它包含三种不同的实现：

- `RejectAndDontRequeueRecoverer`：重试耗尽后，直接reject，丢弃消息。默认就是这种方式。
- `ImmediateRequeueMessageRecoverer`：重试耗尽后，返回`nack`，消息重新入队
- `RepublishMessageRecoverer`：重试耗尽后，将失败消息投递到指定的交换机

##### RepublishMessageRecoverer

多次重试耗尽后，将错误转发到`error.direct`交换机中

```java
@Configuration
@ConditionalOnProperty(prefix = "spring.rabbitmq.listener.simple.retry", name = "enabled", havingValue = "true")
public class ErrorConfiguration {
    @Bean
    public DirectExchange errorExchange(){
        return new DirectExchange("error.direct");
    }

    @Bean
    public Queue errorQueue(){
        return new Queue("error.queue");
    }

    @Bean
    public Binding errorBinding(Queue errorQueue, DirectExchange errorExchange){
        return BindingBuilder.bind(errorQueue).to(errorExchange).with("error");
    }

    @Bean
    public MessageRecoverer messageRecoverer(RabbitTemplate rabbitTemplate){
        return new RepublishMessageRecoverer(rabbitTemplate, "error.direct", "error");
    }
}
```

## 业务幂等性

幂等是一个数学概念，用函数表达式来描述：$f(x) = f(f(x))$。在程序开发中，则指同一个业务，执行多次对业务状态的影响是一致的。

- 查询和删除操作天生幂等

### 唯一消息Id

方案一，给每个消息都设置一个**唯一的id**，利用id区分是否是重复消息：

1. 每一条消息都生成一个唯一的id，与消息一起投递给消费者
2. 消费者接收到消息后处理自己的业务，业务处理成功后将消息ID保存到数据库中
3. 如果下次又收到相同的消息，去数据库查询判断是否存在，存在则为重复消息放弃处理

```java
    @Bean
    public MessageConverter jackonMessageConverter() {
        Jackson2JsonMessageConverter jjmc = new Jackson2JsonMessageConverter();
        jjmc.setCreateMessageIds(true);
        return jjmc;
    }
```

方案二，结合业务逻辑，基于业务本身做判断。

## 面试题

### 如果保证支付服务与交易服务之间的订单状态一致性？

- 首先，支付服务会正在用户支付成功以后利用MQ消息通知交易服务，完成订单状态同步。
- 其次，为了保证MQ消息的可靠性，我们采用了生产者确认机制、消费者确认、消费者失败重试等策略，确保消息投递和处理的可靠性。同时也开启了MQ的持久化，避免因服务宕机导致消息丢失。
- 最后，我们还在交易服务更新订单状态时做了业务幂等判断，避免了因为重复消费导致订单状态异常。

### 如果交易服务消息处理失败，有没有什么兜底方案？

- 我们可以在交易服务设置定时任务，定时查询订单支付状态。这样即便MQ通知失败，还可以利用定时任务作为兜底方案，确保订单支付状态的最终一致性。

## 延迟消息

**延迟消息**：生产者发送消息时指定一个时间，消费者不会立刻收到消息，而是在指定时间之后才收到消息

**延迟任务**：设置在一定时间之后才执行的任务

### 死信交换机

当一个队列中的消息满足下列情况之一时，就会成为**死信（dead letter）**：

- 消费者使用 `basic.reject` 或 `basic.nack` 声明消费失败，并且消息的requeue参数设置为false
- 消息是一个过期消息（达到了队列或消息本身设置的过期时间），超时无人消费
- 要投递的队列消息堆积满了，最早的消息可能称为死信

如果队列通过dead-letter-exchange属性制定了一个交换机，那么该队列中的死信就会投递到这个交换机中。这个交换机称为死信交换机（Dead Letter Exchange， 简称DLX）

### 延迟消息插件

