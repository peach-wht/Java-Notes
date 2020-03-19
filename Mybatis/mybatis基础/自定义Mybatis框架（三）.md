### 涉及知识点：

本章我们将使用前面所学的基础知识来构建一个属于自己的持久层框架，将会涉及到的一些知识点：工厂模式 （Factory 工厂模式）、构造者模式（Builder 模式）、代理模式，反射，自定义注解，注解的反射，xml 解析， 

数据库元数据，元数据的反射等。

### 自定义Mybatis的分析：

​	mybatis在使用代理dao的方式实现增删改查时做什么事呢？
​		只有两件事：
​			第一：创建代理对象
​			第二：在代理对象中调用selectList

自定义mybatis能通过入门案例看到类
	class Resources
	class SqlSessionFactoryBuilder
	interface SqlSessionFactory
	interface SqlSession