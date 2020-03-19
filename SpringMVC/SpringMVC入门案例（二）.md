### HelloWorld入门案例步骤：

1. 创建maven项目，注意添加一组键值对可以减少idea对于项目创建的时间

2. 编写pom.xml文件，引入依赖包

3. 在main目录下创建java目录和resources目录，并进行配置

4. 在web.xml中配置核心的DispatcherServlet

   ```xml
   <!-- SpringMVC的核心控制器 -->
   <servlet>
   <servlet-name>dispatcherServlet</servlet-name>
   <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet￾class>
   <!-- 配置Servlet的初始化参数，读取springmvc的配置文件，创建spring容器 -->
   <init-param>
   <param-name>contextConfigLocation</param-name>
   <param-value>classpath:springmvc.xml</param-value>
   </init-param>
   <!-- 配置servlet启动时加载对象 -->
   <load-on-startup>1</load-on-startup>
   </servlet>
   <servlet-mapping>
   <servlet-name>dispatcherServlet</servlet-name>
   <url-pattern>/</url-pattern>
   </servlet-mapping>
   ```

   

5. 编写springmvc.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
   xmlns:mvc="http://www.springframework.org/schema/mvc"
   xmlns:context="http://www.springframework.org/schema/context"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="
   http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans.xsd
   http://www.springframework.org/schema/mvc
   http://www.springframework.org/schema/mvc/spring-mvc.xsd
   http://www.springframework.org/schema/context
   http://www.springframework.org/schema/context/spring-context.xsd">
   <!-- 配置spring创建容器时要扫描的包 -->
   <context:component-scan base-package="com.itheima"></context:component-scan>
   <!-- 配置视图解析器 -->
   <bean id="viewResolver"
   class="org.springframework.web.servlet.view.InternalResourceViewResolver">
   <property name="prefix" value="/WEB-INF/pages/"></property>
   <property name="suffix" value=".jsp"></property>
   </bean>
   <!-- 配置spring开启注解mvc的支持
   <mvc:annotation-driven></mvc:annotation-driven>-->
   </beans>
   ```

   

6. 编写index.jsp和HelloController控制器类

### 涉及到的组件：

1. 前端控制器（DispatcherServlet） 

2. 处理器映射器（HandlerMapping） 

3. 处理器（Handler） 

4. 处理器适配器（HandlAdapter） 

5. 视图解析器（View Resolver） 

6. 视图（View）

### 关于<mvc:annotation-driven>

使<mvc:annotation-driven>用自动加载 RequestMappingHandlerMapping （处理映射器） 和 

RequestMappingHandlerAdapter （ 处 理 适 配 器 ） ， 可 用 在 SpringMVC.xml 配 置 文 件 中 使 用 

替代注解处理器和适配器的配置。 

### RequestMapping注解

1. RequestMapping注解的作用是建立请求URL和处理
2. RequestMapping注解可以作用在方法和类上
   1. 作用在类上：第一级的访问目录
   2. 作用在方法上：第二级的访问目录
   3. 细节：路径可以不编写 / 表示应用的根目录开
   4. 细节：${ pageContext.request.contextPath 
3. RequestMapping的属性
   1. path 指定请求路径的url
   2. value value属性和path属性是一样的
   3. method 指定该方法的请求方式
   4. params 指定限制请求参数的条件
   5. headers 发送的请求中必须包含的请求头

