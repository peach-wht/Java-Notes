### parameterType 配置参数 

我们在上一章节中已经介绍了 SQL 语句传参，使用标签的 parameterType 属性来设定。该属性的取值可以 

是基本类型，引用类型（例如:String 类型），还可以是实体类类型（POJO 类）。同时也可以使用实体类的包装 

类，本章节将介绍如何使用实体类的包装类作为参数传递。

基 本 类 型 和 String 我 们 可 以 直 接 写 类 型 名 称 ， 也 可 以 使 用 包 名 . 类 名 的 方 式 ， 例 如 ： 

java.lang.String。 

实体类类型，目前我们只能使用全限定类名。 

究其原因，是 mybaits 在加载时已经把常用的数据类型注册了别名，从而我们在使用时可以不写包名， 

而我们的是实体类并没有注册别名，所以必须写全限定类名。在今天课程的最后一个章节中将讲解如何注册实体类 的别名。 

### 传递 pojo 包装对象

开发中通过 pojo 传递查询条件 ，查询条件是综合的查询条件，不仅包括用户查询条件还包括其它的查 询条件（比如将用户购买商品信息也作为查询条件），这时可以使用包装对象传递输入参数。 

Pojo 类中包含 pojo。 

需求：根据用户名查询用户信息，查询条件放到 QueryVo 的 user 属性中。 （换言之就是如果javabean类型数据中有类对象时，xml中怎么使用类对象的属性做占位符）

```xml
<!-- 根据用户名称模糊查询，参数变成一个 QueryVo 对象了 -->
<select id="findByVo" resultType="com.itheima.domain.User"
 parameterType="com.itheima.domain.QueryVo">
select * from user where username like #{user.username};
</select>
```

