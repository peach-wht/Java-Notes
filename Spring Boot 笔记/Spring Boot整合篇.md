# **一**、Spring Boot与缓存

## 1.JSR107

JSP-107是关于如何使用缓存的规范 ，其中 JSR是Java Specification Requests 的缩写 即 Java规范请求 

Java Caching定义了5个核心接口，分别是**CachingProvider, CacheManager, Cache, Entry 和 Expiry。**

### CachingProvider

定义了创建、配置、获取、管理和控制多个CacheManager。一个应用可以在运行期访问多个CachingProvide

### CacheManager

定义了创建、配置、获取、管理和控制多个唯一命名的Cache，这些Cache存在于CacheManager的上下文中。一个CacheManager仅被一个CachingProvider所拥有。

### Cache

是一个类似Map的数据结构并临时存储以Key为索引的值。一个Cache仅被一个CacheManager所拥有。

### Entry

是一个存储在Cache中的key-value对。

### Expiry

 每一个存储在Cache中的条目有一个定义的有效期。一旦超过这个时间，条目为过期的状态。一旦过期，条目将不可访问、更新和删除。缓存有效期可以通过ExpiryPolicy设置。

图示：

![image-20191113142828362](\images\image-20191113142828362.png)

## 2.Spring缓存抽象

Spring从3.1开始定义了org.springframework.cache.Cache
和org.springframework.cache.CacheManager接口来统一不同的缓存技术；
并支持使用JCache（JSR-107）注解简化我们开发；

每次调用需要缓存功能的方法时，Spring会检查检查指定参数的指定的目标方法是否已经被调用过；如果有就直接从缓存中获取方法调用后的结果，如果没有就调用方法并缓存结果后返回给用户。下次调用直接从缓存中获取。

图示：

![](\images\image-20191113143153609.png)



## 3.几个重要概念&缓存注解

| Cache              | 缓存接口，定义缓存操作。实现有：RedisCache、EhCacheCache、ConcurrentMapCache等 |
| ------------------ | ------------------------------------------------------------ |
| **CacheManager**   | 缓存管理器，管理各种缓存（Cache）组件                        |
| **@Cacheable**     | 主要针对方法配置，能够根据方法的请求参数对其结果进行缓存     |
| **@CacheEvict**    | **清空缓存**                                                 |
| **@CachePut**      | 保证方法被调用，又希望结果被缓存。                           |
| **@Caching**       | 定义复杂的缓存规则                                           |
| @CacheConfig       | 抽取缓存的公共配置，这样就不用每个注解都配相同重复的属性，用在类上，其他的用在方法上 |
| **@EnableCaching** | 开启基于注解的缓存                                           |
| keyGenerator       | 缓存数据时key生成策略                                        |
| **serialize**      | 缓存数据时value序列化策略                                    |

### @Cacheable/@CachePut/@CacheEvict 主要的参数

| **@Cacheable/@CachePut/@CacheEvict** **主要的参数**          |                                                              |
| ------------------------------------------------------------ | :----------------------------------------------------------- |
| **value**：缓存的名称，在 spring 配置文件中定义，必须指定至少一个 | 例如：@Cacheable(value=”mycache”) 或者 @Cacheable(value={”cache1”,”cache2”} |
| **key** ：缓存的 key，可以为空，如果指定要按照 SpEL 表达式编写，如果不指定，则缺省按照方法的所有参数进行组合 | 例如：@Cacheable(value=”testcache”,key=”#userName”)          |
| **condition**：缓存的条件，可以为空，使用 SpEL 编写，返回 true 或者 false，只有为 true 才进行缓存/清除缓存，在调用方法之前之后都能判断 | 例如：@Cacheable(value=”testcache”,condition=”#userName.length()>2”) |
| **allEntries**(**@CacheEvict** )：是否清空所有缓存内容，缺省为 false，如果指定为 true，则方法调用后将立即清空所有缓存 | 例如：@CachEvict(value=”testcache”,allEntries=true)          |
| **beforeInvocation(@CacheEvict)**：是否在方法执行前就清空，缺省为 false，如果指定为 true，则在方法还没有执行的时候就清空缓存，缺省情况下，如果方法执行抛出异常，则不会清空缓存 | 例如：@CachEvict(value=”testcache”，beforeInvocation=true)   |
| **unless(@CachePut)(@Cacheable)**：用于否决缓存的，不像condition，该表达式只在方法执行之后判断，此时可以拿到返回值result进行判断。条件为true不会缓存，fasle才缓存 | 例如：@Cacheable(value=”testcache”,unless=”#result == null”) |
| **keyGenerator**：key的生成器；可以自己指定key的生成器的组件id（可以使用默认的，也可以自己定义）     key/keyGenerator：二选一使用; |                                                              |

### Cache SpEL available metadata

| **名字**        | **位置**           | **描述**                                                     | **示例**             |
| --------------- | ------------------ | ------------------------------------------------------------ | -------------------- |
| methodName      | root object        | 当前被调用的方法名                                           | #root.methodName     |
| method          | root object        | 当前被调用的方法                                             | #root.method.name    |
| target          | root object        | 当前被调用的目标对象                                         | #root.target         |
| targetClass     | root object        | 当前被调用的目标对象类                                       | #root.targetClass    |
| args            | root object        | 当前被调用的方法的参数列表                                   | #root.args[0]        |
| caches          | root object        | 当前方法调用使用的缓存列表（如@Cacheable(value={"cache1", "cache2"})），则有两个cache | #root.caches[0].name |
| *argument name* | evaluation context | 方法参数的名字. 可以直接 #参数名 ，也可以使用 #p0或#a0 的形式，0代表参数的索引； | #iban 、 #a0 、 #p0  |
| result          | evaluation context | 方法执行后的返回值（仅当方法执行之后的判断有效，如‘unless’，’cache put’的表达式 ’cache evict’的表达式beforeInvocation=false） | #result              |





## 4.缓存的使用

​	1、引入spring-boot-starter-cache模块
​	2、@EnableCaching开启缓存
​	3、使用缓存注解
​	4、切换为其他缓存

### @Cacheable标注的方法执行的过程：

```
1、方法运行之前，先去查询Cache（缓存组件），按照cacheNames指定的名字获取；（CacheManager先获取相应的缓存），第一次获取缓存如果没有Cache组件会自动创建。
2、去Cache中查找缓存的内容，使用一个key，默认就是方法的参数；
key是按照某种策略生成的；默认是使用keyGenerator生成的，默认使用SimpleKeyGenerator生成key；
SimpleKeyGenerator生成key的默认策略；
	如果没有参数；key=new SimpleKey()；
	如果有一个参数：key=参数的值
	如果有多个参数：key=new SimpleKey(params)；
3、没有查到缓存就调用目标方法；
4、将目标方法返回的结果，放进缓存中
```

## 5.整合redis

​	1.引入spring-boot-starter-data-redis

​	2.application.yml配置redis连接地址

​	3.使用ReditTemplate操作redis
​		redisTemplate.opsForValue();//操作字符串
​		redisTemplate.opsForHash();//操作hash
​		redisTemplate.opsForList();//操作list
​		redisTemplate.opsForSet();//操作set
​		redisTemplate.opsForZSet();//操作有序set

​	4.配置缓存、CacheManagerCustomizers

​	5.测试使用缓存、切换缓存、 CompositeCacheManager

# 二、Spring Boot与消息

### 消息服务中两个重要概念

 消息代理（message broker）和目的地（destination）

当消息发送者发送消息以后，将由消息代理接管，消息代理保证消息传递到指定目的地。

#### 消息队列主要有两种形式的目的地

##### 队列（queue）：点对点消息通信（point-to-point）

​	1.消息发送者发送消息，消息代理将其放入一个队列中，消息接收者从队列中获取消息内容，消息读取后被移出队列
​	2.消息只有唯一的发送者和接受者，但并不是说只能有一个接收者

##### 主题（topic）：发布（publish）/订阅（subscribe）消息通信

​	发送者（发布者）发送消息到主题，多个接收者（订阅者）监听（订阅）这个主题，那么就会在消息到达时同时收到消息

#### 消息队列或消息中间件主要解决的问题：

​	1.异步处理

![image-20191113192444981](\images\image-20191113192444981.png)

​	2.应用解耦

![image-20191113192421524](\images\image-20191113192421524.png)

​	3.流量削峰

![image-20191113192401685](\images\image-20191113192401685.png)

## 两种消息代理的规范

### JMS（Java Message Service）JAVA消息服务：

​	基于JVM消息代理的规范。ActiveMQ、HornetMQ是JMS实现


### AMQP（Advanced Message Queuing Protocol）

​	高级消息队列协议，也是一个消息代理的规范，兼容JMS
​	RabbitMQ是AMQP的实现

|              | JMS                                                          | AMQP                                                         |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 定义         | Java api                                                     | 网络线级协议                                                 |
| 跨语言       | 否                                                           | 是                                                           |
| 跨平台       | 否                                                           | 是                                                           |
| Model        | 提供两种消息模型：（1）、Peer-2-Peer（2）、Pub/sub           | 提供了五种消息模型：（1）、direct exchange（2）、fanout exchange（3）、topic change（4）、headers exchange（5）、system exchange本质来讲，后四种和JMS的pub/sub模型没有太大差别，仅是在路由机制上做了更详细的划分； |
| 支持消息类型 | 多种消息类型：TextMessageMapMessageBytesMessageStreamMessageObjectMessageMessage （只有消息头和属性） | byte[]当实际应用时，有复杂的消息，可以将消息序列化后发送。   |
| 综合评价     | JMS 定义了JAVA API层面的标准；在java体系中，多个client均可以通过JMS进行交互，不需要应用修改代码，但是其对跨平台的支持较差； | AMQP定义了wire-level层的协议标准；天然具有跨平台、跨语言特性。 |



## RabbitMQ

RabbitMQ是一个由erlang开发的AMQP(Advanved Message Queue Protocol)的开源实现

### 核心概念

#### Message

消息，消息是不具名的，它由消息头和消息体组成。消息体是不透明的，而消息头则由一系列的可选属性组成，这些属性包括routing-key（路由键）、priority（相对于其他消息的优先权）、delivery-mode（指出该消息可能需要持久性存储）等。


#### Publisher

消息的生产者，也是一个向交换器发布消息的客户端应用程序。


#### Exchange

交换器，用来接收生产者发送的消息并将这些消息路由给服务器中的队列。
Exchange有4种类型：direct(默认)，fanout, topic, 和headers，不同类型的Exchange转发消息的策略有所区别

#### Queue

消息队列，用来保存消息直到发送给消费者。它是消息的容器，也是消息的终点。一个消息可投入一个或多个队列。消息一直在队列里面，等待消费者连接到这个队列将其取走。


#### Binding

绑定，用于消息队列和交换器之间的关联。一个绑定就是基于路由键将交换器和消息队列连接起来的路由规则，所以可以将交换器理解成一个由绑定构成的路由表。
Exchange 和Queue的绑定可以是多对多的关系。

#### Connection

网络连接，比如一个TCP连接。


#### Channel

信道，多路复用连接中的一条独立的双向数据流通道。信道是建立在真实的TCP连接内的虚拟连接，AMQP 命令都是通过信道发出去的，不管是发布消息、订阅队列还是接收消息，这些动作都是通过信道完成。因为对于操作系统来说建立和销毁 TCP 都是非常昂贵的开销，所以引入了信道的概念，以复用一条 TCP 连接。

#### Consumer

消息的消费者，表示一个从消息队列中取得消息的客户端应用程序。


#### Virtual Host

虚拟主机，表示一批交换器、消息队列和相关对象。虚拟主机是共享相同的身份认证和加密环境的独立服务器域。每个 vhost 本质上就是一个 mini 版的 RabbitMQ 服务器，拥有自己的队列、交换器、绑定和权限机制。vhost 是 AMQP 概念的基础，必须在连接时指定，RabbitMQ 默认的 vhost 是 / 。


#### Broker

表示消息队列服务器实体

![image-20191113164533858](images\image-20191113164533858.png)

![image-20191113165121375](images\image-20191113165121375.png)

### 关于Exchange 类型详解

#### direct：

完全匹配才进该消息队列，单播模式

![image-20191113165949425](images\image-20191113165949425.png)

#### fanout：

绑定所有消息队列，类似子网的广播，它是所有类型转发消息最快的。

![image-20191113170317550](images\image-20191113170317550.png)

#### topic：模糊匹配

topic 交换器通过模式匹配分配消息的路由键属性，将路由键和某个模式进行匹配，此时队列需要绑定到一个模式上。它将路由键和绑定键的字符串切分成单词，这些单词之间用点隔开。它同样也会识别两个通配符：符号“#”和符号“*”。#匹配0个或多个单词，*匹配一个单词。

![image-20191113170140068](images\image-20191113170140068.png)

## Spring Boot整合RabbitMQ

1.引入 spring-boot-starter-amqp
2.application.yml配置

```properties
spring.rabbitmq.host=118.24.44.169
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
```



3.测试RabbitMQ
	AmqpAdmin：管理组件
	RabbitTemplate：消息发送处理组件

### 自动配置的内容：  

1、RabbitAutoConfiguration
2、有自动配置了连接工厂ConnectionFactory
3、RabbitProperties 封装了 RabbitMQ的配置
4、 RabbitTemplate ：给RabbitMQ发送和接受消息；
5、 AmqpAdmin ： RabbitMQ系统管理功能组件;
AmqpAdmin：创建和删除 Queue，Exchange，Binding
6、@EnableRabbit +  @RabbitListener 监听消息队列的内容

# 三、Spring Boot与检索

## 1.检索

我们的应用经常需要添加检索功能，开源的 ElasticSearch 是目前全文搜索引擎的首选。他可以快速的存储、搜索和分析海量数据。Spring Boot通过整合Spring Data ElasticSearch为我们提供了非常便捷的检索功能支持；

### ElasticSearch

Elasticsearch是一个分布式搜索服务，提供Restful API，底层基于Lucene，采用多shard（分片）的方式保证数据安全，并且提供自动resharding的功能，github等大型的站点也是采用了ElasticSearch作为其搜索服务，

### 重要概念

以 员工文档 的形式存储为例：一个文档代表一个员工数据。存储数据到 ElasticSearch 的行为叫做 索引 ，但在索引一个文档之前，需要确定将文档存储在哪里。
一个 ElasticSearch 集群可以 包含多个 索引 ，相应的每个索引可以包含多个 类型 。 这些不同的类型存储着多个 文档 ，每个文档又有 多个 属性 。

![image-20191113202913643](\images\image-20191113202913643.png)

## SpringBoot整合ElasticSearch

​	1.引入spring-boot-starter-data-elasticsearch
​	2.安装Spring Data 对应版本的ElasticSearch
​	3.application.yml配置
​	4.Spring Boot自动配置的
​		ElasticsearchRepository、ElasticsearchTemplate、Jest
​	5.测试ElasticSearch

```java
/**
 * SpringBoot默认支持两种技术来和ES交互；
 * 1、Jest（默认不生效）
 * 	需要导入jest的工具包（io.searchbox.client.JestClient）
 * 2、SpringData ElasticSearch【ES版本有可能不合适】
 * 		版本适配说明：https://github.com/spring-projects/spring-data-elasticsearch
 *		如果版本不适配：2.4.6
 *			1）、升级SpringBoot版本
 *			2）、安装对应版本的ES
 *
 * 		1）、Client 节点信息clusterNodes；clusterName
 * 		2）、ElasticsearchTemplate 操作es
 *		3）、编写一个 ElasticsearchRepository 的子接口来操作ES；
 *	两种用法：https://github.com/spring-projects/spring-data-elasticsearch
 *	1）、编写一个 ElasticsearchRepository
 */
```

# 四、Spring Boot与任务

## 1.异步任务

在Java应用中，绝大多数情况下都是通过同步的方式来实现交互处理的；但是在处理与第三方系统交互的时候，容易造成响应迟缓的情况，之前大部分都是使用多线程来完成此类任务，其实，在Spring 3.x之后，就已经内置了@Async来完美解决这个问题。

两个注解：
@EnableAysnc、@Aysnc



## 2.定时任务

项目开发中经常需要执行一些定时任务，比如需要在每天凌晨时候，分析一次前一天的日志信息。Spring为我们提供了异步执行任务调度的方式，提供TaskExecutor 、TaskScheduler 接口。
两个注解：@EnableScheduling、@Scheduled
cron表达式：

| **字段** | **允许值**            | **允许的特殊字符** |
| -------- | --------------------- | ------------------ |
| 秒       | 0-59                  | , - * /            |
| 分       | 0-59                  | , - * /            |
| 小时     | 0-23                  | , - * /            |
| 日期     | 1-31                  | , - * ? / L W C    |
| 月份     | 1-12                  | , - * /            |
| 星期     | 0-7或SUN-SAT 0,7是SUN | , - * ? / L C #    |

| **特殊字符** | **代表含义**               |
| ------------ | -------------------------- |
| ,            | 枚举                       |
| -            | 区间                       |
| *            | 任意                       |
| /            | 步长                       |
| ?            | 日/星期冲突匹配            |
| L            | 最后                       |
| W            | 工作日                     |
| C            | 和calendar联系后计算过的值 |
| #            | 星期，4#2，第2个星期四     |

## 3.邮件任务

1.邮件发送需要引入spring-boot-starter-mail
2.Spring Boot 自动配置MailSenderAutoConfiguration
3.定义MailProperties内容，配置在application.yml中

```properties
spring.mail.username=534096094@qq.com
spring.mail.password=gtstkoszjelabijb
spring.mail.host=smtp.qq.com
spring.mail.properties.mail.smtp.ssl.enable=true
```



4.自动装配JavaMailSender

```java
@Autowired
	JavaMailSenderImpl mailSender;

	@Test
	public void contextLoads() {
		SimpleMailMessage message = new SimpleMailMessage();
		//邮件设置
		message.setSubject("通知-今晚开会");
		message.setText("今晚7:30开会");

		message.setTo("17512080612@163.com");
		message.setFrom("534096094@qq.com");

		mailSender.send(message);
	}
```



5..测试邮件发送

# 五、Spring Boot与安全

Spring Security是针对Spring项目的安全框架，也是Spring Boot底层安全模块默认的技术选型。他可以实现强大的web安全控制。对于安全控制，我们仅需引入spring-boot-starter-security模块，进行少量的配置，即可实现强大的安全管理。

应用程序的两个主要区域是“认证”和“授权”（或者访问控制）。这两个主要区域是Spring Security 的两个目标。

### 几个重要的类：

WebSecurityConfigurerAdapter：自定义Security策略
AuthenticationManagerBuilder：自定义认证策略
@EnableWebSecurity：开启WebSecurity模式

### “认证”（Authentication）

“认证”（Authentication），是建立一个他声明的主体的过程（一个“主体”一般是指用户，设备或一些可以在你的应用程序中执行动作的其他系统）。

### “授权”（Authorization）

“授权”（Authorization）指确定一个主体是否允许在你的应用程序执行一个动作的过程。为了抵达需要授权的店，主体的身份已经有认证过程建立。

### Web&安全 

#### 1.登陆/注销

​	HttpSecurity配置登陆、注销功能

#### 2.Thymeleaf提供的SpringSecurity标签支持

​	需要引入thymeleaf-extras-springsecurity4
​	sec:authentication=“name”获得当前用户的用户名
​	sec:authorize=“hasRole(‘ADMIN’)”当前用户必须拥有ADMIN权限时才会显示标签内容

#### 3.remember me

​	表单添加remember-me的checkbox
​	配置启用remember-me功能

#### 4.CSRF（Cross-site request forgery）跨站请求伪造

​	HttpSecurity启用csrf功能，会为表单添加_csrf的值，提交携带来预防CSRF；

## SpringBoot整合SpringSecurity

### 1.引入依赖

````xml
		<dependency>
			<groupId>org.thymeleaf.extras</groupId>
			<artifactId>thymeleaf-extras-springsecurity4</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>
		<dependency>
````

注意thymeleaf和layout的版本匹配

### 2.编写MySecurityConfig类

继承 WebSecurityConfigurerAdapter,开启@EnableWebSecurity

```java
@EnableWebSecurity
public class MySecurityConfig extends WebSecurityConfigurerAdapter 
```

### 3.定制请求的授权规则

重写 configure(HttpSecurity http)

```java
 protected void configure(HttpSecurity http) throws Exception {
        //super.configure(http);
        //定制请求的授权规则
        http.authorizeRequests().antMatchers("/").permitAll()
                .antMatchers("/level1/**").hasRole("VIP1")
                .antMatchers("/level2/**").hasRole("VIP2")
                .antMatchers("/level3/**").hasRole("VIP3");

        //开启自动配置的登陆功能，效果，如果没有登陆，没有权限就会来到登陆页面
        http.formLogin().usernameParameter("user").passwordParameter("pwd")
                .loginPage("/userlogin");
        //1、/login来到登陆页
        //2、重定向到/login?error表示登陆失败
        //3、更多详细规定
        //4、默认post形式的 /login代表处理登陆
        //5、一但定制loginPage；那么 loginPage的post请求就是登陆


        //开启自动配置的注销功能。
        http.logout().logoutSuccessUrl("/");//注销成功以后来到首页
        //1、访问 /logout 表示用户注销，清空session
        //2、注销成功会返回 /login?logout 页面；

        //开启记住我功能
        http.rememberMe().rememberMeParameter("remeber");
        //登陆成功以后，将cookie发给浏览器保存，以后访问页面带上这个cookie，只要通过检查就可以免登录
        //点击注销会删除cookie

    }
```

### 4.定制认证规则

````java
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //super.configure(auth);
        auth.inMemoryAuthentication()
                .withUser("zhangsan").password("123456").roles("VIP1","VIP2")
                .and()
                .withUser("lisi").password("123456").roles("VIP2","VIP3")
                .and()
                .withUser("wangwu").password("123456").roles("VIP1","VIP3");

    }
````

### 5.对thymeleaf页面进行修改，按照角色显示功能

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
	  xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity4">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
<h1 align="center">欢迎光临武林秘籍管理系统</h1>
<div sec:authorize="!isAuthenticated()">
	<h2 align="center">游客您好，如果想查看武林秘籍 <a th:href="@{/userlogin}">请登录</a></h2>
</div>
<div sec:authorize="isAuthenticated()">
	<h2><span sec:authentication="name"></span>，您好,您的角色有：
		<span sec:authentication="principal.authorities"></span></h2>
	<form th:action="@{/logout}" method="post">
		<input type="submit" value="注销"/>
	</form>
</div>

<hr>

<div sec:authorize="hasRole('VIP1')">
	<h3>普通武功秘籍</h3>
	<ul>
		<li><a th:href="@{/level1/1}">罗汉拳</a></li>
		<li><a th:href="@{/level1/2}">武当长拳</a></li>
		<li><a th:href="@{/level1/3}">全真剑法</a></li>
	</ul>

</div>

<div sec:authorize="hasRole('VIP2')">
	<h3>高级武功秘籍</h3>
	<ul>
		<li><a th:href="@{/level2/1}">太极拳</a></li>
		<li><a th:href="@{/level2/2}">七伤拳</a></li>
		<li><a th:href="@{/level2/3}">梯云纵</a></li>
	</ul>

</div>

<div sec:authorize="hasRole('VIP3')">
	<h3>绝世武功秘籍</h3>
	<ul>
		<li><a th:href="@{/level3/1}">葵花宝典</a></li>
		<li><a th:href="@{/level3/2}">龟派气功</a></li>
		<li><a th:href="@{/level3/3}">独孤九剑</a></li>
	</ul>
</div>


</body>
</html>
```



### ！注意！：

 使用的是spring security自带的login页面，结果登陆的时候，用户名和密码正确也无法打开资源，还是停留在login页面。而且发现控制台报了异常——There is no PasswordEncoder mapped for the id “null”。 

![image-20191114111746454](\images\image-20191114111746454.png)

是因为spring5.0 中新增了多种加密方式，也改变了密码的格式 ，应修改为：

内存中取数据：

```java
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //inMemoryAuthentication 从内存中获取  
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder()).withUser("user1").password(new BCryptPasswordEncoder().encode("123456")).roles("USER");
}
```

数据库中取：

```java
auth.userDetailsService(userService).passwordEncoder(new BCryptPasswordEncoder());
```



# 六、Spring Boot与分布式

## 1.分布式应用

在分布式系统中，国内常用zookeeper+dubbo组合，而Spring Boot推荐使用全栈的Spring，Spring Boot+Spring Cloud。

### 架构的发展：

#### 1.单一应用架构

​	当网站流量很小时，只需一个应用，将所有功能都部署在一起，以减少部署节点和成本。此时，用于简化增删改查工作量的数据访问框架(ORM)是关键。

#### 2.垂直应用架构

​	当访问量逐渐增大，单一应用增加机器带来的加速度越来越小，将应用拆成互不相干的几个应用，以提升效率。此时，用于加速前端页面开发的Web框架(MVC)是关键。

#### 3..分布式服务架构

​	当垂直应用越来越多，应用之间交互不可避免，将核心业务抽取出来，作为独立的服务，逐渐形成稳定的服务中心，使前端应用能更快速的响应多变的市场需求。此时，用于提高业务复用及整合的分布式服务框架(RPC)是关键。

#### 4.流动计算架构

​	当服务越来越多，容量的评估，小服务资源的浪费等问题逐渐显现，此时需增加一个调度中心基于访问压力实时管理集群容量，提高集群利用率。此时，用于提高机器利用率的资源调度和治理中心(SOA)是关键。

![image-20191114145841842](\images\image-20191114145841842.png)

## 2.Zookeeper和Dubbo

#### ZooKeeper

ZooKeeper 是一个分布式的，开放源码的分布式应用程序协调服务。它是一个为分布式应用提供一致性服务的软件，提供的功能包括：配置维护、域名服务、分布式同步、组服务等。

#### Dubbo

Dubbo是Alibaba开源的分布式服务框架，它最大的特点是按照分层的方式来架构，使用这种方式可以使各个层之间解耦合（或者最大限度地松耦合）。从服务模型的角度来看，Dubbo采用的是一种非常简单的模型，要么是提供方提供服务，要么是消费方消费服务，所以基于这一点可以抽象出**服务提供方（Provider）**和**服务消费方（Consumer）**两个角色。

![image-20191114150108710](\images\image-20191114150108710.png)

## 3.springboot整合Dubbo

1、安装zookeeper作为注册中心

2、编写服务提供者

编写service接口

```java
public interface TicketService {

    public String getTicket();
}
```

编写接口实现类

````java
@Component
@Service //将服务发布出去
public class TicketServiceImpl implements TicketService {
    @Override
    public String getTicket() {
        return "《厉害了，我的国》";
    }
}
````

编写propretis连接zookeeper,注册到注册中心

```properties
dubbo.application.name=provider-ticket

dubbo.registry.address=zookeeper://118.24.44.169:2181

dubbo.scan.base-packages=com.atguigu.ticket.service
```

3、编写服务消费者

在同级目录下编写和提供者一样的service（听说可以在配置文件下配置，不知道咋做）

编写类接受

```java
@Service
public class UserService{

    @Reference
    TicketService ticketService;

    public void hello(){
        String ticket = ticketService.getTicket();
        System.out.println("买到票了："+ticket);
    }
}
```

```properties
dubbo.application.name=consumer-user

dubbo.registry.address=zookeeper://118.24.44.169:2181
```

4、整合dubbo





## 4.Spring Boot和Spring Cloud

Spring Cloud是一个分布式的整体解决方案。Spring Cloud 为开发者提供了在分布式系统（配置管理，服务发现，熔断，路由，微代理，控制总线，一次性token，全局琐，leader选举，分布式session，集群状态）中快速构建的工具，使用Spring Cloud的开发者可以快速的启动服务或构建应用、同时能够快速和云平台资源进行对接。

### SpringCloud分布式开发五大常用组件

​	服务发现——Netflix Eureka(注册中心)
​	客服端负载均衡——Netflix Ribbon
​	断路器——Netflix Hystrix
​	服务网关——Netflix Zuul
​	分布式配置——Spring Cloud Config

### springcloud入门

​	1、创建provider

controller

```java
@RestController
public class TicketController {


    @Autowired
    TicketService ticketService;

    @GetMapping("/ticket")
    public String getTicket(){
        return ticketService.getTicket();
    }
}

```

service

```java
@Service
public class TicketService {

    public String getTicket(){
        System.out.println("8002");
        return "《厉害了，我的国》";
    }
}
```

yml

```yaml
server:
  port: 8002
spring:
  application:
    name: provider-ticket


eureka:
  instance:
    prefer-ip-address: true # 注册服务的时候使用服务的ip地址
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/

```



​	2、创建consumer

```java
@RestController
public class UserController {

    @Autowired
    RestTemplate restTemplate;

    @GetMapping("/buy")
    public String buyTicket(String name){
        String s = restTemplate.getForObject("http://PROVIDER-TICKET/ticket", String.class);
        return name+"购买了"+s;
    }
}
```

SpringBoot主配置类

```java

@EnableDiscoveryClient //开启发现服务功能
@SpringBootApplication
public class ConsumerUserApplication {

	public static void main(String[] args) {
		SpringApplication.run(ConsumerUserApplication.class, args);
	}

	@LoadBalanced //使用负载均衡机制
	@Bean
	public RestTemplate restTemplate(){
		return new RestTemplate();
	}
}
```

```yaml
spring:
  application:
    name: consumer-user
server:
  port: 8200

eureka:
  instance:
    prefer-ip-address: true # 注册服务的时候使用服务的ip地址
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/

```



​	3、引入Spring Cloud

​	4、引入Eureka注册中心

```java
/**
 * 注册中心
 * 1、配置Eureka信息
 * 2、@EnableEurekaServer
 */
@EnableEurekaServer
@SpringBootApplication
public class EurekaServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(EurekaServerApplication.class, args);
	}
}

```



```yaml
server:
  port: 8761
eureka:
  instance:
    hostname: eureka-server  # eureka实例的主机名
  client:
    register-with-eureka: false #不把自己注册到eureka上
    fetch-registry: false #不从eureka上来获取服务的注册信息
    service-url:
      defaultZone: http://localhost:8761/eureka/
```



​	5、引入Ribbon进行客户端负载均衡

```java
@LoadBalanced //使用负载均衡机制
	@Bean
	public RestTemplate restTemplate(){
		return new RestTemplate();
	}
}
```

# 七、Spring Boot与开发热部署

## 热部署

1、模板引擎
在Spring Boot中开发情况下禁用模板引擎的cache
页面模板改变ctrl+F9可以重新编译当前页面并生效

2、Spring Loaded(太麻烦)
Spring官方提供的热部署程序，实现修改类文件的热部署
下载Spring Loaded（项目地址https://github.com/spring-projects/spring-loaded）
添加运行时参数；
-javaagent:C:/springloaded-1.2.5.RELEASE.jar –noverify

3、JRebel（收费）
收费的一个热部署软件
安装插件使用即可

Spring Boot Devtools（推荐）
引入依赖

````xml
<dependency>  
       <groupId>org.springframework.boot</groupId>  
       <artifactId>spring-boot-devtools</artifactId>   
</dependency> 

````

# 八、Spring Boot与监控管理

## 监控管理

通过引入spring-boot-starter-actuator，可以使用Spring Boot为我们提供的准生产环境下的应用监控和管理功能。我们可以通过HTTP，JMX，SSH协议来进行操作，自动得到审计、健康及指标信息等

步骤：
引入spring-boot-starter-actuator
通过http方式访问监控端点
可进行shutdown（POST 提交，此端点默认关闭）

| **端点名**   | **描述**                    |
| ------------ | --------------------------- |
| *autoconfig* | 所有自动配置信息            |
| auditevents  | 审计事件                    |
| beans        | 所有Bean的信息              |
| configprops  | 所有配置属性                |
| dump         | 线程状态信息                |
| env          | 当前环境信息                |
| health       | 应用健康状况                |
| info         | 当前应用信息                |
| metrics      | 应用的各项指标              |
| mappings     | 应用@RequestMapping映射路径 |
| shutdown     | 关闭当前应用（默认关闭）    |
| trace        | 追踪信息（最新的http请求）  |