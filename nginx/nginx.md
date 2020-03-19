# **Nginx** 简介

## 1、什么是 nginx 

Nginx 是高性能的 HTTP 和反向代理的服务器，处理高并发能力是十分强大的，能经受高负 

载的考验,有报告表明能支持高达 50,000 个并发连接数。 

## 2、什么是反向代理

首先了解什么是正向代理

（1）需要在客户端配置代理服务器进行指定网站访问

 ![image-20191114165301428](images\image-20191114165301428.png)

反向代理 

暴露的是代理服务器地址，隐藏了真实服务器 IP 地址。 

![image-20191114165524038](images\image-20191114165524038.png)

## 3、什么是负载均衡

增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的 

情况改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负 

载均衡

![image-20191114165710066](images\image-20191114165710066.png)

## 4、动静分离

为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速 

度。降低原来单个服务器的压力

![image-20191114165806172](images\image-20191114165806172.png)

# nginx的常用命令和配置文件

## 常用命令

### 1、查看版本号

./nginx -v

### 2、启动

./nginx 

### 3、停止

./nginx -s stop

### 4、重新加载

./nginx -s reload



## 配置文件

一般是在  /usr/local/nginx/conf/nginx.conf

### 配置文件的内容主要包含三部分

#### 	1、全局块

​		主要是用来配置服务器整体运行的配置指令

​		**比如** **worker_processes 1; 处理并发数的配置**

#### 	2、events块

​		**影响** **Nginx** **服务器与用户的网络连接** 

​		**比如** **worker_connections 1024;** **支持的最大连接数为** **1024**

#### 	3、http块

​		这算是 Nginx 服务器配置中最频繁的部分，代理、缓存和日志定义等绝大多数功能和第三方模块的配置都在这里。 

​	    需要注意的是：http 块也可以包括 **http 全局块**、**server 块**。

​			**1）http** **全局块** 

 			http 全局块配置的指令包括文件引入、MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等。

​			**2）server** **块** 

​			 这块和虚拟主机有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是完全一样的，该技术的产生是为

​			了节省互联网服务器硬件成本。 每个 http 块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机。 

​			而每个 server 块也分为全局 server 块，以及可以同时包含多个 locaton 块。 

 				**（1）全局 server 块** 

​					 最常见的配置是本虚拟机主机的监听配置和本虚拟主机的名称或 IP 配置。 

 				**（2）、location 块** 

​					一个 server 块可以配置多个 location 块。  这块的主要作用是基于 Nginx 服务器接收到的请求字符串

​					（例如 server_name/uri-string），对虚拟主机名称 （也可以是 IP 别名）之外的字符串（例如 前面的 

​					/uri-string）进行匹配，对特定的请求进行处理。地址定向、数据缓存和应答控制等功能，还有许多第三方模

​					块的配置也在这里进行。 

# nginx反向代理

实例一实现效果：使用 nginx 反向代理，访问 www.123.com 直接跳转到 127.0.0.1:8080

## 实例一步骤：

1.先启动一个tomcat服务器

2.通过修改本地 host 文件，将 www.123.com 映射到 192.168.189.128

​	格式：192.168.189.128  www.123.com

3.在 nginx.conf 配置文件中增加如下配置 (完成)

![image-20191115125528778](images\image-20191115125528778.png)

实例二实现效果：使用 nginx 反向代理，根据访问的路径跳转到不同端口的服务中 

nginx 监听端口为 9001， 

访问 http://127.0.0.1:9001/edu/ 直接跳转到 127.0.0.1:8081 

访问 http://127.0.0.1:9001/vod/ 直接跳转到 127.0.0.1:8082

## 实例二步骤：

1.准备两个tomcat服务器，一个是8080端口、一个是8081端口

2.找到 nginx 配置文件，进行反向代理配置（记得防火墙开放对外访问的端口号 9001 8080 8081 ）

![image-20191115130021618](images\image-20191115130021618.png)

# nginx负载均衡

实现效果：浏览器地址栏输入地址 http://192.168.17.129/edu/a.html**，负载均衡效果，平均** 8080 

和 8081 端口中

## 步骤：

（1）准备两台 tomcat 服务器，一台 8080，一台 8081 

（2）在两台 tomcat 里面 webapps 目录中，创建名称是 edu 文件夹，在 edu 文件夹中创建 

页面 a.html，用于测试 

（3）在 nginx 的配置文件中进行负载均衡的配置

![image-20191115130455210](images\image-20191115130455210.png)

## 关于nginx 分配服务器策略

第一种 轮询（默认） 

每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器 down 掉，能自动剔除。 

第二种 weight 

weight 代表权重默认为 1,**权重越高被分配的客户端越多** 

![image-20191115151930190](images\image-20191115151930190.png)

第三种 ip_hash 

每个请求按访问 ip 的 hash 结果分配，这样每个访客固定访问一个后端服务器 

![image-20191115152006788](images\image-20191115152006788.png)

第四种 fair（第三方） 

按后端服务器的响应时间来分配请求，响应时间短的优先分配。

![image-20191115152040004](images\image-20191115152040004.png)

# nginx动静分离

Nginx 动静分离简单来说就是把动态跟静态请求分开，不能理解成只是单纯的把动态页面和 

静态页面物理分离。严格意义上说应该是动态请求跟静态请求分开，**可以理解成使用 Nginx**  

**处理静态页面，Tomcat 处理动态页面。**动静分离从目前实现角度来讲大致分为两种， 

**一种是纯粹把静态文件独立成单独的域名，放在独立的服务器上，也是目前主流推崇的方案；** 

**另外一种方法就是动态跟静态文件混合在一起发布，通过 nginx 来分开。**

通过 location 指定不同的后缀名实现不同的请求转发。通过 expires 参数设置，可以使 

浏览器缓存过期时间，减少与服务器之前的请求和流量。具体 Expires 定义：是给

一个资 源设定一个过期时间，也就是说无需去服务端验证，直接通过浏览器自身确认是否过期即可， 

所以不会产生额外的流量。此种方法非常适合不经常变动的资源。（如果经常更新的文件， 

不建议使用 Expires 来缓存），我这里设置 3d，表示在这 3 天之内访问这个 URL，发送 一

个请求，比对服务器该文件最后更新时间没有变化，则不会从服务器抓取，返回状态码 

304，如果有修改，则直接从服务器重新下载，返回状态码 200。

## 步骤

（1）在 liunx 系统中准备静态资源，用于进行访问 

![image-20191115152617005](images\image-20191115152617005.png)

（2）在 nginx 配置文件中进行配置

![image-20191115152643039](images\image-20191115152643039.png)

（3）测试结果

![image-20191115152749192](images\image-20191115152749192.png)

![image-20191115152820228](images\image-20191115152820228.png)



# nginx配置高可用的集群

什么是 nginx 高可用 

**简单的来说，就是当主机的nginx宕机了，能启用从机的nginx，让请求还能正常执行**

![image-20191115153122560](images\image-20191115153122560.png)

### 需要准备的东西：

（1）需要两台 nginx 服务器 

（2）需要 keepalived 

（3）需要虚拟 ip

### 准备实例的操作：

（1）需要两台服务器 192.168.17.129 和 192.168.17.131 

（2）在两台服务器安装 nginx 

（3）在两台服务器安装 keepalived

## 步骤

**（1）在两台服务器安装 keepalived** 

yum install keepalived  –y

**（2）完成高可用配置（主从配置）**

修改**/etc/keepalived/keepalivec.conf** 配置文件

```javascript
//全局部分
global_defs {
 notification_email {
 acassen@firewall.loc
 failover@firewall.loc
 sysadmin@firewall.loc
 }
 notification_email_from Alexandre.Cassen@firewall.loc
 smtp_server 192.168.17.129
 smtp_connect_timeout 30
    
//主机的名字，可以通过/etc/hosts文件查看    
 router_id LVS_DEVEL
}


//脚本相关配置
vrrp_script chk_http_port {
 script "/usr/local/src/nginx_check.sh"#脚本的位置
 interval 2 #（检测脚本执行的间隔）
 weight 2  #当脚本成立，那么就把当前主机的权重降为2
}


//虚拟ip配置
vrrp_instance VI_1 {
 state BACKUP # 备份服务器上将 MASTER 改为 BACKUP 
 interface ens33 //网卡
 virtual_router_id 51 # 主、备机的 virtual_router_id 必须相同
 priority 90 # 主、备机取不同的优先级，主机值较大，备份机值较小
 advert_int 1 #每隔多少秒检测主机是否还在运行
//权限校验方式
 authentication {
 auth_type PASS
 auth_pass 1111
 }
 virtual_ipaddress {
 192.168.17.50 // VRRP H 虚拟地址
 } 
}
```

在**/usr/local/src** 添加检测脚本 

```shell
#!/bin/bash
A=`ps -C nginx –no-header |wc -l`
if [ $A -eq 0 ];then
 /usr/local/nginx/sbin/nginx
 sleep 2
 if [ `ps -C nginx --no-header |wc -l` -eq 0 ];then
 killall keepalived
 fi
fi
```

把两台服务器上 nginx 和 keepalived 启动

**（3）测试**

**在浏览器地址栏输入 虚拟** **ip** **地址** **192.168.17.50** 

把主服务器（**192.168.17.129）nginx** 和 keepalived 停止，再输入 192.168.17.50 

# nginx的原理解析

## 1.master和worker

![image-20191115161550328](images\image-20191115161550328.png)

![image-20191115161629214](images\image-20191115161629214.png)

## 2.worker的工作机制

![image-20191115161743638](images\image-20191115161743638.png)

## 3.一个 master 和多个 woker 有好处

（1）可以使用 nginx –s reload 热部署，利用 nginx 进行热部署操作 

（2）每个 woker 是独立的进程，如果有其中的一个 woker 出现问题，其他 woker 独立的， 继续进行争抢，实现请求过程，不会造成服务中断

## 4.设置多少个 woker 合适

**worker 数和服务器的 cpu 数相等是最为适宜的**

## 5.连接数 worker_connection的问题

第一个：发送请求，占用了woker 的几个连接数？ 

答案：2 或者 4 个 

第二个：nginx 有一个 master，有四个 woker，每个 woker 支持最大的连接数 1024，支持的最大并发数是多少？ 

（1）普通的静态访问最大并发数是 **worker_connections（最大连接数）* worker_processes（worker的个数）/2**， 

（2）而如果是 HTTP 作 为反向代理来说，最大并发数量应该是 **worker_connections * worker_processes/4**。