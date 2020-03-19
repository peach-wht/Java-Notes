### SpringMVC的拦截器：

#### 	过滤器和拦截器的区别

​		**过滤器**是 servlet 规范中的一部分，任何 java web 工程都可以使用。 

​		**拦截器**是 SpringMVC 框架自己的，只有使用了 SpringMVC 框架的工程才能用。 

​		**过滤器**在 url-pattern 中配置了**/\***之后，可以对所有要访问的资源拦截。 

​		**拦截器**它是只会拦截访问的控制器方法，如果访问的是 jsp，html,css,image 或者 js 是不会进行拦 截的。 

​		要想自定义拦截器，就必须实现HandlerInterceptor接口

#### 	步骤：

​		第一步：编写一个普通类实现 HandlerInterceptor 接口

​		

```java
public class HandlerInterceptorDemo1 implements HandlerInterceptor {
@Override
//预处理：在控制器方法前执行
public boolean preHandle(HttpServletRequest request, HttpServletResponse 
response, Object handler)throws Exception {
	System.out.println("preHandle 拦截器拦截了");
	return true;
}
    
//后处理：在控制器方法后执行
@Override
public void postHandle(HttpServletRequest request, HttpServletResponse response, 
Object handler,ModelAndView modelAndView) throws Exception {
	System.out.println("postHandle 方法执行了");
}
    
//最后执行：jsp页面执行了才执行
@Override
public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
throws Exception {
	System.out.println("afterCompletion 方法执行了");
}
}
```

​		第二步：配置拦截器

​		

```xml
<!-- 配置拦截器 -->
<mvc:interceptors>
<mvc:interceptor>
    <!-- 你要拦截的方法-->
<mvc:mapping path="/**"/><!--所有方法都拦-->
<bean id="handlerInterceptorDemo1"
      class="com.itheima.web.interceptor.HandlerInterceptorDemo1"></bean>
    <!--注入自定义拦截器-->
</mvc:interceptor>
</mvc:interceptors>
```

**（1）过滤器(Filter)：**

它依赖于servlet容器。在实现上，基于函数回调，它可以对几乎所有请求进行过滤，但是缺点是一个过滤器实例只能在容器初始化时调用一次。使用过滤器的目的，是用来做一些过滤操作，获取我们想要获取的数据，比如：在Javaweb中，对传入的request、response提前过滤掉一些信息，或者提前设置一些参数，然后再传入servlet或者Controller进行业务逻辑操作。通常用的场景是：在过滤器中修改字符编码（CharacterEncodingFilter）、在过滤器中修改HttpServletRequest的一些参数（XSSFilter(自定义过滤器)），如：过滤低俗文字、危险字符等。

**（2）拦截器（Interceptor）：**

它依赖于web框架，在SpringMVC中就是依赖于SpringMVC框架。在实现上,基于Java的反射机制，属于面向切面编程（AOP）的一种运用，就是在service或者一个方法前，调用一个方法，或者在方法后，调用一个方法，比如动态代理就是拦截器的简单实现，在调用方法前打印出字符串（或者做其它业务逻辑的操作），也可以在调用方法后打印出字符串，甚至在抛出异常的时候做业务逻辑的操作。由于拦截器是基于web框架的调用，因此可以使用Spring的依赖注入（DI）进行一些业务操作，同时一个拦截器实例在一个controller生命周期之内可以多次调用。**但是缺点是只能对controller请求进行拦截，对其他的一些比如直接访问静态资源的请求则没办法进行拦截处理**（存在问题）。

 两者的本质区别：拦截器（Interceptor）是基于Java的反射机制，而过滤器（Filter）是基于函数回调。 

两者的执行顺序：

![](\20180603132536413.png)

![](\20180603133007923.png)