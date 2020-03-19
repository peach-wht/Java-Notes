##  Mybatis 连接池与事务深入

### 	Mybatis 的连接池技术

#### 	mybatis连接池提供了3种方式的配置：

​		配置的位置：
​			主配置文件SqlMapConfig.xml中的dataSource标签，type属性就是表示采用何种连接池方式。
​		type属性的取值：
​			**POOLED**	 采用传统的javax.sql.DataSource规范中的连接池，mybatis中有针对规范的实现
​			**UNPOOLED** 采用传统的获取连接的方式，虽然也实现Javax.sql.DataSource接口，但是并没有使用池的思想。
​			**JNDI**	 采用服务器提供的JNDI技术实现，来获取DataSource对象，不同的服务器所能拿到DataSource是不一样。
​				 注意：如果不是web或者maven的war工程，是不能使用的。
​				 我们课程中使用的是tomcat服务器，采用连接池就是dbcp连接池。

![image-20191025201045373](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\image-20191025201045373.png)

其实PoolDataSource也是用UnpoolDataSource来创建连接，PoolDataSource只是提供了缓存连接池机制

```xml
<dataSource type="POOLED">
<property name="driver" value="${jdbc.driver}"/>
<property name="url" value="${jdbc.url}"/>
<property name="username" value="${jdbc.username}"/>
<property name="password" value="${jdbc.password}"/>
</dataSource>
```

MyBatis 在初始化时，根据的 type 属性来创建相应类型的的数据源 DataSource，即： 

type=”POOLED”：MyBatis 会创建 PooledDataSource 实例 

type=”UNPOOLED” ： MyBatis 会创建 UnpooledDataSource 实例 

type=”JNDI”：MyBatis 会从 JNDI 服务上查找 DataSource 实例，然后返回使用 

![image-20191025202410179](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\image-20191025202410179.png)

关于上图总结：

##### POOLED方式的源码显示：

首先判断空闲池有没有可用连接，有则remove取一个出来

如果没有呢，那么再判断活动池里的连接数小于设置的最大值，那就创建一个出来，

如果为false，那么获取活动池中的最先创建的连接返回（如果该连接在用呢？）



**当我们需要创建 SqlSession 对象并需要执行 SQL 语句时，这时候 MyBatis 才会去调用 dataSource 对象** 

**来创建java.sql.Connection对象。也就是说，java.sql.Connection对象的创建一直延迟到执行SQL语句** 

**的时候。** 



### Mybatis 的事务控制

![image-20191025202748571](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\image-20191025202748571.png)

这是我们的 Connection 的整个变化过程，通过分析我们能够发现之前的 CUD 操作过程中，我们都要手动进 

行事务的提交，原因是 setAutoCommit()方法，在执行时它的值被设置为 false 了，所以我们在 CUD 操作中， 

必须通过 sqlSession.commit()方法来执行提交操作。 

**我们可以改为**：

session = **factory.openSession(true);**

![image-20191025202848112](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\image-20191025202848112.png)