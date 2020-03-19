### resultType 配置结果类型 

```
resultType 属性可以指定结果集的类型，它支持基本类型和实体类类型。
我们在前面的 CRUD 案例中已经对此属性进行过应用了。
需要注意的是，它和 parameterType 一样，如果注册过类型别名的，可以直接使用别名。没有注册过的必须
使用全限定类名。例如：我们的实体类此时必须是全限定类名（今天最后一个章节会讲解如何配置实体类的别名）
同时，当是实体类名称时，还有一个要求，实体类中的属性名称必须和查询语句中的列名保持一致，否则无法
实现封装。
```

### 为什么名称会有值呢？ 

##### 因为：mysql 在 windows 系统中不区分大小写！ 

### 当数字库字段和实体类的属性名没有相同，没有对应上，那么有什么办法解决呢

#### 1.使用别名：

```xml
使用别名查询
<!-- 配置查询所有操作 -->
<select id="findAll" resultType="com.itheima.domain.User">
select id as userId,username as userName,birthday as userBirthday,
sex as userSex,address as userAddress from user
</select>
```

如果我们的查询很多，都使用别名的话写起来岂不是很麻烦，有没有别的解决办法呢？ 

#### 2.resultMap 结果类型 

```xml
<!-- 建立 User 实体和数据库表的对应关系
type 属性：指定实体类的全限定类名
id 属性：给定一个唯一标识，是给查询 select 标签引用用的。
-->
<resultMap type="com.itheima.domain.User" id="userMap">
<id column="id" property="userId"/>
<result column="username" property="userName"/>
<result column="sex" property="userSex"/>
<result column="address" property="userAddress"/>
<result column="birthday" property="userBirthday"/>
</resultMap>
id 标签：用于指定主键字段
result 标签：用于指定非主键字段
column 属性：用于指定数据库列名
property 属性：用于指定实体类属性名称
```

##### 配置映射：

```xml
<!-- 配置查询所有操作 -->
<select id="findAll" resultMap="userMap">
select * from user
</select>
```

