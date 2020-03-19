[TOC]

# Java高级II

## java集合

### 集合框架概述

集合、数组都是对多个数据进行存储操作的结构，简称Java容器，此时的存储，主要是指内存层面的存储，不涉及到持久性存储

Java集合可以分为Collection和Map两个体系

##### 数组在内存存储方面的特点：

1.  数组初始化以后，长度就确定了。 
2. 数组声明的类型，就决定了进行元素初始化时的类型 

##### 数组在存储数据方面的弊端：

1. 数组初始化以后，长度就不可变了，不便于扩展 
2. 数组中提供的属性和方法少，不便于进行添加、删除、插入等操作，且效率不高。 同时无法直接获取存储元素的个数
3.  数组存储的数据是有序的、可以重复的。---->存储数据的特点单一

#### Collection接口：

单列数据，定义了存取一组对象的方法的集合

1. List：有序可重复集合
   - ArrayList
   - LinkedList
   - Vector
2. Set：无序不可重复集合
   - HashSet
   - LinkedHashSet
   - TreeSet

#### Map接口：

双列数据，保存具有映射关系“key-value对”的集合

- HashMap
- LinkedHashMap
- TreeMap
- Hashtable
- Properties
- ConcurrentHashMap

##### 面试题：比较throw和throws区别

throw：生成一个异常对象，并抛出。使用在这个方法内部，自动抛出

throws：处理异常的方式。使用在方法声明处的末尾

#### Collection常用方法

1、添加

 add(Object obj) 

addAll(Collection coll) 

2、获取有效元素的个数 

int size()

 3、清空集合 

void clear() 

4、是否是空集合 

 boolean isEmpty() 

5、是否包含某个元素 

boolean contains(Object obj)：是通过元素的**equals**方法来判断是否 是同一个对象

 boolean containsAll(Collection c)：也是调用元素的**equals**方法来比 较的。拿两个集合的元素挨个比较

6、删除 

boolean remove(Object obj) ：通过元素的equals方法判断是否是 要删除的那个元素。只会删除找到的第一个元素 

 boolean removeAll(Collection coll)：取当前集合的差

 7、取两个集合的交集 

boolean retainAll(Collection c)：把交集的结果存在当前集合中，不 影响c 

8、集合是否相等 

 boolean equals(Object obj) 

9、转成对象数组 

 Object[] toArray() 

10、获取集合对象的哈希值 

hashCode()

11、遍历 

iterator()：返回迭代器对象，用于集合遍历

#### Iterator迭代器接口详解

##### 定义：

GOF给迭代器模式的定义为：提供一种**方法**访问一个**容器**(container)对象中**各个元素**，而**又不需暴露该对象的内部细节**。迭代器模式，就是为容器而生

##### 主要方法：





#### 使用 foreach 循环遍历集合元素()

##### 格式：

for(Object  obj : coll){

​	System.out.println(obj);

}

ps：本质上还是用迭代器实现遍历

### Collection子接口

#### List接口

 List集合类中元素有序、且可重复，集合中的每个元素都有其对应的顺序索引。 

 List容器中的元素都对应一个整数型的序号记载其在容器中的位置，可以根据 序号存取容器中的元素。

##### List接口的实现类常用的有：

ArrayList：作为List接口的主要实现类，线程不安全，效率高，使用Object【】存储

LinkedList：底层使用双向链表存储，对频繁的插入和删除操作，此类效率比ArrayList高

Vector：List的古老实现类（比List接口还早出现）线程安全，效率低，使用Object【】存储

##### 面试题：

ArrayList、LinkedList、Vector三者异同

##### List常用方法：

1. void add(int index, Object ele):在index位置插入ele元素 
2. boolean addAll(int index, Collection eles):从index位置开始将eles中 的所有元素添加进来 
3. Object get(int index):获取指定index位置的元素 
4. int indexOf(Object obj):返回obj在集合中首次出现的位置 
5. int lastIndexOf(Object obj):返回obj在当前集合中末次出现的位置 
6. Object remove(int index):移除指定index位置的元素，并返回此元素 
7. Object set(int index, Object ele):设置指定index位置的元素为ele 
8. List subList(int fromIndex, int toIndex):返回从fromIndex到toIndex 位置的子集

#####  ArrayList底层源码分析：

JDK7新建对象时就创建长度为10的数组（饿汉式）

JDK8新建对象时为0，插入第一个是长度为10（懒汉式）

加到10之后不够，那么就扩展数组长度为1.5倍（若知道创建长度，那么直接调用有参构造器，这样可以提高效率）

##### Vector底层源码分析：

初始为10，扩容为2倍，其他跟ArrayList一样

#### Set接口

##### 性质

1. 无序性：不等于随机性，存储的数据在底层数组中并非按照数组索引的顺序添加，而是根据HashCode（）方法得出来的哈希值决定的
2. 不可重复性：保证添加的元素按照equals（）判断时，不能返回true，即是：相同元素只能添加一个

##### 分类

1. HashSet：数组加链表存储，作为Set接口的主要实现类；线程不安全的；可以存储null值
2. LinkedHashSet：底层使用LinkedHashMap实现，线程不安全，有序
3. TreeSet：红黑树存储，可以按照添加元素的指定属性进行排序（不能添加不同类的对象）



##### HashSet添加过程

![1569906075570](C:\Users\13112\AppData\Roaming\Typora\typora-user-images\1569906075570.png)

1. 我们向HashSet中添加元素a，首先调用元素a所在类的hashCode（）方法，计算a的哈希值

2. 此哈希值接着通过某种算法计算出在HashSet底层数组中的存放位置，判断数组此位置上是否有元素：

   1. 没有，元素a添加成功
   2. 有，那么就比较a与其他元素b的哈希值
      1. 如果不同 ，a添加成功
      2. 相同，那么就调用equals（）方法
         1. equals（）返回true，a添加失败
         2. 返回false，a添加成功

   JDK7：元素a放在数组中，指向原来的的b

   JDK8：原来的元素b指向元素a

   总结：七上八下

   ##### 关于hashCode（）重写探讨：

   ![1569912017551](C:\Users\13112\AppData\Roaming\Typora\typora-user-images\1569912017551.png)

##### 向set添加数据前的要求

1. 一定要重写hashCode（）和equals（）方法
2. 重写尽量要保证两个函数的一致性：相等的对象必需具有相同的（哈希值）
3. 重写的技巧：两个函数所用对象相同

##### LinkedHashSet：

作为HashSet的子类，在添加数据的同时，每个数据还维护了两个引用，一个记录前一个数据一个后一个数据

优点：对于频繁的遍历操作，LinkedHashSet优于HashSet

##### TreeSet

1. 添加数据，只能是相同类
2. 两种排序方式：自然排序（Comparable接口）和定制排序（comparator接口）

### Map接口

双列数据，存储key-value对的数据

1. HashMap：Map的主要实现类，线程不安全，效率高，可以存储null的key-value ，底层使用JDK7以前数组+链表，JDK8数组+链表+红黑树
   - LinedHashMap：保证在遍历map元素时，可以按照添加的顺序遍历
2. TreeMap：按照添加的key-value进行排序，实现排序遍历。用key自然排序或定制排序，底层使用红黑树
3. Hashtable：古老的实现类，线程安全，效率低,不能存储null的key-value  
   - Properties：常用来处理配置文件，

##### 面试题

1. HashMap的底层实现原理

   HashMap基于hashing原理，我们通过put()和get()方法储存和获取对象。当我们将键值对传递给put()方法时，它调用键对象的hashCode()方法来计算hashcode，让后找到bucket位置来储存值对象。当获取对象时，通过键对象的equals()方法找到正确的键值对，然后返回值对象。

2. HashMap和Hashtable的区别

3. CurrentHashMap与Hashtable的异同

concurrentHashMap则使用分段，相当于把一个hashmap分成多个，然后每个部分分配一把锁，这样就可以支持多线程访问。

##### key-value的特点

![1569916545056](C:\Users\13112\AppData\Roaming\Typora\typora-user-images\1569916545056.png)

#### HashMap的put方法底层实现

**JDK7**：

**过程：**

HashMap map=new HashMap（）；

在实例化以后，底层创建了长度为16的一个一维数组entry【】table

map.put（key1，value1）；

首先，调用key1所在类的hashCode（）计算key1哈希值，此哈希值经过某种算法计算以后，得到在Entry数组中的存放位置

如果此位置为空，那么添加成功

如果该位置上不为空，比较key1和已存在的一个或多个数据的哈希值：

​	如果key1的哈希值与已存在的数据（单个或链表）不同，那么就添加成功

​	如果key1与存在的某一个数据（key2，value2）的哈希值相同，继续比较：调用key1所在类的equals（key2）方法

​		如果equals（）返回false：那么key1-value1添加成功

​		如果equals（）返回true：使用value1覆盖value2

默认扩容方式，当超过临界值（当前数组长度*0.75（默认加载因子））的时候且存放位置非空，那么扩容为原来的2倍，并把原有的元素复制过来

**JDK8**：

![1569920261969](C:\Users\13112\AppData\Roaming\Typora\typora-user-images\1569920261969.png)

1. new HashMap（）：底层没有创建一个长度为16的数组

2. JDK8底层的数组是Node【】，而非Entry【】

3. 首次调用put（）方法时，底层才创建长度为16的数组

4. JDK7底层结构只有：数组加链表，JDK8底层结构：数组+链表+红黑树

   当数组的某个索引位置的元素个数>8且当前数组长度>64那么此时索引位置上的数据全部改为红黑树

##### HashMap源码中的重要常量 

DEFAULT_INITIAL_CAPACITY : HashMap的默认容量，16

DEFAULT_LOAD_FACTOR：HashMap的默认加载因子（0.75）

 TREEIFY_THRESHOLD：Bucket中链表长度大于该默认值，转化为红黑树 

threshold：扩容的临界值，=容量*填充因子 

##### Map**接口常用方法**

**添加、删除、修改操作：**

1.  **Object put(Object key,Object value)**：将指定key-value添加到(或修改)当前map对象中 
2. **void putAll(Map m):**将m中的所有key-value对存放到当前map中 
3.  **Object remove(Object key)**：移除指定key的key-value对，并返回value 
4. **void clear()**：清空当前map中的所有数据

#### ConcurrentHashMap

 ConcurrentHashMap是Java中的一个**线程安全且高效的HashMap实现**。平时涉及高并发如果要用map结构，那第一时间想到的就是它。 

##### put方法执行步骤

1、判断Node[]数组是否初始化，没有则**进行初始化操作**
 2、通过**hash定位数组的索引坐标**，是否有Node节点，如果没有则使用CAS进行添加（链表的头节点），添加失败则进入下次循环。
 3、检查到内部正在扩容，就帮助它一块扩容。
 4、如果f！=null，则**使用synchronized锁住**f元素（链表/红黑树的头元素）。如果是Node（链表结构）则执行链表的添加操作；如果是TreeNode（树型结构）则执行树添加操作。
 5、判断链表长度已经达到临界值8（默认值），当节点超过这个值就需要**把链表转换为树结构**。
 6、如果添加成功就调用addCount（）方法统计size，并且检查是否需要扩容

 其中抛弃了原有的**Segment 分段锁，而采用了 CAS + synchronized 来保证并发安全性**。至于如何实现，那我继续看一下put方法逻辑 

![image-20200223181211891](D:\学习笔记\JavaSE\images\image-20200223181211891.png)

#### 面试题

##### HashMap、Hashtable、ConccurentHashMap三者的区别

HashMap线程不安全，数组+链表+红黑树
 Hashtable线程安全，锁住整个对象，数组+链表
 ConccurentHashMap线程安全，CAS+同步锁，数组+链表+红黑树
 HashMap的key，value均可为null，其他两个不行。

**元素查询的操作：**

1. **Object get(Object key)**：获取指定key对应的value 
2. **boolean containsKey(Object key)**：是否包含指定的key 
3. **boolean containsValue(Object value)**：是否包含指定的value 
4.  **int size()**：返回map中key-value对的个数 
5.  **boolean isEmpty()**：判断当前map是否为空 
6.  **boolean equals(Object obj)**：判断当前map和参数对象obj是否相等

**元视图操作的方法**

1. **Set keySet()：**返回所有key构成的Set集合
2.   **Collection values()**：返回所有value构成的Collection集合
3.  **Set entrySet()**：返回所有key-value对构成的Set集合

##### **面试题：**

1. 谈谈你对HashMap中put/get方法的认识？如果了解再谈谈 HashMap的扩容机制？默认大小是多少？什么是负载因子( 或填充比)？什么是吞吐临界值(或阈值、threshold)？
2. 负载因子值的大小，对HashMap有什么影响

#### Collections工具类

对Set，List，Map等集合进行操作的工具类

**排序操作：**（均为static方法） 

1. reverse(List)：反转 List 中元素的顺序 
2. shuffle(List)：对 List 集合元素进行随机排序 
3. sort(List)：根据元素的自然顺序对指定 List 集合元素按升序排序 
4. sort(List，Comparator)：根据指定的 Comparator 产生的顺序对 List 集合元素进行排序 
5. swap(List，int， int)：将指定 list 集合中的 i 处元素和 j 处元素进行交换

**Collections常用方法**：

**查找、替换**

1. Object max(Collection)：根据元素的自然顺序，返回给定集合中的最大元素
2.  Object max(Collection，Comparator)：根据 Comparator 指定的顺序，返回 给定集合中的最大元素 
3. Object min(Collection) 
4. Object min(Collection，Comparator) 
5. int frequency(Collection，Object)：返回指定集合中指定元素的出现次数 
6. void copy(List dest,List src)：将src中的内容复制到dest中 
7. boolean replaceAll(List list，Object oldVal，Object newVal)：使用新值替换 List 对象的所有旧

**Collections常用方法：同步控制（就是用synchronized代码块包了下）：**

Collections 类中提供了多个 synchronizedXxx() 方法，该方法可使将指定集 合包装成线程同步的集合，从而可以解决多线程并发访问集合时的线程安全 问题 





## 泛型

### 泛型是什么

泛型可以说是一种标签（限制集合的数据类型）

### 定义：

所谓泛型，就是允许在定义类、接口时通过一个标识表示类中某个属性的类 型或者是某个方法的返回值及参数类型。这个类型参数将在使用时（例如， 继承或实现这个接口，用这个类型声明变量、创建对象时）确定（即传入实 际的类型参数，也称为类型实参）。

