### SqlSessionFactoryBuilder、SqlSessionFactory解析

```java
 		//1.读取配置文件
        InputStream inputStream= Resources.getResourceAsStream("SqlMapConfig.xml");
        //2.创建SqlSessionFactory工厂
        SqlSessionFactoryBuilder builder=new SqlSessionFactoryBuilder();
        SqlSessionFactory factory=builder.build(inputStream);
        //3.使用工厂生产Sqlsession对象
        SqlSession sqlSession=factory.openSession();
        //4.使用SqlSession创建Dao接口的代理对象
        IUserDao userDao=sqlSession.getMapper(IUserDao.class);
        //5.使用代理对象执行方法
        List<User>users=userDao.findAll();
        for (User user:users) {
            System.out.println(user);
        }
        //6.释放资源
        sqlSession.close();
        inputStream.close();
```

#### SqlSessionFactoryBuilder：

```java
package org.apache.ibatis.session;

import java.io.IOException;
import java.io.InputStream;
import java.io.Reader;
import java.util.Properties;
import org.apache.ibatis.builder.xml.XMLConfigBuilder;
import org.apache.ibatis.exceptions.ExceptionFactory;
import org.apache.ibatis.executor.ErrorContext;
import org.apache.ibatis.session.defaults.DefaultSqlSessionFactory;

public class SqlSessionFactoryBuilder {

//方法1
  public SqlSessionFactory build(Reader reader) {
    return build(reader, null, null);
  }

//方法2
  public SqlSessionFactory build(Reader reader, String environment) {
    return build(reader, environment, null);
  }

//方法3
  public SqlSessionFactory build(Reader reader, Properties properties) {
    return build(reader, null, properties);
  }

//方法4
  public SqlSessionFactory build(Reader reader, String environment, Properties properties) {
    try {
      XMLConfigBuilder parser = new XMLConfigBuilder(reader, environment, properties);
      return build(parser.parse());
    } catch (Exception e) {
      throw ExceptionFactory.wrapException("Error building SqlSession.", e);
    } finally {
      ErrorContext.instance().reset();
      try {
        reader.close();
      } catch (IOException e) {
        // Intentionally ignore. Prefer previous error.
      }
    }
  }

//方法5
  public SqlSessionFactory build(InputStream inputStream) {
    return build(inputStream, null, null);
  }

//方法6
  public SqlSessionFactory build(InputStream inputStream, String environment) {
    return build(inputStream, environment, null);
  }

//方法7
  public SqlSessionFactory build(InputStream inputStream, Properties properties) {
    return build(inputStream, null, properties);
  }

//方法8
  public SqlSessionFactory build(InputStream inputStream, String environment, Properties properties) {
    try {
      XMLConfigBuilder parser = new XMLConfigBuilder(inputStream, environment, properties);
      return build(parser.parse());
    } catch (Exception e) {
      throw ExceptionFactory.wrapException("Error building SqlSession.", e);
    } finally {
      ErrorContext.instance().reset();
      try {
        inputStream.close();
      } catch (IOException e) {
        // Intentionally ignore. Prefer previous error.
      }
    }
  }
    
//方法9
  public SqlSessionFactory build(Configuration config) {
    return new DefaultSqlSessionFactory(config);
  }
```

由源码可知该类共有9个public方法，方法1、2、3最终都是调用了方法4；而方法5、6、7最终都是调用了方法8；而方法4和方法8都是根据传参创建了一个XMLConfiguration对象；

然后根据XMLConfiguration对象的parse方法创建了一个Configuration对象，然后都调用了方法9，所以该类的所有方法最终都是调用了方法9。而方法9是根据传入的Configuration参数新建了一个DefaultSqlSessionFactory对象返回；

很显然DefaultSqlSessionFactory是SqlSessionFactory接口的默认实现类（SqlSessionFactory还有一个SqlSessionManager实现类，后续继续了解）

SqlSessionFactoryBuilder根据mybatis的配置文件流创建Configuration对象，然后新建一个SqlSessionFactory的默认实现类DefaultSqlSessionFactory的对象

#### SqlSessionFactory：

```java
package org.apache.ibatis.session;

import java.sql.Connection;

/**
 * Creates an {@link SqlSession} out of a connection or a DataSource
 * 
 * @author Clinton Begin
 */
public interface SqlSessionFactory {

  SqlSession openSession();

  SqlSession openSession(boolean autoCommit);
  SqlSession openSession(Connection connection);
  SqlSession openSession(TransactionIsolationLevel level);

  SqlSession openSession(ExecutorType execType);
  SqlSession openSession(ExecutorType execType, boolean autoCommit);
  SqlSession openSession(ExecutorType execType, TransactionIsolationLevel level);
  SqlSession openSession(ExecutorType execType, Connection connection);

  Configuration getConfiguration();

}
```

SqlSessionFactory接口定义了8个创建SqlSession的接口，和一个获取Configuration，该接口的主要作用也就是创建SqlSession

可以看出这里面涉及到了几个参数：

autoCommit：数据库是否自动提交

Connection：数据库连接

TransactionIsolationLevel：数据库隔离级别

ExecutorType：执行器类型



#### 总结：

1.先获取mybatis的配置文件，解析成流对象（字符流和字节流都可以）Reader和InputStream都可以

2.通过SqlSessionFactoryBuilder根据mybatis的配置文件流创建一个Configuration（配置）对象

3.SqlSessionFactoryBuilder根据Configuration（配置）对象创建一个DefaultSqlSessionFactory（SqlSessionFactory的默认实现类）

4.DefaulatSqlSessionFacotry根据传入的参数，创建一个DefaultSqlSession对象（SqlSession的默认实现类）