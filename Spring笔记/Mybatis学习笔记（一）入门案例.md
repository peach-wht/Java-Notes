# Mybatis学习笔记（一）入门案例

## 一、环境搭建

1. Mybatis的环境搭建

    （数据库创建略过）

    第一步：创建Maven工程并导入坐标Pom.xml

        <?xml version="1.0" encoding="UTF-8"?>
        <project xmlns="http://maven.apache.org/POM/4.0.0"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
            <modelVersion>4.0.0</modelVersion>
        
            <groupId>com.greyson</groupId>
            <artifactId>day91_mybatis</artifactId>
            <version>1.0-SNAPSHOT</version>
            <packaging>jar</packaging>
        
            <dependencies>
                <dependency>
                    <groupId>org.mybatis</groupId>
                    <artifactId>mybatis</artifactId>
                    <version>3.4.1</version>
                </dependency>
        
                <dependency>
                    <groupId>mysql</groupId>
                    <artifactId>mysql-connector-java</artifactId>
                    <version>5.1.46</version>
                </dependency>
        
                <dependency>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                    <version>1.2.17</version>
                </dependency>
        
                <dependency>
                    <groupId>junit</groupId>
                    <artifactId>junit</artifactId>
                    <version>4.12</version>
                </dependency>
            </dependencies>
        
        </project>

    第二步：创建实体类和Dao的接口（略）

    第三步： 创建Mybatis的主配置文件 SqlMapConfig.xml

        <?xml version="1.0" encoding="UTF-8"?>
        <!DOCTYPE configuration
                PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
                "http://mybatis.org/dtd/mybatis-3-config.dtd">
        <configuration>
            <environments default="mysql">
                <!--configure mysql-->
                <environment id="mysql">
                    <!--configure transaction type-->
                    <transactionManager type="JDBC"></transactionManager>
                    <!--configure datasource-->
                    <dataSource type="POOLED">
                        <property name="driver" value="com.mysql.jdbc.Driver"/>
                        <property name="url" value="jdbc:mysql://localhost:3306/eesy"/>
                        <property name="username" value="root"/>
                        <property name="password" value="HotteMYSQL"/>
                    </dataSource>
                </environment>
            </environments>
        
            <!--Specified mapping location-->
            <mappers>
                <mapper resource="com/greyson/dao/IUserDao.xml"/>
            </mappers>
        </configuration>

    第四步：创建映射配置文件IUserDao.xml

        <?xml version="1.0" encoding="UTF-8"?>
        <!DOCTYPE mapper
                PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
        <mapper namespace="com.greyson.dao.IUserDao">
            <select id="findAll" resultType="com.greyson.domain.User">
                select * from user
            </select>
        </mapper>

2. 环境搭建的注意事项
    - Mybatis中把持久层的操作接口名称和映射文件都叫做：Mapper，所以 IUserDao 和 IUserMapper 是一样的
    - Mybatis 的映射配置文件位置必须和 dao 接口的包结构一致
    - 映射配置文件的 mapper 标签 namespace 属性的取值必须是 dao 接口的全限定类名
    - 映射配置文件的操作配置（select），id 属性的取值必须是 dao 接口的方法名

    遵从以上规则，开发中则无须再写 dao 的实现类。

3. 编写测试类MyBatisTest

        public class MybatisTest{
        
            /**
             * Getting started case
             * @param args
             */
            public static void main(String[] args) throws Exception {
                // 1. Read configuration file
                InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
                // 2. Create SqlSessionFactory
                SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
                SqlSessionFactory factory = builder.build(in);
                // 3. Use factory generate SqlSession Object
                SqlSession session = factory.openSession();
                // 4. Use SqlSession generate proxy object of DAO interface
                IUserDao userDao = session.getMapper(IUserDao.class);
                // 5. Use proxy object execution
                List<User> userList = userDao.findAll();
                for (User user : userList) {
                    System.out.println(user);
                }
                // 6. Release resources
                session.close();
                in.close();
        
            }
        }

4. 文件结构（补充）

    ![](./mybatis_png/1.png)

5. 测试结果

    ![](./mybatis_png/1.png)