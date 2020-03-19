[TOC]



# java高级

## 多线程

#### 程序

是为完成特定任务、用某种语言编写的一组指令的集合，即指一段静态代码。

#### 进程

正在执行的程序，是个动态过程

是计算机中的程序关于某数据集合上的一次运行活动

进程是资源分配的基本单位

#### 线程

是操作系统能够进行调度的最小单位

它被包含在进程之中，是进程的实际运作单位，一条线程指的是进程中的一个单一顺序的控制流

是程序内部的一条执行路径

线程作为 调度和执行的单位，每个线程拥有独立的运行栈和程序计数器（pc）

线程共享相同的内存单元/内存地址空间，就是从同一个堆中分配对象，可以访问相同的变量和对象，这就存在安全隐患

#### 并行

多个单核CPU同时执行多个任务，比如多人同时做不同事

#### 并发

一个单核CPU同时执行多个任务，比如秒杀

#### 线程的创建使用

第一种方法：继承thread的类，重写run（）方法

第一步：创建thread的子类

第二步：重写thread类的run（）方法--->线程所要执行的操作

第三步：创建thread类子类的对象

第四步：通过对象调用start（）方法

#### start方法的两个作用

1，启动当前线程

2，调用当前线程run方法

#### 两种线程启动方式

1，创建一个子类继承thread类

2，创建Thread的匿名子类

#### 线程常用方法

void start（）：线程启动，并且调用run（）方法

void run（）：线程在被调度时执行的操作

String getName（）：返回线程的名称

void setName（String name）：设置线程名称（在start之前）

static Thread currentThread（）：返回当前线程，通常用于主线程和Runnable实现类

void yield（）：释放当前CPU执行权

void join（）：在线程a调用线程b的join（）方法，此时线程a进入阻塞状态，直到线程b完全执行完

void sleep（long millitime）：让当前线程睡眠指定毫秒数

#### 线程调度

抢占式：高优先级的线程抢占CPU

#### 优先级

MAX_PRIORITY：10

MIN_PRIORITY:1

NORM_PRIORITY:5

#### 管理优先级别的函数

void getPriority（）：获取当前线程优先级

void setPriority（）：设置当前线程优先级

ps：高优先级只是概率上讲比较大几率被执行，不是说高的执行完了低的才去执行

#### 有两种创建多线程的方式

第一种：创建一个类继承thread类

第二种：实现runnable接口，用该类创建对象，调用Thread的带参构造器，传入该对象

实现的方式较好，因为1，继承不能多父类扩展性差，2，实现的方式更适合处理多线程共享数据的情况

#### 线程的分类

守护线程和用户线程

典型的main和gc线程

若JVM里都是守护线程，那么JVM就会退出

#### 线程的生命周期

新建、就绪、运行、阻塞、死亡

新建：当Thread类或子类的对象被创建时

就绪：调用start（）方法后，进入线程队列等待CPU时间片时

运行：获得CPU资源时

阻塞：被认为挂起或执行输入输出操作时，让CPU临时终止自己的执行

死亡：线程完成工作或被强制终止或出现异常

#### 线程的安全问题解决方法

##### 方式一：synchronized同步

###### 形式一：同步代码块

synchronized（同步监视器）{

//需要被同步的代码

操作共享数据（如ticket）的代码

}

同步监视器：就是锁，任何一个类的对象都能充当锁

锁的要求：多个线程必须要共用一把锁

​	锁的示例 如：当前类名.class，this，Obj

好处：解决线程安全问题

坏处：只能一个线程参与，其他等待，就是一个单线程，效率慢

###### 形式二：同步方法

若操作共享数据代码完整的在一个方法内，那么就可以用synchronized修饰方法

1.同步方法也涉及到同步监视器，只是不需要我们显式声明

2.非静态同步方法的同步监视器是this，静态同步方法的同步监视器是当前类本身  

##### 死锁问题

定义：不同的线程分别占有对方需要的同步资源不放弃，都等待着对方放弃自己需要的同步资源

##### 方法二：Lock锁（JDK5.0新增）

通过显式定义同步锁对象来实现同步，同步锁使用Lock对象充当

Lock接口是控制多线程对共享资源进行访问的工具，ReentrantLock 类实现了 Lock ，它拥有与 synchronized 相同的并发性和 内存语义，在实现线程安全的控制中，比较常用的是ReentrantLock，可以 显式加锁、释放锁。

###### 示例：

class A{

​	 private final ReentrantLock lock = new ReenTrantLock(); 	

​	public void m(){

​		 lock.lock(); 

​		try{

​			 //保证线程安全的代码;

​			 } finally{ 

​				lock.unlock();

 		 }

​	 } 

}

##### synchronized和Lock的对比：

1. Lock是显式锁（手动开启和关闭锁，别忘记关闭锁），synchronized是 隐式锁，出了作用域自动释放
2. Lock只有代码块锁，synchronized有代码块锁和方法锁
3. 使用Lock锁，JVM将花费较少的时间来调度线程，性能更好。并且具有 更好的扩展性（提供更多的子类）

#### 线程的通信

##### wait() 与 notify() 和 notifyAll()

1.wait()：令当前线程挂起并放弃CPU进入阻塞状态，并释放同步监视器
2.notify()：唤醒正在排队等待同步资源的线程中优先级最高者结束等待 

3.notifyAll ()：唤醒正在排队等待资源的所有线程结束等待.

ps：这三个方法只有在synchronized方法或synchronized代码块中才能使用

##### 面试题：sleep（）和wait（）的异同

###### 相同点：一旦执行，线程进入阻塞状态

###### 不同点：

1.两个方法的声明位置不同：Thread类声明sleep（），Object类中声明wait（）

2.调用的要求不同：sleep（）任何地方都行，而wait只能在同步代码块里

3.sleep（）不会释放锁，wait（）会释放锁

#### JDK5.0新增线程创建方式

##### 方法三：实现Callable接口

1. 相比run()方法，可以有返回值 

2.  方法可以抛出异常  

3. 支持泛型的返回值 

4.  需要借助FutureTask类，比如获取返回结果

5. ps：Futrue接口可以对具体Runnable、Callable任务的执行结果进行取消、查询是
   否完成、获取结果等。 

   FutrueTask是Futrue接口的唯一的实现类 

   FutureTask 同时实现了Runnable, Future接口。它既可以作为 Runnable被线程执行，又可以作为Future得到Callable的返回值

###### Callable实现多线程过程

1. 创建一个实现类实现Callable
2. 实现call（）方法，将需要执行的操作写在call（）方法中
3. 创建Callable接口实现类的对象
4. 将实现类的对象传递到FutrueTask构造器中创建FutrueTask的对象
5. 将FutrueTask对象作为参数传递到Thread构造器中
6. 然后用Thread的对象调用start（）方法

###### Callable比Runnable接口强大在哪

1. call（）有返回值
2. call（）可以抛出异常
3. Callable支持泛型

##### 方法四：使用线程池创建多线程

###### 定义：

提前创建好多个线程，放入线程池，使用时直接获取，使用完放回池中，可避免频繁创建、实现重复利用

###### 好处：

1. 提高响应速度
2. 降低资源消耗
3. 便于线程管理



### ExecutorService：

真正的线程池接口。常见子类ThreadPoolExecutor

1.  void execute(Runnable command) ：执行任务/命令，没有返回值，一般用来执行 Runnable 
2.  <T> Future<T> submit(Callable<T> task)：执行任务，有返回值，一般又来执行 Callable
3. void shutdown() ：关闭连接池 

 Executors：工具类、线程池的工厂类，用于创建并返回不同类型的线程池 

1. Executors.newCachedThreadPool()：创建一个可根据需要创建新线程的线程池
2. Executors.newFixedThreadPool(n); 创建一个可重用固定线程数的线程池 
3. Executors.newSingleThreadExecutor() ：创建一个只有一个线程的线程池 
4. Executors.newScheduledThreadPool(n)：创建一个线程池，它可安排在给定延迟后运行命令或者定期地执行。

###### 创建过程

1. 提供指定数量的线程池
2. 执行指定线程操作，需提供Runnable接口或Callable接口实现类的对象
3. 关闭线程池

## java常用类

#### String的使用

##### String应注意的点

1. String声明为final的，不可被继承

2. String实现了Serializable接口：表示字符串是支持序列化的

   实现了Comparable接口

3. String内部定义了final char[] value用于存储字符串数据

4. String：代表不可变的字符序列，简称不可变性，即不可修改，只会新建

5. 通过字面量方式（区别于new）给一个字符串赋值，此时字符串值声明在字符串常量池中

6. 字符串常量池是不会存储相同的字符串的

7. 字符串常量存储在 字符串常量池，目的是共享
   字符串非常量对象 存储在堆中

##### String的赋值

String str = "hello";

//本质上this.value = new char[0]; 

String  s1 = new String(); 

//this.value = original.value; 

String  s2 = new String(String original); 

//this.value = Arrays.copyOf(value, value.length);

String  s3 = new String(char[] a); 

String  s4 = new String(char[] a,int startIndex,int count);



##### 面试题

String s=new String（“abc”）;方式创建对象时，内存创建了几个对象

两个：一个是堆空间的new出来的结构，另一个是char【】对应的常量池中的数据“abc”

##### ps：

1.  常量与常量的拼接结果在常量池。且常量池中不会存在相同内容的常量。 
2.  只要其中有一个是变量，结果就在堆中 
3.  如果拼接的结果调用intern()方法，返回值就在常量池中

##### String常用方法

1. int length（）：返回字符串长度
2. char charAt（int index）：返回索引处字符
3. boolean isEmpty（）：判断是否是空字符串
4. String trim（）：返回字符串副本，去除前后的空格
5. int indexOf(String str)：返回指定子字符串在此字符串中第一次出现处的索引 
6. String replace(char oldChar, char newChar)：返回一个新的字符串，它是 通过用 newChar 替换此字符串中出现的所有 oldChar 得到的。 
7. boolean matches(String regex)：告知此字符串是否匹配给定的正则表达式
8. String[] split(String regex)：根据给定正则表达式的匹配拆分此字符串。 

##### String方法大全

1. int length（）：返回字符串长度
2. char charAt（int index）：返回索引处字符
3. boolean isEmpty（）：判断是否是空字符串
4. String toLowerCase（）：将字符全部转为小写
5. String toUpperCase（）：将字符全部转为大写
6. String trim（）：返回字符串副本，去除前后的空格
7. boolean equalsIgnoreCase（）：判断字符串是否相同，并忽略大小写
8. int compareTo（）：比较字符串大小（每个字符逐一相减）
9. String substring（）：返回一个新字符串从开始索引到结束索引（不包含）的一个字符串
10. boolean endsWith(String suffix)：测试此字符串是否以指定的后缀结束 
11. boolean startsWith(String prefix)：测试此字符串是否以指定的前缀开始 
12. boolean startsWith(String prefix, int toffset)：测试此字符串从指定索引开始的 子字符串是否以指定前缀开始
13.  boolean contains(CharSequence s)：当且仅当此字符串包含指定的 char 值序列 时，返回 true 
14.  int indexOf(String str)：返回指定子字符串在此字符串中第一次出现处的索引 
15. int indexOf(String str, int fromIndex)：返回指定子字符串在此字符串中第一次出 现处的索引，从指定的索引开始 
16.  int lastIndexOf(String str)：返回指定子字符串在此字符串中最右边出现处的索引 
17. int lastIndexOf(String str, int fromIndex)：返回指定子字符串在此字符串中最后 一次出现处的索引，从指定的索引开始反向搜索 注：indexOf和lastIndexOf方法如果未找到都是返回-1
18. String replace(char oldChar, char newChar)：返回一个新的字符串，它是 通过用 newChar 替换此字符串中出现的所有 oldChar 得到的。 
19.  String replace(CharSequence target, CharSequence replacement)：使 用指定的字面值替换序列替换此字符串所有匹配字面值目标序列的子字符串。 
20. String replaceAll(String regex, String replacement) ： 使用给 定 的 replacement 替换此字符串所有匹配给定的正则表达式的子字符串。 
21. String replaceFirst(String regex, String replacement) ： 使用给 定 的 replacement 替换此字符串匹配给定的正则表达式的第一个子字符串
22. boolean matches(String regex)：告知此字符串是否匹配给定的正则表达式
23. String[] split(String regex)：根据给定正则表达式的匹配拆分此字符串。 
24. String[] split(String regex, int limit)：根据匹配给定的正则表达式来拆分此 字符串，最多不超过limit个，如果超过了，剩下的全部都放到最后一个元素中

##### String类与其他结构之间的转换

###### string和int之间

如：String str=“123”；

int num=Integer.parseInt（str）；

String str1=String.valueOf(num);

###### String和char【】之间

char【】charArray=str.toCharArray（）；

String str=new String（charArray）

###### String和byte【】之间

byte【】byte=String.getBytes（）；

String str=new String（byte）；

#### StringBuffer和StringBuilder的使用

StringBuffer：可变的数组序列，线程安全的，效率低，底层用char【】存储

StringBuilder：可变的字符序列，JDK5.0新增，线程不安全的，效率高，底层使用char【】存储

###### stringBuffer

1. 虽然新建的StringBuffer字符串都是sb.length（）+16，但sb.length（）还是等于真实长度
2. 扩容，默认情况都是2倍+2，同时将原来的数组元素复制到新数组，若扩容还放不下，那么直接用扩容后的长度
3. 开发建议：若长度过长，那么还是用带參的构造器创建，可以避免反复的扩容

###### StringBuffer常用方法

1. StringBuffer append(xxx)：提供了很多的append()方法，用于进行字符串拼接
2.  StringBuffer delete(int start,int end)：删除指定位置的内容 StringBuffer 
3. replace(int start, int end, String str)：把[start,end)位置替换为str 
4. StringBuffer insert(int offset, xxx)：在指定位置插入xxx 
5. StringBuffer reverse() ：把当前字符序列逆转

#### JDK8之前时间日期API

##### java.lanf.System类

###### long currentTimeMillis()：

用来返回当前时 间与1970年1月1日0时0分0秒之间以毫秒为单位的时间差。

##### java.util.Date:表示特定的瞬间，精确到毫秒  

######  构造器： 

1.  Date()：使用无参构造器创建的对象可以获取本地当前时间。
2.  Date(long date) 

###### 常用方法 :

 getTime():返回自 1970 年 1 月 1 日 00:00:00 GMT 以来此 Date 对象 表示的毫秒数。 

 toString():把此 Date 对象转换为以下形式的 String： dow mon dd hh:mm:ss zzz yyyy 其中： dow 是一周中的某一天 (Sun, Mon, Tue, Wed, Thu, Fri, Sat)，zzz是时间标准。 

#####  java.text.SimpleDateFormat

它允许进行格式化：日期->文本、解析：文本->日期

######  格式化：

 SimpleDateFormat() ：默认的模式和语言环境创建对象 

public SimpleDateFormat(String pattern)：该构造方法可以用参数pattern 指定的格式创建一个对象，该对象调用： 

public String format(Date date)：方法格式化时间对象date 

######  解析： 

public Date parse(String source)：从给定字符串的开始解析文本，以生成 一个日期

![1569830937920](C:\Users\13112\AppData\Roaming\Typora\typora-user-images\1569830937920.png)

#### JDK8.0中用到的新的时间API

LocalDate代表IOS格式（yyyy-MM-dd）的日期,可以存储 生日、纪念日等日期。

LocalTime表示一个时间，而不是日期。 

LocalDateTime是用来表示日期和时间的，这是一个最常用的类之一

#### Java比较器概述

 Java实现对象排序的方式有两种： 

自然排序：java.lang.Comparable 

定制排序：java.util.Comparator

##### Comparable接口的使用

1. 像String、包装类等实现了Comparable接口，重写了compareTo（）方法，给出了比较两个对象的大小的方式

2. 重写compareTo（obj）的规则：

   如果当前对象this大 于形参对象obj，则返回正整数

   如果当前对象this小于形参对象obj，则返回 负整数

   如果当前对象this等于形参对象obj，则返回零

3. 对于自定义的类需要排序，那么我们可以让自定义类实现Comparable接口重写compareTo（obj）方法，在方法指明如何排序

   ##### 实现comparator

   当元素的类型没有实现java.lang.Comparable接口而又不方便修改代码， 或者实现了java.lang.Comparable接口的排序规则不适合当前的操作，那 么可以考虑使用 Comparator 的对象来排序，强行对多个对象进行整体排 序的比较。

   ## 枚举类和注解

   #### 枚举类的使用

   ##### 枚举类：

   类的对象只有有限个，确定的

   #####  枚举类的定义：

   ###### 方式一：自定义

   1. 声明该类的对象属性，用private final修饰
   2. 私有化构造器，并给对象属性赋值
   3. 提供对象枚举类的多个对象：用public static final修饰

   ###### 方式二：使用enum

##### enum类的主要方法：

1. values()方法：返回枚举类型的对象数组。该方法可以很方便地遍历所有的 枚举值。
2.  valueOf(String str)：可以把一个字符串转为对应的枚举类对象。要求字符 串必须是枚举类对象的“名字”。如不是，会有运行时异常： IllegalArgumentException。
3.  toString()：返回当前枚举类对象常量的名称

#### 注解

##### 定义：

注解（Annotation） 其实就是代码里的特殊标记, 这些标记可以在编译, 类加载, 运行时被读取, 并执行相应的处理。通过使用Annotation, 程序员 可以在不改变原有逻辑的情况下, 在源文件中嵌入一些补充信息。代 码分析工具、开发工具和部署工具可以通过这些补充信息进行验证 或者进行部署。

##### 使用：

注解（Annotation） 可以像修饰符一样被使用, 可用于修饰包,类, 构造器, 方 法, 成员变量, 参数, 局部变量的声明, 这些信息被保存在Annotation 的 “name=value” 对中

ps：框架 = 注解 + 反射 + 设计模式

##### 使用示例

1. 生成文档相关注解
2. 在编译时进行格式检查
3. 跟踪代码依赖性，实现替代配置文件功能
4. 自定义：使用@interface
5. 通过反射获取注解信息

##### JDK5.0中的元注解（修饰其他注解的注解）

1. Retention：指定所修饰注解的生命周期

2. Target：用于修饰 Annotation 定义, 用于指定被修饰的 Annotation 能用于 修饰哪些程序元素。

3. Documented： 用于指定被该元 Annotation 修饰的 Annotation 类将被 javadoc 工具解析时，保留下来。

4. Inherited： 被它修饰的 Annotation 将具有继承性。如果某个类使用了被 @Inherited 修饰的 Annotation, `则其子类将自动具有该注解`