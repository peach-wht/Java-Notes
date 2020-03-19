# Spring MVC

## 概述

1. Spring为展现层提供的基于MVC 设计理念的优秀的Web 框架，是目前最主流的MVC 框架之一
2. Spring MVC 通过一套MVC 注解，让POJO 成为处理请求的控制器，而无须实现任何接口。
3. 支持REST 风格的URL 请求
4. 采用了松散耦合可插拔组件结构，比其他MVC 框架更具扩展性和灵活性

## SpringMVC工作原理

1. 用户发出请求

2. 前端控制器（DispatcherServlet）对请求的URL进行解析，得到URI（请求资源标识符），然后根据URI调用处理器映射器（HandlerMapping）获得Handler配置相关的对象，包括Handler对象以及Handler对象对应的拦截器，这些对象会封装到一个HandlerExecutionChain对象当中返回

3. 前端控制器（DispatcherServlet）根据获取的Handler，找到对应的HandlerAdaper

4. HandlerAdaper调用对应的Handler，提取请求中的模型数据开始执行Handler

5. Handler执行完成后，向DispatchServlet返回一个ModelAndView对象

6. 根据返回的ModelAndView对象，选择一个合适的ViewResolver（视图解析器）返回给前端控制器

7. viewResolver结合Model和View渲染视图

8. 将视图渲染结果返回给客户端

   ![1570964590105](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570964590105.png)

## HelloWorld：

1. 编辑web.xml配置DispatchServlet
2. 配置DispatcherServlet的一个初始化参数：配置SpringMVC配置文件的位置和名称
3. 加入SpringMVC配置文件
   1. 配置自动扫描的包
   2. 配置视图解析器：如何把Handler方法返回值解析为实际的视图
4. 创建HelloWorld文件
5. 使用@RequestMapping注解来映射请求的URL
6. 返回值会通过视图解析器解析为实际的物理视图，然后做转发操作

## @RequestMapping

Spring MVC 使用@RequestMapping注解为控制器指定可以处理哪些URL 请求

1. 可以修饰类也可以修改方法

2. ### 类定义处：

   提供初步的请求映射信息。相对于WEB 应用的根目录–

3. #### 方法处：

   提供进一步的细分映射信息。相对于类定义处的URL。若–类定义处未标注@RequestMapping，则方法处标记的URL 相对于WEB 应用的根目录

### 注意：

@RequestMapping的value、method、params及heads 分别表示**请求URL**、**请求方法、请求参数及请求头的映射条件**，他们之间是与的关系，联合使用多个条件可让请求映射更加精确化。

其中**请求方法**较为常用

#### 请求方法

@RequestMapping（value=".....",method=RequestMethod.POST）

#### 了解：

可以使用param和headers来更加精确的映射请求，且支持简单的表达式

### @PathVariable

可以来映射URL中的占位符到目标方法的参数中

![1570949113524](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570949113524.png)

## REST

即Representational State Transfer。（资源）表现层状态转化。是目前最流行的一种互联网软件架构。

### HiddenHttpMethodFilter

浏览器form 表单只支持GET 与POST 请求，而DELETE、PUT 等method 并不支持，Spring3.0 添加了一个过滤器，可以将这些请求转换为标准的http 方法，使得支持GET、POST、PUT 与DELETE 请求

#### 如何发送PUT和DELETE请求呢？

1. 在web.xml中配置HiddenHttpMethodFilter
2. 需要发送POST请求
3. 在发送POST请求时携带一个name="_method"的隐藏域（< input type="hidden" name="__method"> ），值为PUT或DELETE

## 映射请求参数& 请求参数

### @RequestParam

在处理方法入参处使用@RequestParam可以把请求参•数传递给请求方法

#### value：

请求参数的参数名

#### required：

是否必须。默认为true, 表示请求参数中必须包含对应–的参数，若不存在，将抛出异常

#### defaultValue：

请求参数默认值

![1570952212007](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570952212007.png)

### @RequestHeader（了解）

请求头包含了若干个属性，服务器可据此获知客户端的信息，通过@RequestHeader即可将请求头中的属性值绑定到处理方法的入参中

![1570952515317](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570952515317.png)

### @CookieValue（了解）

@CookieValue可让处理方法入参绑定某个Cookie 值

![1570952671690](C:\Users\wuhaotian\AppData\Roaming\Typora\typora-user-images\1570952671690.png)

### POJO

#### 题外话：级联属性是啥：

先是内部bean是啥：**一个Person有name,age,car这三个属性**

**给Person配置xml文件时有car这个配置好的属性（Forexam炮：轮子、品牌、价格）这时一个<perporty   ref="/>指过去就可以了，当然单独在后面给配置一个也行用构造器注入**

**叫引用内部Bean。类似继承一样，继承了car的Bean配置的属性值。说白了就是Person的Bean     引用了属性（car）    的属性Bean。**

**（内部Bean应该是Person的属性内部有car，后面引用car的Bean配置文件，所以就叫内部Bean，引用了car的Bean就叫引用内部Bean）**

级联属性顾名思义又是属性和属性之间的事，如图构造器注入ref="car"后，下面就可以给car的属性进行赋值，这个理解一下更简单

Person有名字年龄还有一辆车，有一天他突然想卖这辆车，就自己给车重新写了一个价钱多少钱，就这样  

#### 正题：

Spring MVC 会按**请求参数名**和**POJO** （java类）属性名进行自动匹配，自动为该对象填充属性值。支持级联属性

### POJO和JavaBean的区别

POJO其实是比javabean更纯净的简单类或接口。POJO严格地遵守简单对象的概念，而一些JavaBean中往往会封装一些简单逻辑。

POJO主要用于数据的临时传递，它只能装载数据， 作为数据存储的载体，而不具有业务逻辑处理的能力。

Javabean虽然数据的获取与POJO一样，但是javabean当中可以有其它的方法。

## 处理模型数据

### ModelAndView

控制器处理方法的返回值如果为ModelAndView, 则其既包含视图信息，也包含模型数据信息

#### 添加模型数据:

MoelAndViewaddObject(String attributeName, Object –attributeValue)

ModelAndViewaddAllObject(Map<String, ?> modelMap)

#### 设置视图:

void setView(View view)

void setViewName(String viewName)j

### Map 及Model

Spring MVC 在调用方法前会创建一个隐–含的模型对象作为模型数据的存储容器。如果方法的入参为Map 或Model 类–型，Spring MVC 会将隐含模型的引用传递给这些入参。在方法体内，开发者可以通过这个入参对象访问到模型中的所有数据，也可以向模型中添加新的属性数

### @SessionAttributes

若希望在多个请求之间共用某个模型属性数据，则可以在控制器类上标注一个@SessionAttributes, Spring MVC 将在模型中对应的属性暂存到HttpSession中

@SessionAttributes(types=User.class) 会将隐含模型中所有类型–为User.class的属性添加到会话中。

@SessionAttributes(value={“user1”, “user2”})–

@SessionAttributes(types={User.class, Dept.class})–

@SessionAttributes(value={“user1”, “user2”}, –types={Dept.class})

### @ModelAttribute

#### 使用场景：

当需要更新数据库信息时，某些字段不能修改，那么只能先从数据库中获得数据，再把它放入User中，这个操作能进行的基础就是@ModelAttribute，在获得数据库数据的方法前加上@ModelAttribute注解就行了

在方法定义上使用@ModelAttribute注解：Spring MVC 在调用目标处理方法前，**会先逐个调用在方法级上标注了@ModelAttribute的方法**。

在方法的入参前使用@ModelAttribute注解：

可以从隐含对象中获取隐含的模型数据中获取对象，**再将请求参数绑定到对象中，再传入入参将方法入参对象添加到模型中**

#### 问题：SpringMVC确定目标方法POJO类型入参过程

解答 P21

### 由@SessionAttributes引发的异常

如果在处理类定义处标注了@SessionAttributes(“xxx”)，则•尝试从会话中获取该属性，并将其赋给该入参，然后再用请求消息填充该入参对象。如果在会话中找不到对应的属性，则抛出HttpSessionRequiredException异常

## 视图和视图解析器

请求处理方法执行完成后，最终返回一个ModelAndView•对象。对于那些返回String，View 或ModeMap等类型的处理方法，Spring MVC 也会在内部将它们装配成一个ModelAndView对象，它包含了逻辑名和模型对象的视图Spring MVC 借助视图解析器（ViewResolver）得到最终•的视图对象（View），最终的视图可以是JSP ，也可能是Excel、JFreeChart等各种表现形式的视图对于最终究竟采取何种视图对象对模型数据进行渲染，处•理器并不关心，处理器工作重点聚焦在生产模型数据的工作上，从而实现MVC 的充分解耦

### 视图

视图对象由视图解析器负责实例化。由于视图是无状态的，所以他们•不会有线程安全的问

### 视图解析器

SpringMVC为逻辑视图名的解析提供了不同的策略，可•以在Spring WEB 上下文中配置一种或多种解析策略，并指定他们之间的先后顺序。每一种映射策略对应一个具体的视图解析器实现类。视图解析器的作用比较单一：将逻辑视图解析为一个具体•的视图对象。

#### InternalResourceViewResolver

#### JstlView

### 关于重定向

**如果返回的字符串中带forward: 或redirect: 前缀时**，SpringMVC会对他们进行特殊处理：将forward: 和redirect: 当成指示符，其后的字符串作为URL 来处理

#### redirect:success.jsp：

会完成一个到success.jsp的重定向的操作

#### forward:success.jsp：

会完成一个到success.jsp的转发操作



### mvc:annotation-driven

<mvc:annotation-driven /> 会自动注•册RequestMappingHandlerMapping、RequestMappingHandlerAdapter与ExceptionHandlerExceptionResolver三个bean

还将提供以下支持：

支持使用ConversionService实例对表单参数进行类型转换–

支持使用@NumberFormat annotation、@DateTimeFormat–

注解完成数据类型的格式化支持使用@Valid注解对JavaBean 

实例进行JSR 303 验证–支持使用@RequestBody和@ResponseBody注解





## SpringMVC对比Struts2

①. Spring MVC 的入口是Servlet, 而Struts2 是Filter

•②. Spring MVC 会稍微比Struts2 快些. Spring MVC 是基•于方法设计, 而Sturts2 是基于类, 每次发一次请求都会实例一个Action.

③. Spring MVC 使用更加简洁, 开发效率Spring MVC确实•比struts2 高: 支持JSR303, 处理ajax的请求更方便

④. Struts2 的OGNL 表达式使页面的开发效率相比Spring MVC 更高些.

