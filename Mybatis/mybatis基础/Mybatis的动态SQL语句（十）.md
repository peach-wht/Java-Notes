## Mybatis的动态SQL语句

### 动态 SQL 之if标签 

我们根据实体类的不同取值，使用不同的 SQL 语句来进行查询。比如在 id 如果不为空时可以根据 id 查询， 

如果 username 不同空时还要加入用户名作为条件。这种情况在我们的多条件组合查询中经常会碰到。 

```xml
<select id="findByUser" resultType="user" parameterType="user">
select * from user where 1=1
<if test="username!=null and username != '' ">
and username like #{username}
</if>
<if test="address != null">
and address like #{address}
</if>
</select>
注意：<if>标签的 test 属性中写的是对象的属性名，如果是包装类的对象要使用 OGNL 表达式的写法。
另外要注意 where 1=1 的作用~！
```

### 动态 SQL 之where标签 

为了简化上面 where 1=1 的条件拼装，我们可以采用标签来简化开发。 

```xml
<!-- 根据用户信息查询 -->
<select id="findByUser" resultType="user" parameterType="user">
<include refid="defaultSql"></include>
<where>
<if test="username!=null and username != '' ">
and username like #{username}
</if>
<if test="address != null">
and address like #{address}
</if>
</where>
</select>
```

### 动态标签之foreach标签 

传入多个 id 查询用户信息，用下边两个 sql 实现： 

SELECT * FROM USERS WHERE username LIKE '%张%' AND (id =10 OR id =89 OR id=16) 

SELECT * FROM USERS WHERE username LIKE '%张%' AND id IN (10,89,16) 

这样我们在进行范围查询时，就要将一个集合中的值，作为参数动态添加进来。 

这样我们将如何进行参数的传递？ 

```xml
<!-- 查询所有用户在 id 的集合之中 -->
<select id="findInIds" resultType="user" parameterType="queryvo">
<!-- select * from user where id in (1,2,3,4,5); -->
<include refid="defaultSql"></include>
<where>
<if test="ids != null and ids.size() > 0">
<foreach collection="ids" open="id in ( " close=")" item="uid" 
separator=",">
#{uid}
</foreach>
</if>
</where>
</select>

相当于：
SQL 语句：
select 字段 from user where id in (?)


<foreach>标签用于遍历集合，它的属性：
collection:代表要遍历的集合元素，注意编写时不要写#{}
open:代表语句的开始部分
close:代表结束部分
item:代表遍历集合的每个元素，生成的变量名
sperator:代表分隔符
```

### Mybatis 中简化编写的 SQL 片段 

Sql 中可将重复的 sql 提取出来，使用时用 include 引用即可，最终达到 sql 重用的目的

```xml
<!-- 抽取重复的语句代码片段 -->
<sql id="defaultSql">
select * from user
</sql>

<!-- 配置查询所有操作 -->
<select id="findAll" resultType="user">
<include refid="defaultSql"></include>
</select>

<!-- 根据 id 查询 -->
<select id="findById" resultType="UsEr" parameterType="int">
    <include refid="defaultSql"></include>
where id = #{uid}
</select>
```

