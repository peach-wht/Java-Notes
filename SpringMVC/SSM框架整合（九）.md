## 搭建整合环境

1. 整合说明：SSM整合可以使用多种方式，咱们会选择XML + 注解的方式 

2. 整合的思路 
   1. 先搭建整合的环境 
   2. 先把Spring的配置搭建完成 
   3. 再使用Spring整合SpringMVC框架 
   4. 最后使用Spring整合MyBatis框架 

3. 创建数据库和表结构 

4. 创建maven的工程（今天会使用到工程的聚合和拆分的概念，这个技术maven高级会讲，下面的只是大工程用到的模块，这次部署到一个模块里） 
   1. 创建ssm_parent父工程（打包方式选择pom，必须的） 
   2. 创建ssm_web子模块（打包方式是war包） 
   3. 创建ssm_service子模块（打包方式是jar包） 
   4. 创建ssm_dao子模块（打包方式是jar包） 
   5. 创建ssm_domain子模块（打包方式是jar包） 
   6. web依赖于service，service依赖于dao，dao依赖于domain 

   7. 在ssm_parent的pom.xml文件中引入坐标依赖 
   8. 部署ssm_web的项目，只要把ssm_web项目加入到tomcat服务器中即可 

5. 创建相关包和文档（dao，domain，service，controller）

## Spring框架代码的编写

1. ### 搭建和测试Spring的开发环境 
   1. 在ssm_web项目中创建applicationContext.xml的配置文件，编写具体的配置信息。 

      ```xml
       <!--开启注解的扫描，希望处理service和dao，controller不需要Spring框架去处理-->
          <context:component-scan base-package="cn.itcast" >
              <!--配置哪些注解不扫描-->
              <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller" />
          </context:component-scan>
      ```

      

   2. 在ssm_web项目中编写测试方法，进行测试

      ```java
       public void test1(){
              ApplicationContext ac=new ClassPathXmlApplicationContext("classpath:applicationContext.xml");
              AccountService accountService = (AccountService) ac.getBean("accountService");
              accountService.findAll();
          }
      ```

      

## Spring整合SpringMVC框架

### 搭建和测试SpringMVC的开发环境 
1. 在web.xml中配置DispatcherServlet前端控制器， 再配置DispatcherServlet过滤器解决中文乱码 

   ```xml
   <!--配置前端控制器-->
     <servlet>
       <servlet-name>dispatcherServlet</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!--加载springmvc.xml配置文件-->
       <init-param>
         <param-name>contextConfigLocation</param-name>
         <param-value>classpath:springmvc.xml</param-value>
       </init-param>
       <!--启动服务器，创建该servlet-->
       <load-on-startup>1</load-on-startup>
     </servlet>
     <servlet-mapping>
       <servlet-name>dispatcherServlet</servlet-name>
       <url-pattern>/</url-pattern>
     </servlet-mapping>
   
     <!--解决中文乱码的过滤器-->
     <filter>
       <filter-name>characterEncodingFilter</filter-name>
       <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
       <init-param>
         <param-name>encoding</param-name>
         <param-value>UTF-8</param-value>
       </init-param>
     </filter>
     <filter-mapping>
       <filter-name>characterEncodingFilter</filter-name>
       <url-pattern>/*</url-pattern>
     </filter-mapping>
   ```

2. 创建springmvc.xml的配置文件，编写配置文件 

   ```xml
   <!--开启注解扫描，只扫描Controller注解-->
       <context:component-scan base-package="cn.itcast">
           <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller" />
       </context:component-scan>
   
       <!--配置的视图解析器对象-->
       <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="prefix" value="/WEB-INF/pages/"/>
           <property name="suffix" value=".jsp"/>
       </bean>
   <!--开启SpringMVC注解的支持-->
       <mvc:annotation-driven/>
   ```

3. 测试是否成功

### Spring整合SpringMVC的框架

#### 关键问题：

在controller中怎么能成功的调用service对象中的方法，即当加载是springmvc.xml配置文件时，怎么能加载applicationContext.xml文件

#### 解决方法：

在web.xml中声明

```xml
  <!--配置Spring的监听器，默认只加载WEB-INF目录下的applicationContext.xml配置文件-->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>

```

这样只是加载类WEB-INF下的配置文件，那么就不便于管理，还要加上路径

```xml
  <!--设置配置文件的路径-->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
  </context-param>
```

 在controller中注入service对象，调用service对象的方法进行测试 

```java
@Controller
@RequestMapping("/account")
public class AccountController {

    @Autowired
    private AccountService accountService;

    @RequestMapping("/findAll")
    public String findAll(Model model){
        System.out.println("表现层：查询所有账户...");
        // 调用service的方法
        List<Account> list = accountService.findAll();
        model.addAttribute("list",list);
        return "list";
    }

```



## Spring整合MyBatis框架

###  搭建和测试MyBatis的环境 

1. 在web项目中编写SqlMapConfifig.xml的配置文件，编写核心配置文件 （这个可以不用管，最后是整合到applicationContext.xml中的）

2.  在AccountDao接口的方法上添加注解，编写SQL语句 

   ```java
   	// 查询所有账户
       @Select("select * from account")
       public List<Account> findAll();
   
       // 保存帐户信息
       @Insert("insert into account (name,money) values (#{name},#{money})")
       public void saveAccount(Account account);
   ```

   再编写测试类

### Spring整合MyBatis框架 

1. 把SqlMapConfifig.xml配置文件中的内容配置到applicationContext.xml配置文件中 

就是把mybatis的配置，包括创建连接池，创建SqlSession，获取Dao这些都写进applicationContext.xml配置文件中

```xml
    <!--配置连接池-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.cj.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/ssm?useSSL=false&amp;serverTimezone=UTC&amp;allowPublicKeyRetrieval=true"/>
        <property name="user" value="root"/>
        <property name="password" value="123456"/>
    </bean>

    <!--配置SqlSessionFactory工厂-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
    </bean>

    <!--配置AccountDao接口所在包-->
    <bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="cn.itcast.dao"/>
    </bean>

```

2. 在AccountDao接口中添加@Repository注解 

   ```java
   @Repository
   public interface AccountDao {
   
       // 查询所有账户
       @Select("select * from account")
       public List<Account> findAll();
   
       // 保存帐户信息
       @Insert("insert into account (name,money) values (#{name},#{money})")
       public void saveAccount(Account account);
   
   }
   
   ```

   

3. 在service中注入dao对象，进行测试 

   ```java
   @Service("accountService")
   public class AccountServiceImpl implements AccountService{
   
       @Autowired
       private AccountDao accountDao;
   
       public List<Account> findAll() {
           System.out.println("业务层：查询所有账户...");
           return accountDao.findAll();
       }
   
       public void saveAccount(Account account) {
           System.out.println("业务层：保存帐户...");
           accountDao.saveAccount(account);
       }
   }
   
   ```

4. 将service对象注入controller

   ```java
   @Controller
   @RequestMapping("/account")
   public class AccountController {
   
       @Autowired
       private AccountService accountService;
   
       @RequestMapping("/findAll")
       public String findAll(Model model){
           System.out.println("表现层：查询所有账户...");
           // 调用service的方法
           List<Account> list = accountService.findAll();
           model.addAttribute("list",list);
           return "list";
       }
   
   ```

   5. 配置Spring的声明式事务管理

      ```xml
      <!--配置Spring框架声明式事务管理-->
          <!--配置事务管理器-->
          <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
              <property name="dataSource" ref="dataSource" />
          </bean>
      
          <!--配置事务通知-->
          <tx:advice id="txAdvice" transaction-manager="transactionManager">
              <tx:attributes>
                  <tx:method name="find*" read-only="true"/>
                  <tx:method name="*" isolation="DEFAULT"/>
              </tx:attributes>
          </tx:advice>
      
          <!--配置AOP增强-->
          <aop:config>
              <aop:advisor advice-ref="txAdvice" pointcut="execution(* cn.itcast.service.impl.*ServiceImpl.*(..))"/>
          </aop:config>
      ```

      