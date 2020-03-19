首先得安装erlang，其次得注意

```shell
./rabbitmq-server: line 80: erl: command not found
```

这个错误是因为没有配置环境变量

 在linux的/etc/profile环境下添加如下两行代码: 

````shell
#set erlang environment 
export PATH=$PATH:/usr/local/erlang20/bin
 
#set rabbitmq environment 
export PATH=$PATH:/usr/local/rabbitmq/sbin
````

这里需要注意到:

具体的PATH是要根据自己的安装路径来确定!

然后输入下面的代码使profile文件修改生效

````shell
source /etc/profile
````

