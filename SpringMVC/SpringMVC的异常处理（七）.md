1.自定义异常类

```java
package cn.itcast.exception;
public class SysException extends Exception{
private static final long serialVersionUID = 4055945147128016300L;
// 异常提示信息
private String message;
public String getMessage() {
return message;
}
public void setMessage(String message) {
this.message = message;
}
public SysException(String message) {
this.message = message;
}
    
```

2.自定义异常处理器实现HandlerExceptionResolver

```java
package cn.itcast.exception;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.springframework.web.servlet.HandlerExceptionResolver;
import org.springframework.web.servlet.ModelAndView;
/**
* 异常处理器
* @author rt
*/
public class SysExceptionResolver implements HandlerExceptionResolver{
/**
* 跳转到具体的错误页面的方法
*/
public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler,Exception ex) {
	ex.printStackTrace();
	SysException e = null;
	// 获取到异常对象
	if(ex instanceof SysException) {
		e = (SysException) ex;
	}else {
		e = new SysException("请联系管理员");
	}
	ModelAndView mv = new ModelAndView();
// 存入错误的提示信息
	mv.addObject("message", e.getMessage());
// 跳转的Jsp页面
	mv.setViewName("error");
	return mv;
}
}
```





3.配置（注入）异常处理器

```xml
<!-- 配置异常处理器 -->
<bean id="sysExceptionResolver" class="cn.itcast.exception.SysExceptionResolver"/>
```

