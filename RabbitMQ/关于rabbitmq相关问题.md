## RabbitMQ

MQ全称为Message Queue，即消息队列， RabbitMQ是由erlang语言开发，基于AMQP（Advanced Message 

Queue 高级消息队列协议）协议实现的消息队列，它是一种应用程序之间的通信方法，消息队列在分布式系统开 

发中应用非常广泛

市场上还有哪些消息队列？ 

ActiveMQ，RabbitMQ，ZeroMQ，Kafka，MetaMQ，RocketMQ、Redis。 

## RabbitMQ的工作原理 

![image-20191126201335527](images\image-20191126201335527.png)

Broker：消息队列服务进程，此进程包括两个部分：Exchange和Queue。 

Exchange：消息队列交换机，按一定的规则将消息路由转发到某个队列，对消息进行过虑。

Queue：消息队列，存储消息的队列，消息到达队列并转发给指定的消费方。 

Producer：消息生产者，即生产方客户端，生产方客户端将消息发送到MQ。 

Consumer：消息消费者，即消费方客户端，接收MQ转发的消息。

connection：连接，通过ConnectionFactory创建

channel：通道

exchange：交换机

queue：消息队列

## 消息发布接收流程： 

### 发送消息

1、生产者和Broker建立TCP连接。 

2、生产者和Broker建立通道。 

3、生产者通过通道消息发送给Broker，由Exchange将消息进行转发。 

4、Exchange将消息转发到指定的Queue（队列） 

### 接收消息

1、消费者和Broker建立TCP连接 

2、消费者和Broker建立通道 

3、消费者监听指定的Queue（队列） 

4、当有消息到达Queue时Broker默认将消息推送给消费者。 

5、消费者接收到消息。 

## RabbitMQ的HelloWorld

### 生产者：

1、先引入依赖

```xml
<dependency> 
    <groupId>com.rabbitmq</groupId> 
    <artifactId>amqp‐client</artifactId> 
    <version>4.0.3</version><!‐‐此版本与spring boot 1.5.9版本匹配‐‐>
</dependency> 
<dependency> 
    <groupId>org.springframework.boot</groupId> 
    <artifactId>spring‐boot‐starter‐logging</artifactId>
</dependency>
```

2、在test里编写测试类

````java
    //队列
    private static final String QUEUE = "helloworld";

    public static void main(String[] args) {
        //通过连接工厂创建新的连接和mq建立连接
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("127.0.0.1");
        connectionFactory.setPort(5672);//端口
        connectionFactory.setUsername("guest");
        connectionFactory.setPassword("guest");
        //设置虚拟机，一个mq服务可以设置多个虚拟机，每个虚拟机就相当于一个独立的mq
        connectionFactory.setVirtualHost("/");

        Connection connection = null;
        Channel channel = null;
        try {
            //建立新连接
            connection = connectionFactory.newConnection();
            //创建会话通道,生产者和mq服务所有通信都在channel通道中完成
            channel = connection.createChannel();
            //声明队列，如果队列在mq 中没有则要创建
            //参数：String queue, boolean durable, boolean exclusive, boolean autoDelete, Map<String, Object> arguments
            /**
             * 参数明细
             * 1、queue 队列名称
             * 2、durable 是否持久化，如果持久化，mq重启后队列还在
             * 3、exclusive 是否独占连接，队列只允许在该连接中访问，如果connection连接关闭队列则自动删除,如果将此参数设置true可用于临时队列的创建
             * 4、autoDelete 自动删除，队列不再使用时是否自动删除此队列，如果将此参数和exclusive参数设置为true就可以实现临时队列（队列不用了就自动删除）
             * 5、arguments 参数，可以设置一个队列的扩展参数，比如：可设置存活时间
             */
            channel.queueDeclare(QUEUE,true,false,false,null);
            //发送消息
            //参数：String exchange, String routingKey, BasicProperties props, byte[] body
            /**
             * 参数明细：
             * 1、exchange，交换机，如果不指定将使用mq的默认交换机（设置为""）
             * 2、routingKey，路由key，交换机根据路由key来将消息转发到指定的队列，如果使用默认交换机，routingKey设置为队列的名称
             * 3、props，消息的属性
             * 4、body，消息内容
             */
            //消息内容
            String message = "hello world 黑马程序员";
            channel.basicPublish("",QUEUE,null,message.getBytes());
            System.out.println("send to mq "+message);
        } catch (Exception e) {
            e.printStackTrace();`
        } finally {
            //关闭连接
            //先关闭通道
            try {
                channel.close();
            } catch (IOException e) {
                e.printStackTrace();
            } catch (TimeoutException e) {
                e.printStackTrace();
            }
            try {
                connection.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }


    }
````



### 消费者：

```java
//队列
    private static final String QUEUE = "helloworld";

    public static void main(String[] args) throws Exception{
        //通过连接工厂创建新的连接和mq建立连接
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("127.0.0.1");
        connectionFactory.setPort(5672);//端口
        connectionFactory.setUsername("guest");
        connectionFactory.setPassword("guest");
        //设置虚拟机，一个mq服务可以设置多个虚拟机，每个虚拟机就相当于一个独立的mq
        connectionFactory.setVirtualHost("/");

        //建立新连接
        Connection connection = connectionFactory.newConnection();
        //创建会话通道,生产者和mq服务所有通信都在channel通道中完成
        Channel channel = connection.createChannel();

        //监听队列
        //声明队列，如果队列在mq 中没有则要创建

        channel.queueDeclare(QUEUE,true,false,false,null);

        //实现消费方法
        DefaultConsumer defaultConsumer = new DefaultConsumer(channel){

            /**
             * 当接收到消息后此方法将被调用
             * @param consumerTag  消费者标签，用来标识消费者的，在监听队列时设置channel.basicConsume
             * @param envelope 信封，通过envelope
             * @param properties 消息属性
             * @param body 消息内容
             * @throws IOException
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                //交换机
                String exchange = envelope.getExchange();
                //消息id，mq在channel中用来标识消息的id，可用于确认消息已接收
                long deliveryTag = envelope.getDeliveryTag();
                //消息内容
                String message= new String(body,"utf-8");
                System.out.println("receive message:"+message);
            }
        };

        //监听队列
        //参数：String queue, boolean autoAck, Consumer callback
        /**
         * 参数明细：
         * 1、queue 队列名称
         * 2、autoAck 自动回复，当消费者接收到消息后要告诉mq消息已接收，如果将此参数设置为tru表示会自动回复mq，如果设置为false要通过编程实现回复
         * 3、callback，消费方法，当消费者接收到消息要执行的方法
         */
        channel.basicConsume(QUEUE,true,defaultConsumer);
    }
```



## 总结

### 1、发送端操作流程 

1）创建连接 

2）创建通道 

3）声明队列 

4）发送消息 

### 2、接收端 

1）创建连接 

2）创建通道 

3）声明队列 

4）监听队列 

5）接收消息

6）ack回复

## RabbitMQ的工作模式

RabbitMQ有以下几种工作模式 ： 

1、Work queues （工作队列）

2、Publish/Subscribe （发布/订阅）

3、Routing （路由）

4、Topics （通配符）

5、Header （header转发器）

6、RPC（远程过程调用） 





### Work queues

![image-20191127101415940](images\image-20191127101415940.png)

work queues与入门程序相比，多了一个消费端，两个消费端共同消费同一个队列中的消息。 

应用场景：对于 任务过重或任务较多情况使用工作队列可以提高任务处理的速度。 

测试： 

1、使用入门程序，启动多个消费者。 

2、生产者发送多个消息。 

结果： 

1、一条消息只会被一个消费者接收； 

2、rabbit采用轮询的方式将消息是平均发送给消费者的； 

3、消费者在处理完某条消息后，才会收到下一条消息。

#### 注意：

**这个工作模式不是没有交换机，是因为会自动配置一个默认的交换机，所以不用自己配置**





### Publish/Subscribe 

 发布订阅模式：

![image-20191127101623022](images\image-20191127101623022.png)

 1、每个消费者监听自己的队列。 

2、生产者将消息发给broker，由交换机将消息转发到绑定此交换机的每个队列，每个绑定交换机的队列都将接收到消息  

#### 生产者

​	1、声明Exchange_fanout_inform交换机。 

​	2、声明两个队列并且绑定到此交换机，绑定时不需要指定routingkey 

​	3、发送消息时不需要指定routingkey

#### 发送消费者 

​	1、声明交换机

​	2、声明队列

​	3、交换机队列绑定

​	4、声明消费方法

​	5、监听队列

#### 关于交换机的类型

​	1、 fanout：对应的rabbitmq的工作模式是 publish/subscribe

​	2、direct：对应的Routing	工作模式

​	3、topic：对应的Topics工作模式

​	4、 headers： 对应的headers工作模式



#### 思考：publish/subscribe与work queues有什么区别。 

1）work queues不用定义交换机，而publish/subscribe需要定义交换机。 

2）publish/subscribe的生产方是面向交换机发送消息，work queues的生产方是面向队列发送消息(底层使用默认 

交换机)。 

3）publish/subscribe需要设置队列和交换机的绑定，work queues不需要设置，实质上work queues会将队列绑定到默认的交换机 。 

相同点： 

所以两者实现的发布/订阅的效果是一样的，多个消费端监听同一个队列不会重复消费消息。 

2、实质工作用什么 publish/subscribe还是work queues。 

建议使用 publish/subscribe，发布订阅模式比工作队列模式更强大，并且发布订阅模式可以指定自己专用的交换 

机。

### Routing

![image-20191127130911779](images\image-20191127130911779.png)

路由模式： 

1、每个消费者监听自己的队列，并且设置routingkey。 

2、生产者将消息发给交换机，由交换机根据routingkey来转发消息到指定的队列。 

#### 生产者

声明exchange_routing_inform交换机。 

声明两个队列并且绑定到此交换机，绑定时需要指定routingkey 

发送消息时需要指定routingkey 

#### 思考：Routing模式和Publish/subscibe有啥区别？ 

Routing模式要求队列在绑定交换机时要指定routingkey，消息会转发到符合routingkey的队列

### Topics

![image-20191127131654849](images\image-20191127131654849.png)

路由模式： 

1、每个消费者监听自己的队列，并且设置带统配符的routingkey。 

2、生产者将消息发给broker，由交换机根据routingkey来转发消息到指定的队列。 

#### 思考：本案例的需求使用Routing工作模式能否实现？ 

使用Routing模式也可以实现本案例，共设置三个 routingkey，分别是email、sms、all，email队列绑定email和 

all，sms队列绑定sms和all，这样就可以实现上边案例的功能，实现过程比topics复杂。 

Topic模式更多加强大，它可以实现Routing、publish/subscirbe模式的功能。 

### Header

header模式与routing不同的地方在于，header模式取消routingkey，使用header中的 key/value（键值对）匹配 队列。

### RPC 

RPC即客户端远程调用服务端的方法 ，使用MQ可以实现RPC的异步调用，基于Direct交换机实现，流程如下： 

1、客户端即是生产者就是消费者，向RPC请求队列发送RPC调用消息，同时监听RPC响应队列。 

2、服务端监听RPC请求队列的消息，收到消息后执行服务端的方法，得到方法返回的结果 

3、服务端将RPC方法 的结果发送到RPC响应队列 

4、客户端（RPC调用方）监听RPC响应队列，接收到RPC调用结果。 

## Rabbitmq整合springboot

### 生产者

1、引入依赖

2、编写application.yml

```yaml
server:
  port: 44000
spring:
  application:
    name: test-rabbitmq-producer
  rabbitmq:
    host: 127.0.0.1
    port: 5672
    username: guest
    password: guest
    virtualHost: /
```

3、编写rabbitmq的配置类RabbitMQCofig(主要是为了声明队列、交换机、和它们间的绑定)

```java
@Configuration
public class RabbitMQCofig {

    public static final String QUEUE_INFORM_EMAIL = "queue_inform_email";
    public static final String QUEUE_INFORM_SMS = "queue_inform_sms";
    public static final String EXCHANGE_TOPICS_INFORM="exchange_topics_inform";
    public static final String ROUTINGKEY_EMAIL="inform.#.email.#";
    public static final String ROUTINGKEY_SMS="inform.#.sms.#";

    //声明交换机
    @Bean(EXCHANGE_TOPICS_INFORM)
    public Exchange EXCHANGE_TOPICS_INFORM(){
        //durable(true) 持久化，mq重启之后交换机还在
        return ExchangeBuilder.topicExchange(EXCHANGE_TOPICS_INFORM).durable(true).build();
    }

    //声明QUEUE_INFORM_EMAIL队列
    @Bean(QUEUE_INFORM_EMAIL)
    public Queue QUEUE_INFORM_EMAIL(){
        return new Queue(QUEUE_INFORM_EMAIL);
    }
    //声明QUEUE_INFORM_SMS队列
    @Bean(QUEUE_INFORM_SMS)
    public Queue QUEUE_INFORM_SMS(){
        return new Queue(QUEUE_INFORM_SMS);
    }

    //ROUTINGKEY_EMAIL队列绑定交换机，指定routingKey
    @Bean
    public Binding BINDING_QUEUE_INFORM_EMAIL(@Qualifier(QUEUE_INFORM_EMAIL) Queue queue,
                                              @Qualifier(EXCHANGE_TOPICS_INFORM) Exchange exchange){
        return BindingBuilder.bind(queue).to(exchange).with(ROUTINGKEY_EMAIL).noargs();
    }
    //ROUTINGKEY_SMS队列绑定交换机，指定routingKey
    @Bean
    public Binding BINDING_ROUTINGKEY_SMS(@Qualifier(QUEUE_INFORM_SMS) Queue queue,
                                          @Qualifier(EXCHANGE_TOPICS_INFORM) Exchange exchange){
        return BindingBuilder.bind(queue).to(exchange).with(ROUTINGKEY_SMS).noargs();
    }
}


```

4、编写测试类（指定交换机、routingkey、和消息内容发送消息）

```java
    @Test
    public void testSendEmail(){

        String message = "send email message to user";
        /**
         * 参数：
         * 1、交换机名称
         * 2、routingKey
         * 3、消息内容
         */
        rabbitTemplate.convertAndSend(RabbitMQCofig.EXCHANGE_TOPICS_INFORM,"inform.email",message);

    }
```

### 消费者

和前面消费者一致

#### 注意：消费者也要进行声明队列、交换机、和它们间的绑定所以要配置配置类RabbitMQCofig

#### 编写测试类：

````java
@Component
public class ConsumerTest {
    @RabbitListener(queues = {RabbitMQCofig.QUEUE_INFORM_EMAIL})
    public void comsumerGet(Message message, String msg, Channel channel){
        System.out.println("来自"+channel+"的"+msg+"消息");
    }

}
````

#### 注意：

这里面的@Component一定要加，还有@RabbitListener也要标在方法上，注意它的参数