今天在clone自己的github到本地时，出现问题

![image-20191120151854094](images\image-20191120151854094.png)

下面是解决方案：

1、首先看看有没有原先设立的私钥和公钥

```bash
ls -l ~/.ssh
```

没有：

![image-20191120150615429](images\image-20191120150615429.png)

有：

![image-20191120150659176](images\image-20191120150659176.png)

2、如果没有，就通过命令创建

```bash
ssh-keygen -t rsa -C  "1311240778@qq.com"
```

效果：

![image-20191120150903332](images\image-20191120150903332.png)

3、 Key 添加到ssh-agent 

 ssh-agent 貌似是对解密的专用密钥进行高速缓存。在windows 没有这一步，linux还需要手动添加到ssh-agent。首先确定ssh-agent是否启用 

![image-20191120151201144](images\image-20191120151201144.png)

这就说明启用了

4、将私钥 添加到ssh-agent 

````bash
 ssh-add ~/.ssh/id_rsa
````

如果出现

![image-20191120151406236](images\image-20191120151406236.png)

那么就输入

````bash
 ssh-agent bash
````

5、将公钥添加到Github上面就行了

 这个过程很简单，我简略写一下。依次进入settings–>ssh keys–>add ssh key 然后把你的公钥添加进这里就ok了。 

![image-20191120151647434](images\image-20191120151647434.png)

