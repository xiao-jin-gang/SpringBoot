# Redis

------

## Nosql概述

为什么要用Nosql

 关系型数据库：行、列、表

## Nosql特点

1.方便扩展（数据之间没有关系，很好扩展）

2.大数据高性能（Redis一秒写8万次，）

3.数据类型是多样性的！（不需要事先设计数据库！随取随用！）

4.传统的RDBMS和NoSQL

```
传统的RDBMS
- 结构化组织
- SQL
-数据和关系都存在单独的表中
```

```
NoSql
- 不仅仅是数据
- 没有固定的查询语言
- 键值对存储，列存储，文档存储，图形数据库
- 最终一致性
- CAP定理 和 BASE (异地多活)
- 高性能，高可用，高可扩展性
- .......
```

#  Redis入门

## Redis是什么？

Redis即远程字典服务。

是一个开源使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、key-value数据库，并提供多种API。

Redis能干嘛？

1.内存存储、持久化、内存中是断电即失、所以说持久化很重要

2.效率高，可以用高速缓存

3.发布订阅系统

4.地图信息分析

5.计时器

6..........

## Linux安装redis

1.下载安装包 redis-5.0.8.tar.gz

2.解压Redis安装包  tar -zxvf redis-6.2.6.tar.gz

3.进入解压后的文件，可以看到redis的配置文件 

4.安装基本环境 

```bash
yum install gcc-c++ 

make	// 需要的文件全部配置上

make install //起确认作用
```

5.redis的默认安装路径

 ```shell
 cd /usr/local/bin
 ```

6.将redis配置文件复制到我们当前路径下

```shell
mkdir zconfig #将我们的redis配置文件创建备份

cp /usr/local/software/redis-6.2.6/redis.conf zconfig   #拷贝

zconfig/redis.conf	#之后就用此文件启动redis
```

7.redis默认不是后台启动的，修改配置文件

```
修改配置文件 redis.conf
daemonize no =》 daemonize yes
```

8.启动redis服务

```shell
#退一级回到bin目录下 启动
redis-server zconfig/redis.conf 
#使用redis客户端进行连接
redis-cli -p 6379
```

9.查看redis的进程是否开启

```shell
ps -ef|grep redis
```

10.如何关闭Redis服务命令

```shell
#使用redis客户端进行连接
redis-cli -p 6379
#执行关闭命令
shut down
# 退出
exit  
```

11.注意

```shell
#允许本地访问 从windows访问要注释掉
bind 127.0.0.1 
protected-mode no（保护模式改为no）
#改配置文件 redis.conf 后台启动
daemonize no =》 daemonize yes
```









