# 第一章Maven的介绍

## 什么是 Maven

Maven 的正确发音是[ˈmevən]，而不是“马瘟”以及其他什么瘟。Maven 在美国是一个口语化的词 语，代表**专家、内行的意思**。 一个对 Maven 比较正式的定义是这么说的：**Maven 是一个项目管理工具，它包含了一个项目对象模 型 (POM：Project Object Model)，一组标准集合，一个项目生命周期(Project Lifecycle)，一个依赖管 理系统(Dependency Management System)，和用来运行定义在生命周期阶段(phase)中插件(plugin)目标 (goal)的逻辑**

## Maven能做什么

能帮你构建工程，管理 jar包，编译代码，还能帮你自动运行单元测试，打包，生成报表，甚至能帮你部署项目，生成 Web 站点

## Maven的两个经典作用

### 1、依赖管理

Maven 的一个核心特性就是依赖管理。当我们涉及到多模块的项目（包含成百个模块或者子项目），管理依赖就变成一项困难的任务。Maven 展示出了它对处理这种情形的高度控制。 

![image-20191121105851965](images\image-20191121105851965.png)

通过分析发现：maven 工程中不直接将 jar 包导入到工程中，而是通过在 pom.xml 文件中添加所需 jar 包的坐标，这样就很好的避免了 jar 直接引入进来，在需要用到 jar 包的时候，只要查找 pom.xml 文 件，再通过 pom.xml 文件中的坐标，到一个专门用于”存放 jar 包的仓库”(maven 仓库)中根据坐标从 而找到这些 jar 包，再把这些 jar 包拿去运行。 

#### 这样会不会太慢？

通过 pom.xml 文件配置要引入的 jar 包的坐标，再读取坐标并到仓库中加载 jar 包，这 

样我们就可以直接使用 jar 包了，为了解决这个过程中速度慢的问题，**maven 中也有索引的概念，通** 

**过建立索引，可以大大提高加载 jar 包的速度，使得我们认为 jar 包基本跟放在本地的工程文件中再** 

**读取出来的速度是一样的。**这个过程就好比我们查阅字典时，为了能够加快查找到内容，书前面的 

目录就好比是索引，有了这个目录我们就可以方便找到内容了，一样的在 maven 仓库中有了索引我 们就可以认为可以快速找到 jar 包。

### 2、项目一键构建

我们的项目，往往都要经历编译、测试、运行、打包、安装 ，部署等一系列过程。 

#### 什么是构建？ 

指的是项目从编译、测试、运行、打包、安装 ，部署整个过程都交给 maven 进行管理，这个过程称为构建。 

#### 一键构建 

指的是整个构建过程，使用 maven 一个命令可以轻松完成整个工作。

#### Maven 规范化构建流程如下：

![image-20191121110335500](images\image-20191121110335500.png)

# 第2章 Maven 的使用

## Maven仓库

![](images\maven仓库的种类和关系.png)

### 如上图：

### Maven的仓库类型

#### 本地仓库 ：

用来存储从远程仓库或中央仓库下载的插件和 jar 包，项目使用一些插件或 jar 包， 优先从本地仓库查找 默认本地仓库位置在 ${user.dir}/.m2/repository，${user.dir}表示 windows 用户目录

#### 远程仓库（私服）：

如果本地需要插件或者 jar 包，本地仓库没有，默认去远程仓库下载。 远程仓库可以在互联网内也可以在局域网内。

#### 中央仓库（一般是阿里云，外国的太慢） ：

在 maven 软件中内置一个远程仓库地址 http://repo1.maven.org/maven2 ，它是中 

央仓库，服务于整个互联网，它是由 Maven 团队自己维护，里面存储了非常全的 jar 包，它包 

含了世界上大部分流行的开源项目构件。

### 全局 setting 与用户 setting

maven安装目录下的conf文件的setting.xml是全局setting

在.m2文件夹下的setting是用户setting

### Maven 工程的认识

#### maven工程目录结构：

![image-20191121111445625](images\image-20191121111445625.png)

![image-20191121111532367](images\image-20191121111532367.png)

src/main/java —— 存放项目的.java 文件 

src/main/resources —— 存放项目资源文件，如 spring, hibernate 配置文件 

src/test/java —— 存放所有单元测试.java 文件，如 JUnit 测试类 

src/test/resources —— 测试资源文件 

target —— 项目输出位置，编译后的 class 文件会输出到此目录 

pom.xml——maven 项目核心配置文件 

注意：如果是普通的 java 项目，那么就没有 webapp 目录。

## 第3章 Maven 常用命令

### compile

compile 是 maven 工程的编译命令，作用是将 src/main/java 下的文件编译为 class 文件输出到 target 目录下。 

### **test** 

test 是 maven 工程的测试命令 mvn test，会执行 src/test/java 下的单元测试类。 

**clean** 

clean 是 maven 工程的清理命令，执行 clean 会删除 target 目录及内容。 

### package

package 是 maven 工程的打包命令，对于 java 工程执行 package 打成 jar 包，对于 web 工程打成 war 

包。

### install

install 是 maven 工程的安装命令，执行 install 将 maven 打成 jar 包或 war 包发布到本地仓库。 

当后面的命令执行时，前面的操作过程也都会自动执行，

### Maven 指令的生命周期

maven 对项目构建过程分为三套相互独立的生命周期，请注意这里说的是“三套”，而且“相互独立”， 

这三套生命周期分别是： 

**Clean Lifecycle（清理生命周期）** 在进行真正的构建之前进行一些清理工作。 

**Default Lifecycle（默认生命周期）** 构建的核心部分，编译，测试，打包，部署等等，特点环环相扣，后面的命令一般都是连前面的命令都执行的。 （重点）

**Site Lifecycle（站点生命周期）** 生成项目报告，站点，发布站点。 

![maven生命周期](images\maven生命周期.png)

### maven 的概念模型 

**Maven包含了一个项目对象模 型 (POM：Project Object Model)，一组标准集合，一个项目生命周期(Project Lifecycle)，一个依赖管 理系统(Dependency Management System)，和用来运行定义在生命周期阶段(phase)中插件(plugin)目标 (goal)的逻辑**

![](images\maven概念模型图.png)

#### **项目对象模型 (Project Object Model)**

一个 maven 工程都有一个 pom.xml 文件，通过 pom.xml 文件定义项目的坐标、项目依赖、项目信息、 插件目标等

#### **依赖管理系统(Dependency Management System)** 

通过 maven 的依赖管理对项目所依赖的 jar 包进行统一管理。 比如：项目依赖 junit4.9，通过在 pom.xml 中定义 junit4.9 的依赖即使用 junit4.9，如下所示是 junit4.9 的依赖定义： 

```xml
<!-- 依赖关系 -->
<dependencies>
<!-- 此项目运行使用 junit，所以此项目依赖 junit -->
<dependency>
<!-- junit 的项目名称 -->
<groupId>junit</groupId>
<!-- junit 的模块名称 -->
<artifactId>junit</artifactId>
<!-- junit 版本 -->
<version>4.9</version>
<!-- 依赖范围：单元测试时使用 junit -->
<scope>test</scope>
</dependency
```

#### **一个项目生命周期(Project Lifecycle)**

使用 maven 完成项目的构建，项目构建包括：清理、编译、测试、部署等过程，maven 将这些 

过程规范为一个生命周期，如下所示是生命周期的各各阶段：

![image-20191121143144863](images\image-20191121143144863.png)

maven 通过执行一些简单命令即可实现上边生命周期的各各过程，比如执行 mvn compile 执行编译、 

执行 mvn clean 执行清理。 

#### **一组标准集合** 

maven 将整个项目管理过程定义一组标准，比如：通过 maven 构建工程有标准的目录结构，有 标准的生命周期阶段、依赖管理有标准的坐标定义等。

#### **插件(plugin)目标(goal)** 

maven 管理项目生命周期过程都是基于插件完成的。 

