## Mybatis快速入门案例

### 步骤：

#### mybatis的环境搭建

​		第一步：创建maven工程并导入坐标

```xml
<dependencies>
 <dependency>
 <groupId>org.mybatis</groupId>
 <artifactId>mybatis</artifactId>
 <version>3.4.5</version>
 </dependency>
 <dependency>
 <groupId>junit</groupId>
 <artifactId>junit</artifactId>
 <version>4.10</version>
 <scope>test</scope>
 </dependency>
 <dependency>
 <groupId>mysql</groupId>
 <artifactId>mysql-connector-java</artifactId>
 <version>5.1.6</version>
 <scope>runtime</scope>
 </dependency>
 <dependency>
 <groupId>log4j</groupId>
 <artifactId>log4j</artifactId>
 <version>1.2.12</version>
 </dependency>
 </dependencies>
```

​		第二步：创建实体类和dao的接口
​		第三步：创建Mybatis的主配置文件
​				SqlMapConifg.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration 
 PUBLIC "-//mybatis.org//DTD Config 3.0//EN" 
 "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
<!-- 配置 mybatis 的环境 -->
<environments default="mysql">
<!-- 配置 mysql 的环境 -->
<environment id="mysql">
<!-- 配置事务的类型 -->
<transactionManager type="JDBC"></transactionManager>
<!-- 配置连接数据库的信息：用的是数据源(连接池) -->
<dataSource type="POOLED">
<property name="driver" value="com.mysql.jdbc.Driver"/>
<property name="url" value="jdbc:mysql://localhost:3306/ee50"/>
<property name="username" value="root"/>
<property name="password" value="1234"/>
</dataSource>
</environment>
</environments>
<!-- 告知 mybatis 映射配置的位置 -->
<mappers>
<mapper resource="com/itheima/dao/IUserDao.xml"/>
</mappers>
</configuration>
```

​		第四步：创建映射配置文件
​				IUserDao.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper 
 PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" 
 "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.itheima.dao.IUserDao">
<!-- 配置查询所有操作 -->
<select id="findAll" resultType="com.itheima.domain.User">
select * from user
</select>
```

​		第五步：编写测试类

```java
public class MybatisTest {
public static void main(String[] args)throws Exception {
    //1.读取配置文件
InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
//2.创建 SqlSessionFactory 的构建者对象
SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
//3.使用构建者创建工厂对象 SqlSessionFactory
SqlSessionFactory factory = builder.build(in);
//4.使用 SqlSessionFactory 生产 SqlSession 对象
SqlSession session = factory.openSession();
//5.使用 SqlSession 创建 dao 接口的代理对象
IUserDao userDao = session.getMapper(IUserDao.class);
//6.使用代理对象执行查询所有方法
List<User> users = userDao.findAll();
for(User user : users) {
System.out.println(user);
}
//7.释放资源
session.close();
in.close();
}
}
```



#### 环境搭建的注意事项：


		第一个：创建IUserDao.xml 和 IUserDao.java时名称是为了和我们之前的知识保持一致。
			在Mybatis中它把持久层的操作接口名称和映射文件也叫做：Mapper
			所以：IUserDao 和 IUserMapper是一样的
		第二个：在idea中创建目录的时候，它和包是不一样的
			包在创建时：com.itheima.dao它是三级结构
			目录在创建时：com.itheima.dao是一级目录
		第三个：mybatis的映射配置文件位置必须和dao接口的包结构相同
		第四个：映射配置文件的mapper标签namespace属性的取值必须是dao接口的全限定类名
		第五个：映射配置文件的操作配置（select），id属性的取值必须是dao接口的方法名
	
		当我们遵从了第三，四，五点之后，我们在开发中就无须再写dao的实现类。
#### mybatis的入门案例


		第一步：读取配置文件
		第二步：创建SqlSessionFactory工厂
		第三步：创建SqlSession
		第四步：创建Dao接口的代理对象
		第五步：执行dao中的方法
		第六步：释放资源
		注意事项：
			不要忘记在映射配置中告知mybatis要封装到哪个实体类中
			配置的方式：指定实体类的全限定类名
注意事项：
			不要忘记在映射配置中告知mybatis要封装到哪个实体类中
			配置的方式：指定实体类的全限定类名
	

#### mybatis基于注解的入门案例：	


	把IUserDao.xml移除，在dao接口的方法上使用@Select注解，并且指定SQL语句
	同时需要在SqlMapConfig.xml中的mapper配置时，使用class属性指定dao接口的全限定类名。
	明确：
		我们在实际开发中，都是越简便越好，所以都是采用不写dao实现类的方式。
		不管使用XML还是注解配置。
		但是Mybatis它是支持写dao实现类的。
