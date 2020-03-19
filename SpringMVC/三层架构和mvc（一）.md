## 第一章：三层架构和MVC

### 三层架构

- #### 表现层：

  web层，用来和客户端进行数据交互的，表现层一般会采用MVC的设计模型

- #### 业务层：

  处理公司具体的业务逻辑的

- #### 持久层：

  用来操作数据库的

### MVC模型

1. MVC全名是Model View Controller 模型视图控制器，每个部分各司其职。 

2. Model：数据模型，JavaBean的类，用来进行数据封装。 

3. View：指JSP、HTML用来展示数据给用户 

4. Controller：用来接收用户的请求，整个流程的控制器。用来进行数据校验等。

### SpringMVC的概述

1. 是一种基于Java实现的MVC设计模型的请求驱动类型的轻量级WEB框架。 

2. Spring MVC属于SpringFrameWork的后续产品，已经融合在Spring Web Flow里面。Spring 框架提供 

了构建 Web 应用程序的全功能 MVC 模块。 

3. 使用 Spring 可插入的 MVC 架构，从而在使用Spring进行WEB开发时，可以选择使用Spring的 

SpringMVC框架或集成其他MVC开发框架，如Struts1(现在一般不用)，Struts2等。

#### springMVC在三层架构的位置：

位于web层

#### 面试题：SpringMVC和Struts2的异同

##### 共同点： 

它们都是表现层框架，都是基于 MVC 模型编写的。 

它们的底层都离不开原始 ServletAPI。 

它们处理请求的机制都是一个核心控制器。 

##### 区别： 

Spring MVC 的入口是 Servlet, 而 Struts2 是 Filter  

Spring MVC 是基于方法设计的，而 Struts2 是基于类，Struts2 每次执行都会创建一个动作类。所 以 Spring MVC 会稍微比 Struts2 快些。 

Spring MVC 使用更加简洁,同时还支持 JSR303, 处理 ajax 的请求更方便 