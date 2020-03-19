





# Java高级III

## IO流

![1569999500289](C:\Users\13112\AppData\Roaming\Typora\typora-user-images\1569999500289.png)

### File类的使用

实例化：

- public File(String pathname) 以pathname为路径创建File对象，可以是绝对路径或者相对路径，如果 pathname是相对路径，则默认的当前路径在系统属性user.dir中存储。

   绝对路径：是一个固定的路径,从盘符开始 相对路径：是相对于某个位置开始 

- public File(String parent,String child) 以parent为父路径，child为子路径创建File对象。 

- public File(File parent,String child) 根据一个父File对象和子文件路径创建File对

#### 路径分隔符

1. Java中的分隔符为 \\\
2. windows和DOS系统分隔符“\”
3. UNIX和URL中使用“/”来表示
4. 为了解决这个隐患，File类提供了一个常量：public static final String separator，它可以根据操作系统，动态的提供分隔符
5. ![1569981192068](C:\Users\13112\AppData\Roaming\Typora\typora-user-images\1569981192068.png)

#### File常用方法

##### File类的获取功能 

1. public String getAbsolutePath()：获取绝对路径 
2. public String getPath() ：获取路径 
3. public String getName() ：获取名称
4. public String getParent()：获取上层文件目录路径。若无，返回null 
5. public long length() ：获取文件长度（即：字节数）。不能获取目录的长度。 
6. public long lastModified() ：获取最后一次的修改时间，毫秒值
7. public String[] list() ：获取指定目录下的所有文件或者文件目录的名称数组 
8.  public File[] listFiles() ：获取指定目录下的所有文件或者文件目录的File数

##### File类的重命名功能

1. public boolean renameTo(File file1，File file2):把文件重命名为指定的文件路径（要保证file1存在，file2不存在）

##### File类判断功能：

1. public boolean isDirectory()：判断是否是文件目录
2.  public boolean isFile() ：判断是否是文件
3.  public boolean exists() ：判断是否存在 
4. public boolean canRead() ：判断是否可读 
5. public boolean canWrite() ：判断是否可写
6. public boolean isHidden() ：判断是否隐藏

##### File类的创建功能 

1. public boolean createNewFile() ：创建文件。若文件存在，则不创建，返回false 
2. public boolean mkdir() ：创建文件目录。如果此文件目录存在，就不创建了。 如果此文件目录的上层目录不存在，也不创建。 
3. public boolean mkdirs() ：创建文件目录。如果上层文件目录不存在，一并创建
   注意事项：如果你创建文件或者文件目录没有写盘符路径，那么，默认在项目 路径下

##### File类的删除功能

1.  public boolean delete()：删除文件或者文件夹（**Java中的删除不走回收站。 要删除一个文件目录，请注意该文件目录内不能包含文件或者文件目**）

### IO流原理及流的分类

站位在内存（程序）的角度的输入输出

#### 流的分类

1. 按操作数据单位不同分为：字节流(8 bit)，字符流(16 bit)
2. 按数据流的流向不同分为：输入流，输出流 
3. 按流的角色的不同分为：节点流，处理流

### 使用具体的流操作

1. 实例化File类对象，指明要操作的文件

   File file=new File（“XXX”）；

2. 提供具体的流，即实例化FileReader

   FileReader fr=new FileReader（file）；

3. 数据的读入

   char[] buf = new char[1024]; 

   int len; 

   while ((len = fr.read(buf)) != -1) { System.out.print(new String(buf, 0, len));

    } 

4. 流关闭

   fr.close();

   ### 结论

   对于文本文件，使用字符流处理

   对于非文本文件，就用字节流处理

#### 缓冲流：

1. 造文件
2. 造流
   1. 造字节流
   2. 造缓冲流
3. 读取/写入文件
4. 资源关闭

#### 转换流：

提供了在字节流和字符流之间的转换

InputStreamReader：将InputStream转换为Reader（解码：字节、字节数组-->字符数组、字符串）

OutputStreamWriter：将Writer转换为OutputStream（编码：字符数组、字符串-->字节、字节数组）

#### 对象流：

1.  序列化：用ObjectOutputStream类保存基本类型数据或对象的机制 
2.  反序列化：用ObjectInputStream类读取基本类型数据或对象的机制

#### **对象序列化机制**

允许把内存中的Java对象转换成平台无关的二进制流，从 而允许把这种二进制流持久地保存在磁盘上，或通过网络将这种二进制流传 输到另一个网络节点。//当其它程序获取了这种二进制流，就可以恢复成原 来的Java对象 

#### 关于类序列化的条件：

1. 需要实现接口：Serializable
2. 当前类提供一个全局常量：serialVersionUID（序列版本号）
3. 除了当前Person类需要实现Serializable之外，还必须保证其内部所有属性都是可序列化的（默认情况下，基本数据类型是可序列化的）







## 网络编程

### 网络基础

#### 网络编程的目的：

直接或间接地通过网络协议与其它计算机实现数据交换，进行通讯

网络编程中有两个主要的问题：

1. 如何准确地定位网络上一台或多台主机；定位主机上的特定的应用 
2. 找到主机后如何可靠高效地进行数据传输

#### 网络通信协议

![1570686020605](D:\学习笔记\面试专题\CSDN面试题\images\1570686020605.png)

#### 网络编程的两个要素

1. 对应问题一：IP和端口号
2. 对应问题二：提供网络通信协议：TCP/IP参考模型（应用层，传输层，网络层，数据链路层，物理层）

#### 网络请求过程：

1. 访问如：域名为www.baidu.com的网站
2. 先去本地找host文件有没有对应的域名
3. 没有再去请求DNS（域名解析服务器）
4. DNS返回该网站的IP地址
5. 获得资源

如何实例化 inetAddress：两个方法：getByName（String host）、getLocalHost（）

#### IP地址：InetAddress

唯一的标识 Internet 上的计算机（通信实体）

#### 端口号：

标识正在计算机上运行的进程（程序）

1.  不同的进程有不同的端口号 
2.  被规定为一个 16 位的整数 0~65535。 
3. 端口分类：
   1. 公认端口：0~1023。被预先定义的服务通信占用（如：HTTP占用端口 80，FTP占用端口21，Telnet占用端口23）
   2.  注册端口：1024~49151。分配给用户进程或应用程序。（如：Tomcat占 用端口8080，MySQL占用端口3306，Oracle占用端口1521等）。 
   3. 动态/私有端口：49152~65535。
4. 端口号与IP地址的组合得出一个网络套接字：Socket。

#### TCP 和 UDP

1. TCP协议：
   1.  使用TCP协议前，须先建立TCP连接，形成传输数据通道
   2.  传输前，采用“三次握手”方式，点对点通信，**是可靠的** 
   3. TCP协议进行通信的两个应用进程：客户端、服务端。
   4.  在连接中可**进行大数据量的传输**
   5.  传输完毕，**需释放已建立的连接，效率低** 
   
2.  UDP协议：
   1.  将数据、源、目的封装成数据包，**不需要建立连接** 
   2.  每个数据报的大小限制在64K内 
   3. 发送不管对方是否准备好，接收方收到也不确认，故是不可靠的
   4. 可以广播发送 
   5. 发送数据结束时**无需释放资源，开销小，速度快**
   
   ![image-20200223133944167](D:\学习笔记\JavaSE\images\image-20200223133944167.png)
   
   序列号seq：占4个字节，用来标记数据段的顺序，TCP把连接中发送的所有数据字节都编上一个序号，第一个字节的编号由本地随机产生；给字节编上序号后，就给每一个报文段指派一个序号；序列号seq就是这个报文段中的第一个字节的数据编号。
   
       确认号ack：占4个字节，期待收到对方下一个报文段的第一个数据字节的序号；序列号表示报文段携带数据的第一个字节的编号；而确认号指的是期望接收到下一个字节的编号；因此当前报文段最后一个字节的编号+1即为确认号。
   
       确认ACK：占1位，仅当ACK=1时，确认号字段才有效。ACK=0时，确认号无效
   
       同步SYN：连接建立时用于同步序号。当SYN=1，ACK=0时表示：这是一个连接请求报文段。若同意连接，则在响应报文段中使得SYN=1，ACK=1。因此，SYN=1表示这是一个连接请求，或连接接受报文。SYN这个标志位只有在TCP建产连接时才会被置1，握手完成后SYN标志位被置0。
   
       终止FIN：用来释放一个连接。FIN=1表示：此报文段的发送方的数据已经发送完毕，并要求释放运输连接
   
       PS：ACK、SYN和FIN这些大写的单词表示标志位，其值要么是1，要么是0；ack、seq小写的单词表示序号。
   
   

 ![img](D:\学习笔记\JavaSE\images\1) 

#### 三次握手：建立连接

第一次握手：建立连接时，客户端发送syn包（syn=x）到服务器，并进入SYN_SENT状态，等待服务器确认；SYN：同步序列编号（Synchronize Sequence Numbers）。

第二次握手：服务器收到syn包，必须确认客户的SYN（ack=x+1），同时自己也发送一个SYN包（syn=y），即SYN+ACK包，此时服务器进入SYN_RECV状态；

第三次握手：客户端收到服务器的SYN+ACK包，向服务器发送确认包ACK(ack=y+1），此包发送完毕，客户端和服务器进入ESTABLISHED（TCP连接成功）状态，完成三次握手。


![image-20200223134049487](D:\学习笔记\JavaSE\images\image-20200223134049487.png)

#### 四次挥手：释放连接

 ![img](D:\学习笔记\JavaSE\images\70) 

#### 面试题

##### 为什么连接的时候是三次握手，关闭的时候却是四次握手？ 

因为当Server端收到Client端的SYN连接请求报文后，可以直接发送SYN+ACK报文。其中ACK报文是用来应答的，SYN报文是用来同步的。但是关闭连接时，当Server端收到FIN报文时，很可能并不会立即关闭SOCKET，所以只能先回复一个ACK报文，告诉Client端，"你发的FIN报文我收到了"。只有等到我Server端所有的报文都发送完了，我才能发送FIN报文，因此不能一起发送。故需要四步握手。

#####  为什么TIME_WAIT状态需要经过2MSL(最大报文段生存时间)才能返回到CLOSE状态？ 

虽然按道理，四个报文都发送完毕，我们可以直接进入CLOSE状态了，但是我们必须假设网络是不可靠的，有可以最后一个ACK丢失。所以TIME_WAIT状态就是用来重发可能丢失的ACK报文。在Client发送出最后的ACK回复，但该ACK可能丢失。Server如果没有收到ACK，将不断重复发送FIN片段。所以Client不能立即关闭，它必须确认Server接收到了该ACK。Client会在发送出ACK之后进入到TIME_WAIT状态。Client会设置一个计时器，等待2MSL的时间。如果在该时间内再次收到FIN，那么Client会重发ACK并再次等待2MSL。所谓的2MSL是两倍的MSL(Maximum Segment Lifetime)。MSL指一个片段在网络中最大的存活时间，2MSL就是一个发送和一个回复所需的最大时间。如果直到2MSL，Client都没有再次收到FIN，那么Client推断ACK已经被成功接收，则结束TCP连接。

#####  为什么不能用两次握手进行连接？ 

3次握手完成两个重要的功能，既要双方做好发送数据的准备工作(双方都知道彼此已准备好)，也要允许双方就初始序列号进行协商，这个序列号在握手过程中被发送和确认。

​    现在把三次握手改成仅需要两次握手，死锁是可能发生的。

作为例子，考虑计算机S和C之间的通信，假定C给S发送一个连接请求分组，S收到了这个分组，并发 送了确认应答分组。按照两次握手的协定，S认为连接已经成功地建立了，可以开始发送数据分组。可是，C在S的应答分组在传输中被丢失的情况下，将不知道S 是否已准备好，不知道S建立什么样的序列号，C甚至怀疑S是否收到自己的连接请求分组。在这种情况下，C认为连接还未建立成功，将忽略S发来的任何数据分 组，只等待连接确认应答分组。而S在发出的分组超时后，重复发送同样的分组。这样就形成了死锁。

#####  如果已经建立了连接，但是客户端突然出现故障了怎么办？ 

TCP还设有一个保活计时器，显然，客户端如果出现故障，服务器不能一直等下去，白白浪费资源。服务器每收到一次客户端的请求后都会重新复位这个计时器，时间通常是设置为2小时，若两小时还没有收到客户端的任何数据，服务器就会发送一个探测报文段，以后每隔75秒钟发送一次。若一连发送10个探测报文仍然没反应，服务器就认为客户端出了故障，接着就关闭连接。


#### Socket：

 网络上具有唯一标识的IP地址和端口号组合在一起才能构成唯一能识别的标 识符套接字

##### Socket分类： 

1. 流套接字（stream socket）：使用TCP提供可依赖的字节流服务 
2. 数据报套接字（datagram socket）：使用UDP提供“尽力而为”的数据报服务

##### Socket类的常用构造器：

1.  public Socket(InetAddress address,int port)创建一个流套接字并将其连接到指定IP 地址的指定端口号。
2.  public Socket(String host,int port)创建一个流套接字并将其连接到指定主机上的指定端口号

#####  Socket类的常用方法：

1.  public InputStream getInputStream()返回此套接字的输入流。可以用于接收网络消息
2.   public OutputStream getOutputStream()返回此套接字的输出流。可以用于发送网络消息 
3. public InetAddress getInetAddress()此套接字连接到的远程 IP 地址；如果套接字是未连接的，则返回 null。
4.   public InetAddress getLocalAddress()获取套接字绑定的本地地址。 即本端的IP地址 
5. public int getPort()此套接字连接到的远程端口号；如果尚未连接套接字，则返回0。
6.  public int getLocalPort()返回此套接字绑定到的本地端口。 如果尚未绑定套接字，则返回 -1。即本端的 端口号。
7.   public void close()关闭此套接字。套接字被关闭后，便不可在以后的网络连接中使用（即无法重新连接 或重新绑定）。需要创建新的套接字对象。 关闭此套接字也将会关闭该套接字的 InputStream 和 OutputStream。
8.   public void shutdownInput()如果在套接字上调用 shutdownInput() 后从套接字输入流读取内容，则流将 返回 EOF（文件结束符）。即不能在从此套接字的输入流中接收任何数据。
9.  public void shutdownOutput()禁用此套接字的输出流。对于 TCP 套接字，任何以前写入的数据都将被发 送，并且后跟 TCP 的正常连接终止序列。 如果在套接字上调用 shutdownOutput() 后写入套接字输出流， 则该流将抛出 IOException。 即不能通过此套接字的输出流发送任何数据。

#### TCP网络编程

客户端：

1. 创建Socket对象，指明服务器的IP和端口号
2. 获取一个输出流，用于输出数据
3. 写出数据操作
4. 进行资源的关闭

服务器：

1. 创建服务器的ServerSocket，指明自己的端口号
2. 调用accept（）表示接受来自客户端的socket
3. 获取输入流
4. 读取输入流中的数据
5. 进行资源的关闭

#### UDP网络编程

类 DatagramSocket 和 DatagramPacket 实现了基于 UDP 协议网络程序

 UDP数据报通过数据报套接字 DatagramSocket 发送和接收，系统不保证 UDP数据报一定能够安全送到目的地，也不能确定什么时候可以抵达。 

DatagramPacket 对象封装了UDP数据报，在数据报中包含了发送端的IP 地址和端口号以及接收端的IP地址和端口号。 

##### 流程：

1. DatagramSocket与DatagramPacket 
2.  建立发送端，接收端 
3.  建立数据包 
4. 调用Socket的发送、接收方法 
5. 关闭Socket

#### URL网络编程

##### URL类

URL(Uniform Resource Locator)：统一资源定位符，它表示 Internet 上某一 资源的地址。 

URL的基本结构由5部分组成：

 <传输协议>://<主机名>:<端口号>/<文件名>#片段名?参数列表 

例如：

http://192.168.1.100:8080/helloworld/index.jsp#a?username=shkstart&password=123

#片段名：即锚点，例如看小说，直接定位到章

参数列表格式：参数名=参数值&参数名=参数值....

##### 常用方法：

1. public URL (String spec)：通过一个表示URL地址的字符串可以构造一个URL对象。例 如：URL url = new URL ("http://www. atguigu.com/"); 
2. public URL(URL context, String spec)：通过基 URL 和相对 URL 构造一个 URL 对象。 例如：URL downloadUrl = new URL(url, “download.html")
3. public String getProtocol(  )     获取该URL的协议名 
4. public String getHost(  )        获取该URL的主机名 
5. public String getPort(  )        获取该URL的端口号 
6. public String getPath(  )        获取该URL的文件路径 
7. public String getFile(  )         获取该URL的文件名 
8. public String getQuery(   )      获取该URL的查询名





## Java反射机制

### 反射的定义

Reflection（反射）是被视为动态语言的关键，反射机制允许程序在执行期 借助于Reflection API取得任何类的内部信息，并能直接操作任意对象的内部属性及方法。

### 反射的由来：

加载完类之后，在堆内存的方法区中就产生了一个Class类型的对象（一个类只有一个Class对象），这个对象就包含了完整的类的结构信息。我们可以通过这个对象看到类的结构。这个对象就像一面镜子，透过这个镜子看 到类的结构，所以，我们形象的称之为：反射

### 什么是反射机制

在运行的状态下，对于任何一个类，都知道该类的属性和方法，对于任何一个对象，都能获取它的属性和调用它的方法，对于这种能动态获取信息和调用对象方法的功能，就叫做Java语言的反射机制



#### 封装性和反射机制矛盾吗？

不矛盾，封装性的私有化只是在建议你不要去调用这个属性或方法，而反射机制呢，是解决你调用私有方法的问题，让你能去调用

#### 类的加载过程：

程序经过javac.exe命令后，会生成一个或多个字节码文件（.class结尾）

接着我们使用java.exe命令对某个字节码文件进行解释运行，相当于将某个字节码文件加载到内存中，此过程就称为类的加载。

加载到内存中的类，我们就称为运行时类，此运行时类，就作为Class的一个实例。

万事万物皆对象

#### 4种获取Class的实例的方式

1. 调用运行时类的属性

   Class cla=Person.class

2. 通过运行时类的对象，调用getClass（）

   Person p1=new Person（）；

   Class cla=p1.getClass（）；

3. 调用Class的静态方法：forName(String classPath)

   Class cla=Class.forName("com.atguigu.java.Person");

4. 使用类的加载器

   ClassLoader cl = this.getClass().getClassLoader(); 

   Class clazz4 = cl.loadClass(“类的全类名”);

```java
Class c1 = Object.class; 
Class c2 = Comparable.class; 
Class c3 = String[].class; 
Class c4 = int[][].class;
Class c5 = ElementType.class;
Class c6 = Override.class; 
Class c7 = int.class; Class c8 = void.class; Class c9 = Class.class;
int[] a = new int[10];
int[] b = new int[100];
Class c10 = a.getClass(); 
Class c11 = b.getClass(); 
// 只要元素类型与维度一样，就是同一个Class 
System.out.println(c10 == c11);
```

#### Class类常用方法

![1570689642126](D:\学习笔记\JavaSE\images\1570689642126.png)

#### 过程



#### 类加载器的作用

类加载的作用：将class文件字节码内容加载到内存中，并将这些静态数据转换成方 法区的运行时数据结构，然后在堆中生成一个代表这个类的java.lang.Class对象，作为 方法区中类数据的访问入口。



#### 创建运行时类的对象

 要想此方法正常的创建运行时类的对象，要求：

1. 运行时类必须提供空参的构造器
2. 空参的构造器的访问权限得够，通常设置为public

在javabean中要求提供一个public的空参构造器

1. 便于通过反射，创建运行时类的对象
2. 便于子类继承此运行时类时，默认调用super（）时，保证父类有此构造器

#### 调用运行实类的指定结构

1. ##### 调用指定方法

   通过反射，调用类中的方法，通过Method类完成。

   步骤：

    1.通过Class类的getMethod(String name,Class…parameterTypes)方法取得 一个Method对象，并设置此方法操作时所需要的参数类型。

    2.之后使用Object invoke(Object obj, Object[] args)进行调用，并向方法中 传递要设置的obj对象的参数信息。

   ##### 具体步骤

   Class clazz=Person.class;

   1. 创建运行时类的对象

      Person p=(Person)clazz.newInstance();

   2. 获取指定的某个方法

      Method show =clazz.gettDeclaredMethod("show",String.class);

   3. 保证当前方法是可访问的

      show.setAccessible(true);

   4. 调用invoke()方法

      Object returnValue=show.invoke(p,"CHN");

      注意这个返回值就是被调用的指定方法的返回值

2. ##### 调用指定属性

   在反射机制中，可以直接通过Field类操作类中的属性，通过Field类提供的set()和 get()方法就可以完成设置和取得属性内容的操作。

   1.  public Field getField(String name) 返回此Class对象表示的类或接口的指定的 public的Field。
   2.  public Field getDeclaredField(String name)返回此Class对象表示的类或接口的 指定的Field

   在Field中：

   1.  public Object get(Object obj) 取得指定对象obj上此Field的属性内容
   2. public void set(Object obj,Object value) 设置指定对象obj上此Field的属性内容

   **步骤**

   Class clazz=Person.class;

   1. 创建运行时类的对象

      Person p=(Person)clazz.newInstance();

   2. 获取运行时类中指定变量名的属性

      Filed name=clazz.getDeclaredField("name");

   3. 保证当前属性是可访问的

      name.setAccessible(true);

   4. 获取设置指定对象的属性值

      name.set(p,"Tom");

##### 关于setAccessible方法的使用

1. Method和Field、Constructor对象都有setAccessible()方法
2. setAccessible启动和禁用访问安全检查的开关。
3.  参数值为true则指示反射的对象在使用时应该取消Java语言访问检查。
   1.  提高反射的效率。如果代码中必须用反射，而该句代码需要频繁的被 调用，那么请设置为true。
   2. 使得原本无法访问的私有成员也可以访问 
4. 参数值为false则指示反射的对象应该实施Java语言访问检查

### 动态代理

#### 代理设计模式的原理：

使用一个代理将对象包装起来, 然后用该代理对象取代原始对象。任何对原始对象的调用都要通过代理。代理对象决定是否以及何时将方法调用转到原始对象上

#### 动态代理

动态代理是指客户通过代理类来调用其它对象的方法，并且是在程序运行时 根据需要动态创建目标类的代理对象。 

#### 动态代理相比于静态代理的优点：

抽象角色中（接口）声明的所有方法都被转移到调用处理器一个集中的方法中处理，这样，我们可以更加灵活和统一的处理众多的方法。