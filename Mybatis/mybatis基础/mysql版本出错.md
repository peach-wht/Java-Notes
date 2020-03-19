mysql驱动版本太低，导致错误

# com.mysql.jdbc.exceptions.jdbc4.MySQLNonTransientConnectionException: Could not create conn

这是我出现一个bug，老哥们用的是mysql8.0的就看看吧，项目报这个错误：
com.mysql.jdbc.exceptions.jdbc4.MySQLNonTransientConnectionException: Could not create conn
原因是驱动版本太低了，把

version 5.1.6 version

改成

version 8.0.11 version

然后再在修改driver和url为

```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/eesy?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT%2B8useSSL=false
jdbc.username=root
jdbc.password=123456
```

记得创建一个properties文件，不要直接在上面改，会出错，然后把文件导入SpringMapConfig.xml内使用

记得创建一个properties文件，不要直接在上面改，会出错，然后把文件导入SpringMapConfig.xml内使用

```xml
<properties resource="mysql.properties"></properties>
```

