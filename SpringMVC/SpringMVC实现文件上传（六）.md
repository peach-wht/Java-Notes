## 第二章：SpringMVC实现文件上传

### SpringMVC传统方式文件上传

#### 导包：

```xml
<dependency>
<groupId>commons-fileupload</groupId>
<artifactId>commons-fileupload</artifactId>
<version>1.3.1</version>
</dependency>
<dependency>
<groupId>commons-io</groupId>
<artifactId>commons-io</artifactId>
<version>2.4</version>
</dependency>
```



#### controller代码

记得，jsp页面传过来的数据名得和MultipartFile 后面的形参一致

```java
/**
* SpringMVC方式的文件上传
*
* @param request
* @return
* @throws Exception
*/
@RequestMapping(value="/fileupload2")
public String fileupload2(HttpServletRequest request,MultipartFile upload) throws
Exception {
System.out.println("SpringMVC方式的文件上传...");
// 先获取到要上传的文件目录
String path = request.getSession().getServletContext().getRealPath("/uploads");
// 创建File对象，一会向该路径下上传文件
File file = new File(path);
// 判断路径是否存在，如果不存在，创建该路径
if(!file.exists()) {
file.mkdirs();
}
// 获取到上传文件的名称
String filename = upload.getOriginalFilename();
String uuid = UUID.randomUUID().toString().replaceAll("-", "").toUpperCase();
// 把文件的名称唯一化
filename = uuid+"_"+filename;
// 上传文件
upload.transferTo(new File(file,filename));
return "success";
}
```

#### 配置文件解析器对象

配置文件解析器对象，要求id名称必须是multipartResolver

```xml
<!-- 配置文件解析器对象，要求id名称必须是multipartResolver -->
<bean id="multipartResolver"
class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
<property name="maxUploadSize" value="10485760"/>
</bean>
```

