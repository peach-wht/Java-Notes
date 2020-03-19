[TOC]

# Java8新特性

## Lambda表达式

Lambda 是一个匿名函数，我们可以把 Lambda 表达式理解为是一段可以
传递的代码（将代码像数据一样进行传递）

### 本质

作为接口的实例

### 举例

![1570086998172](C:\Users\13112\AppData\Roaming\Typora\typora-user-images\1570086998172.png)

![1570087023520](C:\Users\13112\AppData\Roaming\Typora\typora-user-images\1570087023520.png)

### Lambda语法

比如：(o1，o2)->Integer.compare(o1,o2);

->:lambda操作符 或箭头操作符

->的左边：lambda形参列表（接口中方法的形参列表）

->的右边：lambda体（重写抽象方法的方法体）

### Lambda语法形式

1. 语法格式一：无参，无返回值

   Runnable r1=()->System.out.println(str);

2. 语法格式二：Lambda需要一个参数，但没返回值

   Consumer<String>con=(String str)->System.out.println(str);

3. 语法格式三：数据类型可以省略，因为可由编译器推断得出，称为“类型推断”

   Comsumer<String>con=(str)->System.out.println(str);

4. 语法格式四：Lambda 若只需要一个参数时，参数的小括号可以省

   Consumer<String>con=str->System.out.println(str);

5. 语法格式五：Lambda 需要两个或以上的参数，多条执行语句，并且可以有返回值

   Consumer<Integer>con=(x,y)->{System.out.println("str");

   return Integer.compare(x,y);

   };

6. 语法格式六：当 Lambda 体只有一条语句时，return 与大括号若有，都可以省略



### 函数式(Functional)接口

只包含一个抽象方法的接口，称为函数式接口。 





## 方法引用与构造器引用

### 方法引用与构造器引用

当要传递给Lambda体的操作，已经有实现的方法了，可以使用方法引用

要求：实现接口的抽象方法的参数列表和返回值类型，必须与方法引用的 方法的参数列表和返回值类型保持一致

 如下三种主要使用情况： 

1. 对象::实例方法名 
2. 类::静态方法名 
3. 类::实例方法名

方法引用就 是Lambda表达式，也就是函数式接口的一个实例

Consumer<String>con=(x)->System.out.println(x);

Consumer<String> con=System.out::println;

### 构造器引用

格式： ClassName::new 

要求构造器参数列表要与接口中抽象 方法的参数列表一致！且方法的返回值即为构造器对应类的对象。

### 数组引用

格式： type[] :: new





## 强大的Stream API

 使用 Stream API 对集合数据进行操作，就类似于使用 SQL 执行的数据库查询。 也可以使用 Stream API 来并行执行操作。简言之，Stream API 提供了一种 高效且易于使用的处理数据的方式。

###  Stream 和 Collection 集合的区别：

Collection 是一种静态的内存数据 结构，而 Stream 是有关计算的。前者是主要面向内存，存储在内存中， 后者主要是面向 CPU，通过 CPU 实现计算。

#### 注意： 

①Stream 自己不会存储元素。 

②Stream 不会改变源对象。相反，他们会返回一个持有结果的新Stream。

③Stream 操作是延迟执行的。这意味着他们会等到需要结果的时候才执行

### Stream 的操作三个步骤

1- 创建 Stream
一个数据源（如：集合、数组），获取一个流 

2- 中间操作
一个中间操作链，对数据源的数据进行处理 

3- 终止操作(终端操作)
一旦执行终止操作，就执行中间操作链，并产生结果。之后，不会再被使用