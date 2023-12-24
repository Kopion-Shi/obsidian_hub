
### 安装erlang

由于RabbitMq需要erlang语言的支持，在安装RabbitMq之前需要安装erlang

`sudo apt-get install erlang-nox`

### 安装RabbitMq

1. 更新源  
    `sudo apt-get update`
    
2. 安装
    
    `sudo apt-get install rabbitmq-server`
    
3. 以应用方式
    

```Bash
sudo rabbitmq-server         # 启动
sudo rabbitmqctl stop       # 停止
sudo rabbitmqctl status     # 查看状态
```

4. 以服务方式启动（安装完之后在任务管理器中服务一栏能看到RabbtiMq）

```Bash
sudo rabbitmq-service install        # 安装服务
sudo rabbitmq-service start          # 开始服务
sudo rabbitmq-service stop           # 停止服务
sudo rabbitmq-service enable         # 使服务有效
sudo rabbitmq-service disable        # 使服务无效
sudo rabbitmq-service help           # 帮助
# 当rabbitmq-service install之后默认服务是enable的，如果这时设置服务为disable的话，rabbitmq-service start就会报错。
# 当rabbitmq-service start正常启动服务之后，使用disable是没有效果的
sudo rabbitmqctl stop                # 关闭服务

```

5. RabbitMq 管理插件启动，可视化界面

```Bash
sudo rabbitmq-plugins enable rabbitmq_management       # 启动
sudo rabbitmq-plugins disable rabbitmq_management      # 关闭
```

6. RabbitMq节点管理方式

```Bash
rabbitmqctl
```

### 添加admin，并赋予administrator权限

1. 添加admin用户，密码设置为admin123
    
    `sudo rabbitmqctl add_user admin admin123`
    
2. 赋予权限
    
    `sudo rabbitmqctl set_user_tags admin administrator`
    
3. 赋予virtual host中所有资源的配置、写、读权限以便管理其中的资源
    
    `sudo rabbitmqctl set_permissions -p / admin '.`_`' '.`_`' '.*'`  

    

### Web管理器连接[#](https://www.cnblogs.com/FleetingAstral/p/16025737.html#web%E7%AE%A1%E7%90%86%E5%99%A8%E8%BF%9E%E6%8E%A5)

浏览器访问http:// ubuntuip :15672，会发现拒绝连接  

原因是RabbitMQ安装后默认是不启动管理模块的，所以需要配置将管理模块启动

启动管理模块命令如下

```Bash
rabbitmqctl start_app                             # 启动软件
rabbitmq-plugins enable rabbitmq_management       # 开启管理插件
rabbitmqctl stop                                  # 停止
```

使用刚刚创建的admin就可以登录，密码为admin123