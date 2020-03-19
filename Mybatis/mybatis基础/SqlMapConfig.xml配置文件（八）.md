##  SqlMapConfig.xml配置文件

### 	SqlMapConfig.xml 中配置的内容和顺序 

```
-properties（属性） 

	--property 

-settings（全局配置参数） 

	--setting 

-typeAliases（类型别名） 

	--typeAliase 

	--package 

-typeHandlers（类型处理器） 

-objectFactory（对象工厂） 

-plugins（插件） 

-environments（环境集合属性对象） 

	--environment（环境子属性对象） 

		---transactionManager（事务管理） 

		---dataSource（数据源） 

-mappers（映射器） 

	--mapper 

	--package 


```



### 	properties（属性） 

在使用 properties 标签配置时，我们可以采用两种方式指定属性配置。 

#### 第一种

直接在xml文件的properties属性下定义

#### 第二种

在 classpath 下定义 db.properties 文件 

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/eesy
jdbc.username=root
jdbc.password=1234
```

```xml
<properties resources="jdbcConfig.properties">
</properties>
```

```xml
<dataSource type="POOLED">
<property name="driver" value="${jdbc.driver}"/>
<property name="url" value="${jdbc.url}"/>
<property name="username" value="${jdbc.username}"/>
<property name="password" value="${jdbc.password}"/>
</dataSource>
```

### 	typeAliases（类型别名） 

前面我们所看到的integer之类的数据类型不用写包名，而我们自己定义的实体类就需要写全类名，因为mybatis把一些常用的数据类型起了默认的别名，我们也可以通过这个标签给我们的实体类起别名，减少代码量。

```xml
<typeAliases>
<!-- 单个别名定义（不再区分大小写） -->
<typeAlias alias="user" type="com.itheima.domain.User"/>
<!--上面的还是太麻烦，直接使用配置包名，指定后扫描整个包下的类，别名为类名（不再区分大小写） -->
<package name="com.itheima.domain"/>
<package name="其它包"/>
</typeAliases>
```

### 	mappers（映射器）

```xml
使用相对于类路径的资源
如：<mapper resource="com/itheima/dao/IUserDao.xml" />

使用 mapper 接口类路径
如：<mapper class="com.itheima.dao.UserDao"/>
注意：此种方法要求 mapper 接口名称和 mapper 映射文件名称相同，且放在同一个目录中。

注册指定包下的所有 mapper 接口
如：<package name="cn.itcast.mybatis.mapper"/>
注意：此种方法要求 mapper 接口名称和 mapper 映射文件名称相同，且放在同一个目录中。
```

### 额外知识：

```
url属性：
            是要求按照Url的写法来写地址
            URL：Uniform Resource Locator 统一资源定位符。它是可以唯一标识网络上一个资源的位置。
            它的写法：
                http://localhost:8080/mybatisserver/demo1Servlet
                协议      主机     端口       URI
```



            URI:Uniform Resource Identifier 统一资源标识符。它是在应用中可以唯一定位一个资源的。

