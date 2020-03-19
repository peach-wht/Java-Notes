# Spring注解

## 容器

### 组件注册

配置类==配置文件
@Configuration  ：告诉Spring这是一个配置类

@Bean：给容器中注册一个Bean;类型为返回值的类型，id默认是用方法名作为id

@ComponentScans

```java
@ComponentScans(
		value = {
				@ComponentScan(value="com.atguigu",includeFilters = {
/*						@Filter(type=FilterType.ANNOTATION,classes={Controller.class}),
						@Filter(type=FilterType.ASSIGNABLE_TYPE,classes={BookService.class}),*/
						@Filter(type=FilterType.CUSTOM,classes={MyTypeFilter.class})
				},useDefaultFilters = false)	
		}
		)
//@ComponentScan  value:指定要扫描的包
//excludeFilters = Filter[] ：指定扫描的时候按照什么规则排除那些组件
//includeFilters = Filter[] ：指定扫描的时候只需要包含哪些组件,注意，要成立必须把默认扫描关掉useDefaultFilters = false

//FilterType.ANNOTATION：按照注解（最常用）
//FilterType.ASSIGNABLE_TYPE：按照给定的类型；
//FilterType.ASPECTJ：使用ASPECTJ表达式（最不常用）
//FilterType.REGEX：使用正则指定
//FilterType.CUSTOM：使用自定义规则（着重来说）
```

@Filter(type=FilterType.CUSTOM,classes={MyTypeFilter.class}

自定义过滤规则，其中自定义类必须实现TypeFilter接口

```java
public class MyTypeFilter implements TypeFilter {

	/**
	 * metadataReader：读取到的当前正在扫描的类的信息
	 * metadataReaderFactory:可以获取到其他任何类信息的
	 */
	@Override
	public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory)
			throws IOException {
		// TODO Auto-generated method stub
		//获取当前类注解的信息
		AnnotationMetadata annotationMetadata = metadataReader.getAnnotationMetadata();
		//获取当前正在扫描的类的类信息
		ClassMetadata classMetadata = metadataReader.getClassMetadata();
		//获取当前类资源（类的路径）
		Resource resource = metadataReader.getResource();
		//获取类的名字
		String className = classMetadata.getClassName();
		System.out.println("--->"+className);
        //只要名字包含er那么就返回true即加入容器
		if(className.contains("er")){
			return true;
		}
		return false;
	}

}

```

@Scope：调整作用域，放在类或方法前

```java
/* 
 * prototype：多实例的：ioc容器启动并不会去调用方法创建对象放在容器中。
 * 					每次获取的时候才会调用方法创建对象；
 * singleton：单实例的（默认值）：ioc容器启动会调用方法创建对象放到ioc容器中。
 * 			以后每次获取就是直接从容器（map.get()）中拿，
 * request：同一次请求创建一个实例
 * session：同一个session创建一个实例
 */
```
@Lazy：

懒加载：

单实例bean：默认在容器启动的时候创建对象；

懒加载：容器启动不创建对象。第一次使用(获取)Bean创建对象，并初始化；

**@Conditional({Condition}) ：** 

按照一定的条件进行判断，满足条件给容器中注册bean

可以放在类或方法上

```java

//判断是否linux系统
public class LinuxCondition implements Condition {//所有的条件判断类都必须实现condition接口

	/**
	 * ConditionContext：判断条件能使用的上下文（环境）
	 * AnnotatedTypeMetadata：注释信息
	 */
	@Override
	public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
		// TODO是否linux系统
		//1、能获取到ioc使用的beanfactory
		ConfigurableListableBeanFactory beanFactory = context.getBeanFactory();
		//2、获取类加载器
		ClassLoader classLoader = context.getClassLoader();
		//3、获取当前环境信息
		Environment environment = context.getEnvironment();
		//4、获取到bean定义的注册类
		BeanDefinitionRegistry registry = context.getRegistry();
		
		String property = environment.getProperty("os.name");
		
		//可以判断容器中的bean注册情况，也可以给容器中注册bean
		boolean definition = registry.containsBeanDefinition("person");
		if(property.contains("linux")){
			return true;
		}
		
		return false;
	}

}


```

ps:applicationContext.getEnvironment()获得运行环境





```java
/**
	 * 给容器中注册组件；
	 * 1）、包扫描+组件标注注解（@Controller/@Service/@Repository/@Component）[自己写的类]
	 * 2）、@Bean[导入的第三方包里面的组件]
	 * 3）、@Import[快速给容器中导入一个组件]
	 * 		1）、@Import(要导入到容器中的组件)；容器中就会自动注册这个组件，id默认是全类名
	 * 		2）、ImportSelector:返回需要导入的组件的全类名数组；
	 * 		3）、ImportBeanDefinitionRegistrar:手动注册bean到容器中
	 * 4）、使用Spring提供的 FactoryBean（工厂Bean）;
	 * 		1）、默认获取到的是工厂bean调用getObject创建的对象
	 * 		2）、要获取工厂Bean本身，我们需要给id前面加一个&
	 * 			&colorFactoryBean
	 */
```

@import:快速给容器中导入一个组件
	    	1）、@Import(要导入到容器中的组件)；容器中就会自动注册这个组件，id默认是全类名
			2）、ImportSelector:返回需要导入的组件的全类名数组；（SpringBoot常用）
	    	3）、ImportBeanDefinitionRegistrar:手动注册bean到容器中

ImportSelector:

```java
//自定义逻辑返回需要导入的组件
public class MyImportSelector implements ImportSelector {

	//返回值，就是到导入到容器中的组件全类名
	//AnnotationMetadata:当前标注@Import注解的类的所有注解信息
	@Override
	public String[] selectImports(AnnotationMetadata importingClassMetadata) {
		// TODO Auto-generated method stub
		//importingClassMetadata
		//方法不要返回null值
		return new String[]{"com.atguigu.bean.Blue","com.atguigu.bean.Yellow"};
	}

}

```

ImportBeanDefinitionRegister:

```java
public class MyImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {

	/**
	 * AnnotationMetadata：当前类的注解信息
	 * BeanDefinitionRegistry:BeanDefinition注册类；
	 * 		把所有需要添加到容器中的bean；调用
	 * 		BeanDefinitionRegistry.registerBeanDefinition手工注册进来
	 */
	@Override
	public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
		
		boolean definition = registry.containsBeanDefinition("com.atguigu.bean.Red");
		boolean definition2 = registry.containsBeanDefinition("com.atguigu.bean.Blue");
		if(definition && definition2){
			//指定Bean定义信息；（Bean的类型，Bean。。。）
			RootBeanDefinition beanDefinition = new RootBeanDefinition(RainBow.class);
			//注册一个Bean，指定bean名
			registry.registerBeanDefinition("rainBow", beanDefinition);
		}
	}

}

```

FactoryBean：

```java
//创建一个Spring定义的FactoryBean
public class ColorFactoryBean implements FactoryBean<Color> {

	//返回一个Color对象，这个对象会添加到容器中
	@Override
	public Color getObject() throws Exception {
		// TODO Auto-generated method stub
		System.out.println("ColorFactoryBean...getObject...");
		return new Color();
	}

	@Override
	public Class<?> getObjectType() {
		// TODO Auto-generated method stub
		return Color.class;
	}

	//是单例？
	//true：这个bean是单实例，在容器中保存一份
	//false：多实例，每次获取都会创建一个新的bean；
	@Override
	public boolean isSingleton() {
		// TODO Auto-generated method stub
		return false;
	}

}

```

### 生命周期

 **bean的生命周期：**
 * 		bean创建---初始化----销毁的过程
 * 容器管理bean的生命周期；

Bean的生命周期管理方法：

​	1）、指定初始化和销毁方法；

​			通过@Bean指定init-method和destroy-method；

​	2）、通过让Bean实现InitializingBean（定义初始化逻辑），

​		DisposableBean（定义销毁逻辑）;

​	3）、可以使用JSR250；

​		@PostConstruct：在bean创建完成并且属性赋值完成；来执行初始化方法

​		@PreDestroy：在容器销毁bean之前通知我们进行清理工作

​	4）、BeanPostProcessor【interface】：bean的后置处理器；

​		在bean初始化前后进行一些处理工作；

​		postProcessBeforeInitialization:在初始化之前工作

​		postProcessAfterInitialization:在初始化之后工作

#### 第一种方法：

```java
@Bean(initMethod="init",destroyMethod="detory")
public Car car(){
	return new Car();
}
```

#### 第二种方法：

```java
public class Cat implements InitializingBean,DisposableBean 
```

#### 第三种方法:

```java
//对象创建并赋值之后调用
	@PostConstruct
	public void init(){
		System.out.println("Dog....@PostConstruct...");
	}
	
	//容器移除对象之前
	@PreDestroy
	public void detory(){
		System.out.println("Dog....@PreDestroy...");
	}

```

#### 第四种方法：

````````java
public class MyBeanPostProcessor implements BeanPostProcessor 
````````

#### Bean的整个生命周期：

 * 构造（对象创建）

   单实例：在容器启动的时候创建对象

   多实例：在每次获取的时候创建对象\
 * BeanPostProcessor.postProcessBeforeInitialization
 * 初始化：

   对象创建完成，并赋值好，调用初始化方法。。。
 * BeanPostProcessor.postProcessAfterInitialization
 * 销毁：

   单实例：容器关闭的时候

   多实例：容器不会管理这个bean；容器不会调用销毁方法；

   

原理：

* 遍历得到容器中所有的BeanPostProcessor；挨个执行beforeInitialization，

    一但返回null，跳出for循环，不会执行后面的BeanPostProcessor.postProcessorsBeforeInitialization

 * BeanPostProcessor原理

    populateBean(beanName, mbd, instanceWrapper);给bean进行属性赋值

    initializeBean

    {

    applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);

    invokeInitMethods(beanName, wrappedBean, mbd);执行自定义初始化

    applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);
    }

### 属性赋值：

@Value:
	1、基本数值
	2、可以写SpEL； #{}
	3、可以写${}；取出配置文件【properties】中的值（在运行环境变量里面的值）

```java
	@Value("张三")
	private String name;
	@Value("#{20-2}")
	private Integer age;
	
	@Value("${person.nickName}")
	private String nickName;
```

如果要取出配置文件的值那么就需要在配置类上标注@PropertySource注解

```java
//使用@PropertySource读取外部配置文件中的k/v保存到运行的环境变量中;加载完外部的配置文件以后使用${}取出配置文件的值
@PropertySource(value={"classpath:/person.properties"})
@Configuration
public class MainConfigOfPropertyValues {
	
	@Bean
	public Person person(){
		return new Person();
	}

}
```

#### 自动装配：

@Autowired

可以放在构造器，参数，方法，属性上；**下面的值都是从容器中获取参数组件的值**
 * 		1）、[标注在方法位置]：@Bean+方法参数；参数从容器中获取;
 * 		2）、[标在构造器上]：如果组件只有一个有参构造器，这个有参构造器的@Autowired可以省略，参数位置的组件还是可以自动从容器中获取
 * 		3）、放在参数位置：
 * 		上面三个位置，默认不写@Autowired，效果是一样的；都能自动装配

@Autowired：自动注入：

1）、默认优先按照类型去容器中找对应的组件:applicationContext.getBean(BookDao.class);找到就赋值

2）、如果找到多个相同类型的组件，再将属性的名称作为组件的id去容器中查找applicationContext.getBean("bookDao")

3）、@Qualifier("bookDao")：使用@Qualifier指定需要装配的组件的id，而不是使用属性名

4）、自动装配默认一定要将属性赋值好，没有就会报错；

​		可以使用@Autowired(required=false);（能装就装，不能装拉倒。为空）

5）、@Primary：让Spring进行自动装配的时候，默认使用首选的bean；

也可以继续使用@Qualifier指定需要装配的bean的名字

```java
class BookService{
  @Autowired
  BookDao  bookDao;
}
```

Spring还支持使用@Resource(JSR250)和@Inject(JSR330)[java规范的注解]
 * @Resource:

   可以和@Autowired一样实现自动装配功能；默认是按照组件名称进行装配的；

   没有能支持@Primary功能没有支持@Autowired（reqiured=false）;
 * @Inject:

   需要导入javax.inject的包，能支持@Primary功能。没有required=false的功能；

   注意：@Autowired:Spring定义的； @Resource、@Inject都是java规范

AutowiredAnnotationBeanPostProcessor:解析完成自动装配功能；

**自定义组件想要使用Spring容器底层的一些组件（ApplicationContext，BeanFactory，xxx）；**

 * 		自定义组件实现xxxAware；在创建对象的时候，会调用接口规定的方法注入相关组件；Aware；
 * 		把Spring底层一些组件注入到自定义的Bean中；
 * 		xxxAware：功能使用xxxProcessor；
 * 			ApplicationContextAware==》ApplicationContextAwareProcessor；

```java
@Component
public class Red implements ApplicationContextAware,BeanNameAware,EmbeddedValueResolverAware {
	
	private ApplicationContext applicationContext;

	@Override
	public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
		// TODO Auto-generated method stub
		System.out.println("传入的ioc："+applicationContext);
		this.applicationContext = applicationContext;
	}

	@Override
	public void setBeanName(String name) {
		// TODO Auto-generated method stub
		System.out.println("当前bean的名字："+name);
	}

	@Override
	public void setEmbeddedValueResolver(StringValueResolver resolver) {
		// TODO Auto-generated method stub
		String resolveStringValue = resolver.resolveStringValue("你好 ${os.name} 我是 #{20*18}");
		System.out.println("解析的字符串："+resolveStringValue);
	}
}

```

其中实现EmbeddedValueResolverAware能使用解析占位符的方法





@Profile：

Spring为我们提供的可以根据当前环境，动态的激活和切换一系列组件的功能；

我们怎么能来回切换开发环境、测试环境、生产环境的数据源呢；

@Profile：指定组件在哪个环境的情况下才能被注册到容器中，不指定，任何环境下都能注册这个组件

 * 1）、加了环境标识的bean，只有这个环境被激活的时候才能注册到容器中。默认是default环境
 * 2）、写在配置类上，只有是指定的环境的时候，整个配置类里面的所有配置才能开始生效
 * 3）、没有标注环境标识的bean在，任何环境下都是加载的；

```java
@PropertySource("classpath:/dbconfig.properties")
@Configuration
public class MainConfigOfProfile implements EmbeddedValueResolverAware{	
	@Value("${db.user}")
	private String user;
	private StringValueResolver valueResolver;	
	private String  driverClass;
	@Bean
	public Yellow yellow(){
		return new Yellow();
	}
	@Profile("test")
	@Bean("testDataSource")
	public DataSource dataSourceTest(@Value("${db.password}")String pwd) throws Exception{
		ComboPooledDataSource dataSource = new ComboPooledDataSource();
		dataSource.setUser(user);
		dataSource.setPassword(pwd);
		dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
		dataSource.setDriverClass(driverClass);
		return dataSource;
	}
	@Profile("dev")
	@Bean("devDataSource")
	public DataSource dataSourceDev(@Value("${db.password}")String pwd) throws Exception{
		ComboPooledDataSource dataSource = new ComboPooledDataSource();
		dataSource.setUser(user);
		dataSource.setPassword(pwd);
		dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/ssm_crud");
		dataSource.setDriverClass(driverClass);
		return dataSource;
	}
	@Profile("prod")
	@Bean("prodDataSource")
	public DataSource dataSourceProd(@Value("${db.password}")String pwd) throws Exception{
		ComboPooledDataSource dataSource = new ComboPooledDataSource();
		dataSource.setUser(user);
		dataSource.setPassword(pwd);
		dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/scw_0515");
		dataSource.setDriverClass(driverClass);
		return dataSource;
	}
	@Override
	public void setEmbeddedValueResolver(StringValueResolver resolver) {
		// TODO Auto-generated method stub
		this.valueResolver = resolver;
		driverClass = valueResolver.resolveStringValue("${db.driverClass}");
	}

}
```

激活方法：

1、使用命令行动态参数: 在虚拟机参数位置加载 -Dspring.profiles.active=test
2、代码的方式激活某种环境；

```java
		//1、创建一个applicationContext
AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
		//2、设置需要激活的环境
		applicationContext.getEnvironment().setActiveProfiles("dev");
		//3、注册主配置类
		applicationContext.register(MainConfigOfProfile.class);
		//4、启动刷新容器
		applicationContext.refresh();
```

### AOP：【动态代理】

 * 		指在程序运行期间动态的将某段代码切入到指定方法指定位置进行运行的编程方式；

AOP应用步骤：

 * 1、导入aop模块；Spring AOP：(spring-aspects)
 * 2、定义一个业务逻辑类（MathCalculator）；在业务逻辑运行的时候将日志进行打印（方法之前、方法运行结束、方法出现异常，xxx）
 * 3、定义一个日志切面类（LogAspects）：切面类里面的方法需要动态感知MathCalculator.div运行到哪里然后执行；

   ​	通知方法：

   ​	前置通知(@Before)：logStart：在目标方法(div)运行之前运行

   ​	后置通知(@After)：logEnd：在目标方法(div)运行结束之后运行（无论方法正常结束还是异常结束）

   ​	返回通知(@AfterReturning)：logReturn：在目标方法(div)正常返回之后运行

   ​	异常通知(@AfterThrowing)：logException：在目标方法(div)出现异常以后运行

   ​	环绕通知(@Around)：动态代理，手动推进目标方法运行（joinPoint.procced()）
 * 4、给切面类的目标方法标注何时何地运行（通知注解）；
 * 5、将切面类和业务逻辑类（目标方法所在类）都加入到容器中;
 * 6、必须告诉Spring哪个类是切面类(给切面类上加一个注解：@Aspect)
 * 7、给配置类中加 @EnableAspectJAutoProxy 【开启基于注解的aop模式】

AOP创建步骤概括：

三步：

 * 	1）、将业务逻辑组件和切面类都加入到容器中；告诉Spring哪个是切面类（@Aspect）
 * 	2）、在切面类上的每一个通知方法上标注通知注解，告诉Spring何时何地运行（切入点表达式）
 *  3）、开启基于注解的aop模式；@EnableAspectJAutoProxy

### 声明式事务：

步骤：

 * 1、导入相关依赖

   数据源、数据库驱动、Spring-jdbc模块
 * 2、配置数据源、JdbcTemplate（Spring提供的简化数据库操作的工具）操作数据
 * 3、给方法上标注 @Transactional 表示当前方法是一个事务方法；
 * 4、 @EnableTransactionManagement 开启基于注解的事务管理功能；

   @EnableXXX
 * 5、配置事务管理器来控制事务;

   @Bean

   public PlatformTransactionManager transactionManager()

### servlet3.0：

Shared libraries（共享库） / runtimes pluggability（运行时插件能力）

1、Servlet容器启动会扫描，当前应用里面每一个jar包的
	ServletContainerInitializer的实现
2、提供ServletContainerInitializer的实现类；
	必须绑定在，META-INF/services/javax.servlet.ServletContainerInitializer
	文件的内容就是ServletContainerInitializer实现类的全类名；

总结：容器在启动应用的时候，会扫描当前应用每一个jar包里面
META-INF/services/javax.servlet.ServletContainerInitializer
指定的实现类，启动并运行这个实现类的方法；传入感兴趣的类型；

```java
//容器启动的时候会将@HandlesTypes指定的这个类型下面的子类（实现类，子接口等）传递过来；
//传入感兴趣的类型；
@HandlesTypes(value={HelloService.class})
public class MyServletContainerInitializer implements ServletContainerInitializer {

	/**
	 * 应用启动的时候，会运行onStartup方法；
	 * 
	 * Set<Class<?>> arg0：感兴趣的类型的所有子类型；
	 * ServletContext arg1:代表当前Web应用的ServletContext；一个Web应用一个ServletContext；
	 * 
	 * 1）、使用ServletContext注册Web组件（Servlet、Filter、Listener）
	 * 2）、使用编码的方式，在项目启动的时候给ServletContext里面添加组件；
	 * 		必须在项目启动的时候来添加；
	 * 		1）、ServletContainerInitializer得到的ServletContext；
	 * 		2）、ServletContextListener得到的ServletContext；
	 */
	@Override
	public void onStartup(Set<Class<?>> arg0, ServletContext sc) throws ServletException {
		// TODO Auto-generated method stub
		System.out.println("感兴趣的类型：");
		for (Class<?> claz : arg0) {
			System.out.println(claz);
		}
		
		//注册组件  ServletRegistration  
		ServletRegistration.Dynamic servlet = sc.addServlet("userServlet", new UserServlet());
		//配置servlet的映射信息
		servlet.addMapping("/user");
		
		
		//注册Listener
		sc.addListener(UserListener.class);
		
		//注册Filter  FilterRegistration
		FilterRegistration.Dynamic filter = sc.addFilter("userFilter", UserFilter.class);
		//配置Filter的映射信息
		filter.addMappingForUrlPatterns(EnumSet.of(DispatcherType.REQUEST), true, "/*");
		
	}

}

```

### SpringMVC：

1、web容器在启动的时候，会扫描每个jar包下的META-INF/services/javax.servlet.ServletContainerInitializer
2、加载这个文件指定的类SpringServletContainerInitializer
3、spring的应用一启动会加载感兴趣的WebApplicationInitializer接口的下的所有组件；
4、并且为WebApplicationInitializer组件创建对象（组件不是接口，不是抽象类）
	1）、AbstractContextLoaderInitializer：创建根容器；createRootApplicationContext()；
	2）、AbstractDispatcherServletInitializer：
			创建一个web的ioc容器；createServletApplicationContext();
			创建了DispatcherServlet；createDispatcherServlet()；
			将创建的DispatcherServlet添加到ServletContext中；
				getServletMappings();
	3）、AbstractAnnotationConfigDispatcherServletInitializer：注解方式配置的DispatcherServlet初始化器
			创建根容器：createRootApplicationContext()
					getRootConfigClasses();传入一个配置类
			创建web的ioc容器： createServletApplicationContext();
					获取配置类；getServletConfigClasses();
	
总结：
	以注解方式来启动SpringMVC；继承AbstractAnnotationConfigDispatcherServletInitializer；
实现抽象方法指定DispatcherServlet的配置信息；



#### 实例步骤：

```java
//web容器启动的时候创建对象；调用方法来初始化容器以前前端控制器
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

	//获取根容器的配置类；（Spring的配置文件）   父容器；
	@Override
	protected Class<?>[] getRootConfigClasses() {
		// TODO Auto-generated method stub
		return new Class<?>[]{RootConfig.class};
	}

	//获取web容器的配置类（SpringMVC配置文件）  子容器；
	@Override
	protected Class<?>[] getServletConfigClasses() {
		// TODO Auto-generated method stub
		return new Class<?>[]{AppConfig.class};
	}

	//获取DispatcherServlet的映射信息
	//  /：拦截所有请求（包括静态资源（xx.js,xx.png）），但是不包括*.jsp；
	//  /*：拦截所有请求；连*.jsp页面都拦截；jsp页面是tomcat的jsp引擎解析的；
	@Override
	protected String[] getServletMappings() {
		// TODO Auto-generated method stub
		return new String[]{"/"};
	}

}

```

##### 父容器配置类：

```java
//Spring的容器不扫描controller;父容器
@ComponentScan(value="com.atguigu",excludeFilters={
		@Filter(type=FilterType.ANNOTATION,classes={Controller.class})
})
public class RootConfig {

}

```



##### 子容器配置类：

```java
@ComponentScan(value="com.atguigu",includeFilters={
		@Filter(type=FilterType.ANNOTATION,classes={Controller.class})
},useDefaultFilters=false)
public class AppConfig  extends WebMvcConfigurerAdapter  {
}

```

定制SpringMVC；
1）、@EnableWebMvc:开启SpringMVC定制配置功能；
	<mvc:annotation-driven/>；

2）、配置组件（视图解析器、视图映射、静态资源映射、拦截器。。。）
	extends WebMvcConfigurerAdapter

```java
@ComponentScan(value="com.atguigu",includeFilters={
		@Filter(type=FilterType.ANNOTATION,classes={Controller.class})
},useDefaultFilters=false)
@EnableWebMvc
public class AppConfig  extends WebMvcConfigurerAdapter  {

	//定制
	
	//视图解析器
	@Override
	public void configureViewResolvers(ViewResolverRegistry registry) {
		// TODO Auto-generated method stub
		//默认所有的页面都从 /WEB-INF/ xxx .jsp
		//registry.jsp();
		registry.jsp("/WEB-INF/views/", ".jsp");
	}
	
	//静态资源访问
	@Override
	public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
		// TODO Auto-generated method stub
		configurer.enable();
	}
	
	//拦截器
	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		// TODO Auto-generated method stub
		//super.addInterceptors(registry);
		registry.addInterceptor(new MyFirstInterceptor()).addPathPatterns("/**");
	}

}

```

##### servlet3.0异步请求：

```java
@WebServlet(value="/async",asyncSupported=true)
public class HelloAsyncServlet extends HttpServlet {
	
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		//1、支持异步处理asyncSupported=true
		//2、开启异步模式
		System.out.println("主线程开始。。。"+Thread.currentThread()+"==>"+System.currentTimeMillis());
		AsyncContext startAsync = req.startAsync();
		
		//3、业务逻辑进行异步处理;开始异步处理
		startAsync.start(new Runnable() {
			@Override
			public void run() {
				try {
					System.out.println("副线程开始。。。"+Thread.currentThread()+"==>"+System.currentTimeMillis());
					sayHello();
					startAsync.complete();
					//获取到异步上下文
					AsyncContext asyncContext = req.getAsyncContext();
					//4、获取响应
					ServletResponse response = asyncContext.getResponse();
					response.getWriter().write("hello async...");
					System.out.println("副线程结束。。。"+Thread.currentThread()+"==>"+System.currentTimeMillis());
				} catch (Exception e) {
				}
			}
		});		
		System.out.println("主线程结束。。。"+Thread.currentThread()+"==>"+System.currentTimeMillis());
	}

	public void sayHello() throws Exception{
		System.out.println(Thread.currentThread()+" processing...");
		Thread.sleep(3000);
	}
}
```

![1](\images\1.PNG)

springMVC异步处理

 * 1、控制器返回Callable

 * 2、Spring异步处理，将Callable 提交到 TaskExecutor 使用一个隔离的线程进行执行

 * 3、DispatcherServlet和所有的Filter退出web容器的线程，但是response 保持打开状态；

 * 4、Callable返回结果，SpringMVC将请求重新派发给容器，恢复之前的处理；

 * 5、根据Callable返回的结果。SpringMVC继续进行视图渲染流程等（从收请求-视图渲染）。

    

 * preHandle.../springmvc-annotation/async01

		主线程开始...Thread[http-bio-8081-exec-3,5,main]==>1513932494700
	主线程结束...Thread[http-bio-8081-exec-3,5,main]==>1513932494700
	=========DispatcherServlet及所有的Filter退出线程============================
	
	================等待Callable执行==========
	副线程开始...Thread[MvcAsync1,5,main]==>1513932494707
	副线程开始...Thread[MvcAsync1,5,main]==>1513932496708
	================Callable执行完成==========
	
	================再次收到之前重发过来的请求========
	preHandle.../springmvc-annotation/async01
	postHandle...（Callable的之前的返回值就是目标方法的返回值）
	afterCompletion...
	
	异步的拦截器:
		1）、原生API的AsyncListener
		2）、SpringMVC：实现AsyncHandlerInterceptor；
	
	异步请求真实运用场景

![/images/2.PNG](/images/2.PNG)