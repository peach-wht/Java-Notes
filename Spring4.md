# Spring4

## Spring是什么

**轻量级：**

Spring 是非侵入性的 - 基于 Spring 开发的应用中的对象可以不依赖于 Spring 的 API
**依赖注入：**

(DI --- dependency injection、IOC)
**面向切面编程**

(AOP --- aspect oriented programming)
**容器:** 

Spring 是一个容器, 因为它包含并且管理应用对象的生命周期
**框架:**

 Spring 实现了使用简单的组件配置组合成一个复杂的应用. 在 Spring 中可以使用 XML 和 Java 注解组合这些对象
**一站式：**

在 IOC 和 AOP 的基础上可以整合各种企业应用的开源框架和优秀的第三方类库 （实际上 Spring 自身也提供了展现层的 SpringMVC 和 持久层的 Spring JDBC）

**简单来说：Spring 是一个 IOC(DI) 和 AOP 容器框架**

## IOC & DI 概述

IOC(Inversion of Control)：其思想是反转资源获取的方向. 传统的资源查找方式要求组件向容器发起请求查找资源. 作为回应, 容器适时的返回资源. 而应用了 IOC 之后, 则是容器主动地将资源推送给它所管理的组件, 组件所要做的仅是选择一种合适的方式来接受资源. 这种行为也被称为查找的被动形式
DI(Dependency Injection) — IOC 的另一种表述方式：即组件以一些预先定义好的方式(例如: setter 方法)接受来自如容器的资源注入. 相对于 IOC 而言，这种表述更直接

## 配置bean

### bean属性

#### id：

Bean 的名称。
在 IOC 容器中必须是**唯一**的
若 id 没有指定，Spring 自动将权限定性**类名**作为 Bean 的名字

#### class：

需要进行配置的类的位置

### Spring IOC 容器

#### BeanFactory:

 IOC 容器的基本实现.

#### ApplicationContext: 

提供了更多的高级特性. 是 BeanFactory 的子接口.

注意：BeanFactory 是 Spring 框架的基础设施，面向 Spring 本身；ApplicationContext **面向使用 Spring 框架的开发者**，几乎所有的应用场合都**直接使用 ApplicationContext** 而非底层的 BeanFactory

#### ApplicationContext 的主要实现类：

1. ClassPathXmlApplicationContext：

   从 类路径下加载配置文件

2. FileSystemXmlApplicationContext: 

   从文件系统中加载配置文件

![1570790221534](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570790221534.png)

### 依赖注入的方式

Class A中用到了Class B的对象b，一般情况下，需要在A的代码中显式的new一个B的对象。

采用依赖注入技术之后，A的代码只需要定义一个私有的B对象，不需要直接new来获得这个对象，而是通过相关的容器控制程序来将B对象在外部new出来并注入到A类里的引用中。而具体获取的方法、对象被获取时的状态由配置文件（如XML）来指定

属性注入
构造器注入
工厂方法注入（很少使用，不推荐）

#### 属性注入

**ps：命名得符合bean的命名规则，开头小写**

通过setter 方法注入Bean

属性注入使用 **<property>** 元素, 使用 name 属性指定 Bean 的属性名称，value 属性或 **<value>** 子节点指定属性值 

属性注入是实际应用中最常用的注入方式

![1570792276087](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570792276087.png)

#### 构造方法注入

通过构造方法注入Bean 的属性值或依赖的对象，它保证了 Bean 实例在实例化后就可以使用。
构造器注入在 <constructor-arg> 元素里声明属性, <constructor-arg> 中没有 name 属性

#### 引用其它 Bean

可以通过 <ref> 元素或 ref  属性为 Bean 的属性或构造器参数指定对 Bean 的引用.

#### 内部bean

不能被外部引用，不用写id

#### null值

可以使用专用的 <null/> 元素标签为 Bean 的字符串或其它对象类型的属性注入 null 值

Spring 支持级联属性的配置。

#### 集合属性

##### list

<list>

​	<ref value=""/>

</list>

map

![1570794850360](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570794850360.png)

### 自动装配

Spring IOC 容器可以自动装配 Bean. 需要做的仅仅是在 <bean> 的 autowire 属性里指定自动装配的模式
byType(根据类型自动装配): 若 IOC 容器中有多个与目标 Bean 类型一致的 Bean. 在这种情况下, Spring 将无法判定哪个 Bean 最合适该属性, 所以不能执行自动装配.
byName(根据名称自动装配): 必须将目标 Bean 的名称和属性名设置的完全相同.

### 继承 Bean 配置

Spring 允许继承 bean 的配置，子 Bean 从父 Bean 中继承配置, 包括 Bean 的属性配置
子 Bean 也可以覆盖从父 Bean 继承过来的配置

**把父 Bean 作为模板, 可以设置 <bean> 的abstract 属性为 true, 这样 Spring 将不会实例化这个 Bean**

### 依赖 Bean 配置

Spring 允许用户通过 depends-on 属性设定 Bean 前置依赖的Bean，前置依赖的 Bean 会在本 Bean 实例化之前创建好
如果前置依赖于多个 Bean，则可以通过逗号，空格或的方式配置 Bean 的名称

### Bean的作用域

在 Spring 中, 可以在 <bean> 元素的 scope 属性里设置 Bean 的作用域. 
默认情况下, Spring 只为每个在 IOC 容器里声明的 Bean 创建唯一一个实例, 整个 IOC 容器范围内都能共享该实例：所有后续的 getBean() 调用和 Bean 引用都将返回这个唯一的 Bean 实例.该作用域被称为 **singleton,** 它是所有 Bean 的默认作用域.

![1570797527514](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570797527514.png)

### 使用外部属性文件

Spring 提供了一个 PropertyPlaceholderConfigurer 的 BeanFactory 后置处理器, 这个处理器允许用户将 Bean 配置的部分内容外移到属性文件中. 可以在 Bean 配置文件里使用形式为 ${var} 的变量, 

![1570798309523](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570798309523.png)

### Spring表达式语言：SpEL

支持运行时查询和操作对象图的强大的表达式语言。

语法类似于 EL：SpEL 使用 #{…} 作为定界符，所有在大框号中的字符都将被认为是 SpEL

通过 SpEL 可以实现：

1. 通过 bean 的 id 对 bean 进行引用
2. 调用方法以及引用对象中的属性
3. 计算表达式的值
4. 正则表达式的匹配

![1570798574290](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570798574290.png)

![1570798595105](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570798595105.png)

![1570798610643](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570798610643.png)

![1570798630082](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570798630082.png)

![1570798645023](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570798645023.png)

### 管理bean的生命周期

#### Spring IOC 容器对 Bean 的生命周期进行管理的过程:

1. 通过构造器或工厂方法创建 Bean 实例

2. 为 Bean 的属性设置值和对其他 Bean 的引用

3. 调用 Bean 的初始化方法

4. Bean 可以使用了

5. 当容器关闭时, 调用 Bean 的销毁方法

   ps：在 Bean 的声明里设置 init-method 和 destroy-method 属性, 为 Bean 指定初始化和销毁方法

#### init-method:

初始化方法

#### destroy-method:

销毁方法

### Bean的后置处理器

**配置时，不需要配置id**

Bean 后置处理器允许在调用初始化方法前后对 Bean 进行额外的处理.
对Bean 后置处理器而言, 需要实现BeanPostProcessor接口. 在初始化方法被调用前后, Spring 将把每个 Bean 实例分别传递给上述接口的以下两个方法:

![1570850884228](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570850884228.png)

前者，是在init方法之前被调用，后者是init方法之后被调用

可以修改属性甚至返回新的bean。

### 静态工厂方法创建Bean

#### class属性：

指向静态工厂方法的全类名

#### factory-method：

指向工厂方法的方法名字

#### constructor-arg：

如果工厂方法需要传入参数，使用这个配置参数

### 实例工厂方法创建 Bean

#### factory-bean属性：

指向实例工厂方法的全类名

#### factory-method：

指向工厂方法的方法名字

#### constructor-arg：

如果工厂方法需要传入参数，使用这个配置参数

### 实现 FactoryBean 接口配置 Bean

Spring 中有两种类型的 Bean, 一种是普通Bean, 另一种是工厂Bean, 即FactoryBean. 

工厂 Bean 跟普通Bean不同, 其返回的对象不是指定类的一个实例, 其返回的是该工厂 Bean 的 getObject 方法所返回的对象 

![1570856405463](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570856405463.png)

### 基于注解的方式配置bean

特定组件包括:

#### @Component: 

基本注解, 标识了一个受 Spring 管理的组件

#### @Respository: 

标识持久层组件

#### @Service: 

标识服务层(业务层)组件

#### @Controller:

 标识表现层组件



#### 在 classpath 中扫描组件

首相得在文件中声明<context:component-scan>

##### **base-package：**

 属性指定一个需要扫描的基类包，Spring 容器将会扫描这个基类包里及其子包中的所有类. 
当需要扫描多个包时, 可以使用逗号分隔.

##### resource-pattern ：

如果仅希望扫描特定的类而非基包下的所有类，可使用 resource-pattern 属性过滤特定的类，示例

![1570858259703](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570858259703.png)

##### context:include-filter （需要配合user-default-filters=false使用）

子节点表示要包含的目标类

##### context:exclude-filter（需要配合user-default-filters=false使用）

子节点表示要排除在外的目标类

ps：<context:component-scan>下可以拥有若干个 <context:include-filter> 和 <context:exclude-filter> 子节点

<context:include-filter> 和 <context:exclude-filter> 子节点支持多种类型的过滤表达式：

![1570858544201](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570858544201.png)

###### annotation：

所有标注了XXXAnnotation的类，该类型采用目标类是否标注了某个注解进行过滤

###### assinable：

所有继承或扩展XXXService的类，该类型采用目标类是否继承或扩展某个特定类进行过滤

#### 组件装配

**context:component-scan**元素还会自动注册 AutowiredAnnotationBeanPostProcessor 实例, 该实例可以自动装配具有 @Autowired 和 @Resource 、@Inject注解的属性.

####  @Autowired

@Autowired 注解自动装配具有兼容类型的单个 Bean属性

1. 构造器, 普通字段(即使是非 public), 一切具有参数的方法都可以应用@Authwired 注解
2. 默认情况下, 所有使用 @Authwired 注解的属性都需要被设置. 当 Spring 找不到匹配的 Bean 装配属性时, 会抛出异常, **若某一属性允许不被设置, 可以设置 @Authwired 注解的 required 属性为 false**
3. 存在多个类型兼容的 Bean 时, 通过类型的自动装配将无法工作. 此时可以在 **@Qualifier** 注解里提供 Bean 的名称. Spring 允许对方法的入参标注 @Qualifiter 已指定注入 Bean 的名称

### 泛型依赖注入

Spring 4.x中可以为子类注入子类对应的泛型类型的成员变量的引用





## Spring AOP（面向切面）

AOP(Aspect-Oriented Programming, 面向切面编程): 是一种新的方法论, 是对传统 OOP(Object-Oriented Programming, 面向对象编程) 的补充.

AOP 的主要编程对象是切面(aspect), 而切面模块化横切关注点.

AOP 的好处:
每个事物逻辑位于一个位置, 代码不分散, 便于维护和升级
业务模块更简洁, 只包含核心业务代码.

### AOP术语

#### 切面（Aspect）：

 横切关注点(跨越应用程序多个模块的功能)被模块化的特殊对象

人话：就是你抽出来的重复很多次的代码部分

#### 通知（Advice）：

切面必须要完成的工作

人话：每个对象的方法

#### 目标（Target）：

被通知的对象

人话：核心业务逻辑，你需要把通知发送过去

#### 代理（Proxy）：

向目标对象应用通知之后创建的对象

#### 连接点（Joinpoint）：

程序执行的某个特定位置：如类某个方法调用前、调用后、方法抛出异常后等。**连接点由两个信息确定：方法表示的程序执行点；相对点表示的方位。**例如 ArithmethicCalculator#add() 方法执行前的连接点，执行点为 ArithmethicCalculator#add()； 方位为该方法执行前的位置

#### 切点（pointcut）：

每个类都拥有多个连接点：例如 ArithmethicCalculator 的所有方法实际上都是连接点，即**连接点是程序类中客观存在的**事务。AOP 通过切点定位到特定的连接点。**类比：连接点相当于数据库中的记录，切点相当于查询条件**。切点和连接点不是一对一的关系，**一个切点匹配多个连接点**，切点通过 org.springframework.aop.Pointcut 接口进行描述，它使用类和方法作为连接点的查询条件。

### AspectJ

Java 社区里最完整最流行的 AOP 框架.

要在 Spring IOC 容器中启用 AspectJ 注解支持, 只要在 Bean 配置文件中定义一个空的 XML 元素 <aop:aspectj-autoproxy>

**当 Spring IOC 容器侦测到 Bean 配置文件中的 <aop:aspectj-autoproxy> 元素时, 会自动为与 AspectJ 切面匹配的 Bean 创建代理.**

### 基于注解声明切面

#### @Aspect

**用这个注解在IOC容器声明**

在 AspectJ 注解中, 切面只是一个带有 @Aspect 注解的 Java 类. 

#### AspectJ 支持 5 种类型的通知注解: 

1. **@Before**: 前置通知, 在方法执行之前执行
2. **@After**: 后置通知, 在方法执行之后执行 
3. **@AfterRunning**: 返回通知, 在方法返回结果之后执行
4. **@AfterThrowing**: 异常通知, 在方法抛出异常之后
5. **@Around:** 环绕通知, 围绕着方法执行

格式：@Before("execution(*Arite.add(参数))");

这里的*表示任意修饰符和返回类型

##### 前置通知：

在方法执行之前执行的通知
前置通知使用 @Before 注解, 并将切入点表达式的值作为注解值.

![1570871715969](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570871715969.png)

##### 后置通知：

后置通知是在连接点完成之后执行的, 即连接点返回结果或者抛出异常的时候, 下面的后置通知记录了方法的终止. 

![1570871825249](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570871825249.png)

##### 返回通知：

无论连接点是正常返回还是抛出异常, 后置通知都会执行. 如果只想在连接点返回的时候记录日志, 应使用返回通知代替后置通知.

在返回通知中, 只要将 returning 属性添加到 @AfterReturning 注解中, 就可以访问连接点的返回值. 该属性的值即为用来传入返回值的参数名称. 
必须在通知方法的签名中添加一个同名参数. 在运行时, Spring AOP 会通过这个参数传递返回值.
原始的切点表达式需要出现在 pointcut 属性中

![1570871864386](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570871864386.png)

##### 异常通知：

只在连接点抛出异常时才执行异常通知
将 throwing 属性添加到 @AfterThrowing 注解中, 也可以访问连接点抛出的异常. Throwable 是所有错误和异常类的超类. 所以在异常通知方法可以捕获到任何错误和异常.
如果只对某种特殊的异常类型感兴趣, 可以将参数声明为其他异常的参数类型. 然后通知就只在抛出这个类型及其子类的异常时才被执行.

![1570871986589](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570871986589.png)

##### 环绕通知：

环绕通知是所有通知类型中功能最为强大的, 能够全面地控制连接点. 甚至可以控制是否执行连接点.
对于环绕通知来说, 连接点的参数类型必须是 ProceedingJoinPoint . 它是 JoinPoint 的子接口, 允许控制何时执行, 是否执行连接点.
在环绕通知中需要明确调用 ProceedingJoinPoint 的 proceed() 方法来执行被代理的方法. 如果忘记这样做就会导致通知被执行了, 但目标方法没有被执行.
**注意: 环绕通知的方法需要返回目标方法执行之后的结果, 即调用 joinPoint.proceed(); 的返回值, 否则会出现空指针异常**

![1570872073760](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570872073760.png)

#### 具体步骤：

1. 导入jar包
2. 在配置文件中加入aop命名空间
3. 基于注解方式实现编程
   1. 在配置文件下加入如下配置：
   2. 把横切关注点的代码抽象到切面的类中：
      1. 切面首先是一个IOC容器中的Bean即需要加入@Component注解
      2. 切面还需要加入@Aspect注解
   3. 在类中声明各种通知：
      1. 声明一个通知（方法）
      2. 在方法前加入@Before注解
   4. 可以在通知方法中声明一个类型为 JoinPoint 的参数. 然后就能访问链接细节. 如方法名称和参数值. 

### 指定切面优先级

在同一个连接点上应用不止一个切面时, 除非明确指定, 否则它们的优先级是不确定的.
切面的优先级可以通过实现 Ordered 接口或利用 **@Order** 注解指定.
**实现 Ordered 接口, getOrder() 方法的返回值越小, 优先级越高.**

**格式：Order(0);**

### 重用切入点定义

通过 @Pointcut 注解将一个切入点声明成简单的方法. 切入点的方法体通常是空的,

![1570878180428](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570878180428.png)

#### 步骤：

1. 定义一个方法，用于声明切入点表达式，一般的，方法中不需要添入其他代码
2. 使用@Pointcut来声明切入点表达式
3. 后面的其他通知直接使用方法名来引用当前的切面表达式

### 基于xml文件

#### 声明切面

当使用 XML 声明切面时, 需要在 <beans> 根元素中导入 aop Schema
在 Bean 配置文件中, 所有的 Spring AOP 配置都必须定义在 <aop:config> 元素内部. 对于每个切面而言, 都要创建一个 <aop:aspect> 元素来为具体的切面实现引用后端 Bean 实例. 
切面 Bean 必须有一个标示符, 供 <aop:aspect> 元素引用

#### 声明切入点

切入点使用 <aop:pointcut> 元素声明
切入点必须定义在 <aop:aspect> 元素下, 或者直接定义在 <aop:config> 元素下.
定义在 <aop:aspect> 元素下: 只对当前切面有效
定义在 <aop:config> 元素下: 对所有切面都有效
基于 XML 的 AOP 配置不允许在切入点表达式中用名称引用其他切入点

#### 声明通知

通知元素需要使用 <pointcut-ref> 来引用切入点, 或用 <pointcut> 直接嵌入切入点表达式.  method 属性指定切面类中通知方法的名称.

#### 声明引入

可以利用 <aop:declare-parents> 元素在切面内部声明引入



## Spring的事务管理

事务管理是企业级应用程序开发中必不可少的技术,  用来确保数据的完整性和一致性. 
事务就是一系列的动作, 它们被当做一个单独的工作单元. 这些动作要么全部完成, 要么全部不起作用

事务的四个属性：原子性，一致性，隔离性，持久性

#### Spring支持的事务管理方式：

1. 编程式的事务管理
2. 声明式的事务管理

编程式的代码过于繁杂，我们一般使用声明式事务管理

#### 步骤：

1. 配置事务管理

2. 启用事务注解

    <tx:annotation-driven> 

3. 在对应的方法上添加Transactional

### 事务的传播属性

当事务方法被另一个事务方法调用时, 必须指定事务应该如何传播. 例如: 方法可能继续在现有事务中运行, 也可能开启一个新事务, 并在自己的事务中运行.
事务的传播行为可以由传播属性指定. Spring 定义了 **7  种类传播行为.**

1. **REQERED(reqered)**

   **有事务就行，不新开**

   ![1570882131418](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570882131418.png)

2. **REQURED_NEW(requerd_new)**

   **新开一个事务，旧事务先挂起，新事务完了，再继续**

   ![1570882148955](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570882148955.png)

3. SUPPORTS(supports)

4. NOT_SUPPORTS(not_suppors)

5. MANDATORY(mandatory)

6. NEVER(never)

7. NESTED(nested)

![1570881428306](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570881428306.png)

### 格式：

**@Transactional(propagation=Propagation.REQUIRED)**

一般使用propagation

指定事务的传播行为，即当前的事务方法被另一个事务方法调用时，如何选择事务，是原来的，还是新的，默认为REQUIRED，即使用调用方法的事务

### 事务的隔离级别

### isolation指定事务的隔离级别

默认为READ_COMMITED

#### 并发会导致一系列的问题：

1. 脏读
2. 不可重复读
3. 幻读:对于两个事物 T1, T2, T1  从一个表中读取了一个字段, 然后 T2 在该表中插入了一些新的行. 之后, 如果 T1 再次读取同一个表, 就会多出几行.

#### Spring设置的事务隔离级别（isolation）

1. READ_UNCOMMITED
2. READ_COMMITED
3. REPEATABLE_READ
4. SERIALIZABLE



![1570882444213](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570882444213.png)

### 回滚机制（rollbackfor 和norollbackfor）

默认是对运行时异常进行回滚

 可以在 <tx:method> 元素中指定回滚规则. 如果有不止一种异常, 用逗号分隔

### 超时事务属性（timeout）:

 事务在强制回滚之前可以保持多久. 这样可以防止长期运行的事务占用资源.

### 只读事务属性（readOnly）: 

表示这个事务只读取数据但不更新数据, 这样可以帮助数据库引擎优化事务.