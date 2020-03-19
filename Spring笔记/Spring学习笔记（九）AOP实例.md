# Spring学习笔记（九）AOP实例

## 九、Spring中的AOP

### 1、AOP术语

- Advice (通知/增强): 所谓通知是指拦截到 Joinpoint 之后所要做的事情就是通知。 通知的类型：前置通知,后置通知,异常通知,最终通知,环绕通知。

- Joinpoint (连接点): 所谓连接点是指那些被拦截到的点。在 Spring 中,这些点指的是方法,因为 Spring 只支持方法类型的 连接点。

- Pointcut (切入点): 所谓切入点是指我们要对哪些 Joinpoint 进行拦截的定义。

- Introduction (引介): 引介是一种特殊的通知在不修改类代码的前提下, Introduction 可以在运行期为类动态地添加一些方 法或 Field。 

- Target(目标对象): 代理的目标对象。

- Weaving (织入): 是指把增强应用到目标对象来创建新的代理对象的过程。 Spring 采用动态代理织入，而 AspectJ 采用编译期织入和类装载期织入。

- Proxy（代理）: 一个类被 AOP 织入增强后，就产生一个结果代理类。 

- Aspec t(切面): 是切入点和通知（引介）的结合。

  #### 1.通知（Advice）

  　　就是你想要的功能，也就是上面说的 安全，事物，日志等。你给先定义好把，然后在想用的地方用一下。

  ####   2.连接点（JoinPoint）

  　　这个更好解释了，就是spring允许你使用通知的地方，那可真就多了，基本每个方法的前，后（两者都有也行），或抛出异常时都可以是连接点，spring只支持方法连接点.其他如aspectJ还可以让你在构造器或属性注入时都行，不过那不是咱关注的，只要记住，和方法有关的前前后后（抛出异常），都是连接点。

  ####   3.切入点（Pointcut）

  　　上面说的连接点的基础上，来定义切入点，你的一个类里，有15个方法，那就有几十个连接点了对把，但是你并不想在所有方法附近都使用通知（使用叫织入，以后再说），你只想让其中的几个，在调用这几个方法之前，之后或者抛出异常时干点什么，那么就用切点来定义这几个方法，让切点来筛选连接点，选中那几个你想要的方法。

  ####   4.切面（Aspect）

  　　切面是通知和切入点的结合。现在发现了吧，没连接点什么事情，连接点就是为了让你好**理解**切点，搞出来的，明白这个**概念**就行了。通知说明了干什么和什么时候干（什么时候通过方法名中的before,after，around等就能知道），而切入点说明了在哪干（指定到底是哪个方法），这就是一个完整的切面定义。

  ####   5.引入（introduction）

  　　允许我们向现有的类添加新方法属性。这不就是把切面（也就是新方法属性：通知定义的）用到目标类中吗

  ####   6.目标（target）

  　　引入中所提到的目标类，也就是要被通知的对象，也就是真正的业务逻辑，他可以在毫不知情的情况下，被咱们织入切面。而自己专注于业务本身的逻辑。

  ####   7.代理(proxy)

  　　怎么实现整套aop机制的，都是通过代理，这个一会给细说。

  ####   8.织入(weaving)

  　　把切面应用到目标对象来创建新的代理对象的过程。有3种方式，spring采用的是运行时，为什么是运行时，后面解释。

  　　关键就是：切点定义了哪些连接点会得到通知

### 2 、Spring中基于 xml 的 AOP 配置步骤

1. 把通知 Bean 也交给 Spring 来管理
2. 使用 aop : config 标签来表明开始 AOP 的设置
3. 使用 aop : aspect 标签配置切面
    - id 属性：是给切面提供一个唯一标识
    - ref 属性：是指定通知类 Bean 的 id
4. 在 aop : aspect 标签的内部使用对应标签来配置通知的类型
    1. aop : before 标识前置通知
        - method 属性：用于指定类中哪个方法是前置通知
        - pointcut 属性：用于指定切入点表达式，该切入点表达式指的是对业务层中哪些方法增强
    2. 切入点表达式的写法：
        - 关键字：execution ( 表达式 )
        - 表达式：
            - 标准写法：访问修饰符 + 返回值 + 包名.类名.方法名（参数列表）
            - 举例：public void com.greyson.service.impl.IAccountServiceImpl.saveAccount ( )
        - 全通配写法：`* * ..*.*(..)`
            - 访问修饰符可以省略
            - 返回值可以使用通配符，表示任意返回值
            - 包名可以使用通配符，表示任意包，但是有几级包就需要写几个 `*.`
            - 包名可以使用  `..` 表示当前包和子包
            - 类名和方法名都可以使用  `*` 来实现通配
            - 参数列表：
                - 可以直接使写数据类型：
                    - 基本类型直接写名称（如 int ）
                    - 引用类型写包名.类名的方式 （如 java.lang.String ）
                - 可以使用通配符表四任意类型，但是必须有参数
                - 可以使用 `..` 表示有无参数即可，有参数可以是任意类型
        - 实际开发中切入点表达式的通常写法：
            - 切到业务层类实现下的所有方法：`* com.greyson.service.impl.*.*(..)`
        - 配置切入点表达式（aop : pointcut）：
            - id属性用于指定表达式的唯一标识，expression属性用于指定表达式内容
            - 此标签写在 aop : aspect 标签内部只能当前切面使用，在其外部则所有切面可用
    3. Spring常用通知类型
        - 前置通知（aop : before）：在切入点方法执行之前执行
        - 后置通知（aop : after-returning）：在切入点方法正常执行之后执行，它和异常通知永远只能执行一个
        - 异常通知（aop : after-throwing）：在切入点方法执行产生异常之后执行，它和后置通知永远只能执行一个
        - 最终通知（aop : after）：无论切入点方法是否正常执行它都会在其后面执行
    4.  环绕通知

### 3、实例

1. 引入 Maven 工程
    - Pom.xml

        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <project xmlns="http://maven.apache.org/POM/4.0.0"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
            <modelVersion>4.0.0</modelVersion>
        
            <groupId>com.greyson</groupId>
            <artifactId>day03_SpringAOP</artifactId>
            <version>1.0-SNAPSHOT</version>
            <packaging>jar</packaging>
        
            <build>
                <finalName>webapp</finalName>
                <plugins>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-compiler-plugin</artifactId>
                        <version>3.6.0</version>
                        <configuration>
                            <source>1.8</source>
                            <target>1.8</target>
                        </configuration>
                    </plugin>
                </plugins>
            </build>
        
            <dependencies>
                <dependency>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring-context</artifactId>
                    <version>5.0.7.RELEASE</version>
                </dependency>
        
                <!--解析切入点表达式-->
                <dependency>
                    <groupId>org.aspectj</groupId>
                    <artifactId>aspectjweaver</artifactId>
                    <version>1.9.1</version>
                </dependency>
            </dependencies>
        ```
        


        ​    
    
            </project>

2. 编写业务代码
    - IAccountService

        ```java
        /**
         * 账户的业务层接口
         */
        public interface IAccountService {
            /**
             * 模拟保存账户
             *
             */
            void saveAccount();
        
            /**
             * 模拟更新账户
             * @param i
             */
            void updateAccount(int i);
        
            /**
             * 删除账户
             * @return
             */
            int deleteAccount();
    }
        ```

    - AccountServiceImpl
    
        ```java
        /**
         * 账户的业务层实现类
         */
        public class AccountServiceImpl implements IAccountService {
        
            @Override
            public void saveAccount() {
                System.out.println("执行了保存");
            }
        
            @Override
            public void updateAccount(int i) {
                System.out.println("执行了更新" + i);
            }
        
            @Override
            public int deleteAccount() {
                System.out.println("执行了删除");
            return 0;
            }
    }
        ```
    
    - Logger
    
        ```java
        /**
         * 用于记录日志的工具类，它里面提供了公共的代码
         */
    public class Logger {
            public void printLog() {
                System.out.println("lOGGER类中的printLog开始记录日志了。。。");
            }
        }
        ```
    
3. 配置Spring
    - bean.xml

        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xmlns:aop="http://www.springframework.org/schema/aop"
               xsi:schemaLocation="http://www.springframework.org/schema/beans
                http://www.springframework.org/schema/beans/spring-beans.xsd
                http://www.springframework.org/schema/aop
                http://www.springframework.org/schema/aop/spring-aop.xsd">
        
                <!-- 配置Spring的IOC,把Service对象配置进来-->
                <bean id="accountService" class="com.greyson.service.impl.AccountServiceImpl"></bean>
        
                <!--配置Logger类-->
                <bean id="logger" class="com.greyson.utils.Logger"></bean>
        
                <!--配置AOP-->
                <aop:config>
                        <!--配置切面-->
                        <aop:aspect id="logAdvice" ref="logger">
                                <!--配置通知的类型，并且建立通知方法和切入点方法的关联-->
                                <aop:before method="printLog" pointcut="execution(* com.greyson.service.impl.*.*(..))"></aop:before>
                        </aop:aspect>
                </aop:config>
    </beans>
        ```
    
4. 编写测试类
    - TestAOP

        ```java
        /**
         * 测试AOP的配置
         */
        public class TestAOP {
            public static void main(String[] args) {
                // 1. 获取容器
                ApplicationContext applicationContext = new ClassPathXmlApplicationContext("bean.xml");
                // 2. 获取对象
                IAccountService accountService = (IAccountService)applicationContext.getBean("accountService");
                // 3. 执行方法
                accountService.saveAccount();
                accountService.updateAccount(1);
                accountService.deleteAccount();
            }
        }
        ```

# [Spring学习笔记（十）JdbcTemplate](Spring学习笔记（十）JdbcTemplate.md)