1. 如果application.yml和properties同时存在，且配相同的属性，最后还是看properties的

2. @Response注解的功能是：把该类的所有方法的返回值直接传到浏览器

3. 在做CRUD实验时，编辑页面跳转到时css和js样式丢失，原因是没有把他们的路径改为用thymeleaf表示

4. 在做编辑页面的时候，添加页面出错，原因是birth的数据格式出错

5. 在做编辑功能的时候出现POST不支持错误，原因是form标签里写的是href而不是action

6. jdbc的url写法：jdbc:mysql://192.168.189.128:3306/jdbc?serverTimezone=Asia/Shanghai&characterEncoding=utf8&useSSL=false

   相对应的maven坐标

   ````xml
   <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <version>8.0.11</version>
   </dependency>
   ````

   对应的driver

   ````yaml
   server:
     port: 31200
   spring:
     application:
       name: xc-service-manage-course
     datasource:
       druid:
   #      url: jdbc:mysql://localhost:3306/xc_course?characterEncoding=utf-8
         url: jdbc:mysql://localhost:3306/xc_course?serverTimezone=Asia/Shanghai&characterEncoding=utf8&useSSL=false
         username: root
         password: 123456
         driverClassName: com.mysql.cj.jdbc.Driver
         initialSize: 5  #初始建立连接数量
         minIdle: 5  #最小连接数量
         maxActive: 20 #最大连接数量
         maxWait: 10000  #获取连接最大等待时间，毫秒
         testOnBorrow: true #申请连接时检测连接是否有效
         testOnReturn: false #归还连接时检测连接是否有效
         timeBetweenEvictionRunsMillis: 60000 #配置间隔检测连接是否有效的时间（单位是毫秒）
         minEvictableIdleTimeMillis: 300000  #连接在连接池的最小生存时间（毫秒）
   ````

   
