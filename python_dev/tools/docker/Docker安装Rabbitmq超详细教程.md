## 安装[Rabbitmq](https://so.csdn.net/so/search?q=Rabbitmq&spm=1001.2101.3001.7020)

### 1.使用[docker](https://so.csdn.net/so/search?q=docker&spm=1001.2101.3001.7020)查询rabbitmq的镜像

`docker search rabbitmq`  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210713142415838.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NTAyMzM2,size_16,color_FFFFFF,t_70)

### 2.安装镜像

安装name为rabbitmq的这里是直接安装最新的，如果需要安装其他版本在rabbitmq后面跟上版本号即可  
`docker pull rabbitmq`  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210713142423729.png)

### 3.运行mq：

**需要注意**的是`-p 5673:5672` 解释：-p 外网端口：docker的内部端口 ，你们可以改成自己的外网端口号，我这里映射的外网端口是5673那么程序连接端口就是用5673  
`docker run -d --hostname my-rabbit --name rabbit -p 15672:15672 -p 5673:5672 rabbitmq`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210713142432574.png)

通过`docker ps -a`查看部署的mq容器id，在通过 `docker exec -it 容器id /bin/bash` 进入容器内部在  
运行：`rabbitmq-plugins enable rabbitmq_management`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210713142438754.png)

现在可以通过访问http://linuxip:15672，访问web界面，这里的用户名和密码默认都是guest  
输入命令：exit退出容器目录.