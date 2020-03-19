## mybati plus是什么

 MyBatis-Plus（简称 MP）是一个 MyBatis 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高 效率而生 

## 特性

 **无侵入**：只做增强不做改变，引入它不会对现有工程产生影响，如丝般顺滑 

**损耗小**：启动即会自动注入基本 CURD，性能基本无损耗，直接面向对象操作 

**强大的 CRUD 操作**：内置通用 Mapper、通用 Service，仅仅通过少量配置即可实现单表大部分 CRUD 操作， 更有强大的条件构造器，满足各类使用需求 

**支持 Lambda 形式调用**：通过 Lambda 表达式，方便的编写各类查询条件，无需再担心字段写错 

**支持多种数据库**：支持 MySQL、MariaDB、Oracle、DB2、H2、HSQL、SQLite、Postgre、 SQLServer2005、SQLServer 等多种数据库 

**支持主键自动生成**：支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - Sequence），可自由配置，完美解 决主键问题 

**支持 XML 热加载**：Mapper 对应的 XML 支持热加载，对于简单的 CRUD 操作，甚至可以无 XML 启动 

**支持 ActiveRecord 模式**：支持 ActiveRecord 形式调用，实体类只需继承 Model 类即可进行强大的 CRUD 操 作 

**支持自定义全局通用操作**：支持全局通用方法注入（ Write once, use anywhere ） 

**支持关键词自动转义**：支持数据库关键词（order、key......）自动转义，还可自定义关键词 

**内置代码生成器**：采用代码或者 Maven 插件可快速生成 Mapper 、 Model 、 Service 、 Controller 层代码， 支持模板引擎，更有超多自定义配置等您来使用 

**内置分页插件**：基于 MyBatis 物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同于普通 List 查询 

**内置性能分析插件**：可输出 Sql 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢查询 

**内置全局拦截插件**：提供全表 delete 、 update 操作智能分析阻断，也可自定义拦截规则，预防误操作 

**内置 Sql 注入剥离器**：支持 Sql 注入剥离，有效预防 Sql 注入攻击  

## 几个注解

@TableName：数据库表相关
@TableId：表主键标识
@TableField：表字段标识
@TableLogic：表字段逻辑处理注解（逻辑删除）

@TableId(type= IdType.ID_WORKER_STR)

@TableField(exist = false)：表示该属性不为数据库表字段，但又是必须使用的。

@TableField(exist = true)：表示该属性为数据库表字段。

@TableField(condition = SqlCondition.LIKE)：表示该属性可以模糊搜索。

@TableField(fill = FieldFill.INSERT)：注解填充字段 ，生成器策略部分也可以配置！

## 通用CRUD

###  插入操作 

insert()

###  更新操作 

 在MP中，更新操作有2种，一种是根据id更新，另一种是根据条件更新。  

####  根据id更新 :

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class UserMapperTest {
@Autowired
private UserMapper userMapper;
@Test
public void testUpdateById() {
User user = new User();
user.setId(6L); //主键
user.setAge(21); //更新的字段
//根据id更新，更新不为null的字段
this.userMapper.updateById(user);
}
}
```

#### 根据条件更新:

```java
User user = new User();
user.setAge(22); //更新的字段
//更新的条件
QueryWrapper<User> wrapper = new QueryWrapper<>();
wrapper.eq("id", 6);
//执行更新操作
int result = this.userMapper.update(user, wrapper);
System.out.println("result = " + result);

```

###  删除操作 

####  deleteById 

####  deleteByMap 

####  delete 

```java
User user = new User();
user.setAge(20);
user.setName("张三");
//将实体对象进行包装，包装为操作条件
QueryWrapper<User> wrapper = new QueryWrapper<>(user);
int result = this.userMapper.delete(wrapper);
System.out.println("result = " + result);

```

####  deleteBatchIds 

```java
/**
* 删除（根据ID 批量删除）
*
* @param idList 主键ID列表(不能为 null 以及 empty)
*/
int deleteBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable>
idList);
```

用法：

```java
int result = this.userMapper.deleteBatchIds(Arrays.asList(1L,10L,20L));
System.out.println("result = " + result);
```



###  查询操作 

####  selectById 

#### selectBatchIds 

#### selectOne 

```java
/**
* 根据 entity 条件，查询一条记录
*
* @param queryWrapper 实体对象封装操作类（可以为 null）
*/
T selectOne(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
```

用法：

```java
QueryWrapper<User> wrapper = new QueryWrapper<User>();
wrapper.eq("name", "李四");
//根据条件查询一条数据，如果结果超过一条会报错
User user = this.userMapper.selectOne(wrapper);
```



#### selectCount 

```java
/**
* 根据 Wrapper 条件，查询总记录数
*
* @param queryWrapper 实体对象封装操作类（可以为 null）
*/
Integer selectCount(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
```

用法：

````java
QueryWrapper<User> wrapper = new QueryWrapper<User>();
wrapper.gt("age", 23); //年龄大于23岁
//根据条件查询数据条数
Integer count = this.userMapper.selectCount(wrapper);
System.out.println("count = " + count);

````



#### selectList 

```java
/**
* 根据 entity 条件，查询全部记录
*
* @param queryWrapper 实体对象封装操作类（可以为 null）
*/
List<T> selectList(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
```

用法：

```java
QueryWrapper<User> wrapper = new QueryWrapper<User>();
wrapper.gt("age", 23); //年龄大于23岁
//根据条件查询数据
List<User> users = this.userMapper.selectList(wrapper);
for (User user : users) {
System.out.println("user = " + user);
}
```



####  selectPage 

````java
/**
* 根据 entity 条件，查询全部记录（并翻页）
*
* @param page 分页查询条件（可以为 RowBounds.DEFAULT）
* @param queryWrapper 实体对象封装操作类（可以为 null）
*/
IPage<T> selectPage(IPage<T> page, @Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
````

##### 分页插件

```java
@Bean
public PaginationInterceptor paginationInterceptor() {
    return new PaginationInterceptor();
}

```

````java
  @Test
    public void testSelectPage() {
        QueryWrapper<User> wrapper = new QueryWrapper<User>();
        wrapper.gt("age", 20); //年龄大于20岁
        Page<User> page = new Page<>(1, 1);
//根据条件查询数据
        IPage<User> iPage = this.userMapper.selectPage(page, wrapper);
        System.out.println("数据总条数：" + iPage.getTotal());
        System.out.println("总页数：" + iPage.getPages());
        List<User> users = iPage.getRecords();
        for (User user : users) {
            System.out.println("user = " + user);
        }
    }

````

## 配置

###  configLocation 

 MyBatis 配置文件位置，如果您有单独的 MyBatis 配置，请将其路径配置到 configLocation 中。 MyBatis Configuration 的具体内容请参考MyBatis 官方文档 

```properties
mybatis-plus.config-location = classpath:mybatis-config.xml
```

###  mapperLocations 

 MyBatis Mapper 所对应的 XML 文件位置，如果您在 Mapper 中有自定义方法（XML 中有自定义实现），需要进行 该配置，告诉 Mapper 所对应的 XML 文件位置。 

````properties
mybatis-plus.mapper-locations = classpath*:mybatis/*.xml
````

###  typeAliasesPackage 

 MyBaits 别名包扫描路径，通过该属性可以给包中的类注册别名，注册后在 Mapper 对应的 XML 文件中可以直接使 用类名，而不用使用全限定的类名（即 XML 中调用的时候不用包含包名）。 

```properties
 mybatis-plus.type-aliases-package = cn.itcast.mp.pojo
```

### 进阶配置

### mapUnderscoreToCamelCase （默认为true，不用设置）

 是否开启自动驼峰命名规则（camel case）映射，即从经典数据库列名 A_COLUMN（下划线命名） 到经典 Java 属 性名 aColumn（驼峰命名） 的类似映射。  

#### **注意：**

这个配置不能和mybatis-plus.config-location同时存在

```properties
#关闭自动驼峰映射，该参数不能和mybatis-plus.config-location同时存在
mybatis-plus.configuration.map-underscore-to-camel-case=false
```

###  cacheEnabled 

 全局地开启或关闭配置文件中的所有映射器已经配置的任何缓存，默认为 true。 

```properties
mybatis-plus.configuration.cache-enabled=false
```

