---
title: Redis 高级学习篇
categories: 后端   
tags:
  - redis
  - nosql
---

# Redis 高级配置

## 一 可能存在的问题

一般来说，要将 Redis 运用于在项目上，只是用一台 Redis 是万万不能的，原因如下：

1. 从结构上，单个 Redis 服务器会发生单点故障，并且一台服务器需要处理所有的请求负载，压力比较大。
2. 从容量上，单个Redis服务器内存容量有限，就算一个Redis服务器内容量为256G，也不会将所有的内存用作Redis存储内存，一般来说，单台Redis最大使用内存不应该超过20G。

**问题：** `内存容量有限、处理能力有限、无法高可用`



## 二 主从复制

### 简介

应用场景

> 电子商务网站上的商品，一般是一次上传，无数次浏览的，专业名词为“多读少写”

**主从复制**

一个 Redis 服务可以有多个该服务的复制品，这个 Redis 服务成为 Master，其他的复制品成为 Slaves。

![redis 主从复制](./images/redis/redis3.png)

**主从复制**

1. **读写分离**
2. **数据被复制了好多分**

### Redis 主从复制配置

#### 通用安装包redis

1. 主数据库不需要任务配置，创建一个从数据库

redis.conf 配置文件信息添加

```
-- port 6380
-- slaveof 127.0.0.1 6379
```

2. 启动从服务器

```
./bin/redis-server ./redisconf --port 6380 --saveof 127.0.0.1 6379
```

#### Docker 配置 Redis 主从复制

http://note.youdao.com/noteshare?id=7eeea47aaa338fb6625ec994257d73a9



### Redis 哨兵模式

解决 redis master主机出现宕机的可能 。

> 有了主从复制实现之后，我们如果想从服务器进行监控，那么在redis2.6以后提供了一个 哨兵 机制，并在 2.8 版本稳定下来。

![redis 哨兵](./images/redis/redis哨兵.png)

**核心：心跳机制** 快慢不定

> 1.不时地监控redis 是够按照预期良好的运行
>
> 2.如果出现某个redis 节点运行出现状况，能够通知另外一个进程
>
> 3.能够进行自动切换，当一个master节点不可用时，能够选举出master的多个slave中的一个作为新的master，其他的slave节点追随新的master地址。

### Redis Cluster集群

为了在大流量的访问下提供服务。解决高可用、高并发

Redis 集群搭建有多种，但从redis3.0 之后**最少使用3个master和3个slave才能建立集群**

Redis Cluster 采用无中心结构，每一个节点保存数据和整个集群状态，每一个节点都和其他所有节点相连，

![redis-cluster](./images/redis/redis-cluster.png)

1. 所有的 redis 节点彼此环联，内部采用二进制协议优化传输速度和带宽
2. 节点的 fail 是通过集群中超过半数的节点检测失效才生效
3. 客户端和 redis 节点直连，不需要中间proxy层，客户端不需要连接集群的所有节点，连接集群中任意一个节点即可。
4. redis cluster 把所有的物理节点映射到（0-16383）slot 上。
5. Redis 集群预先分好 16384 个哈希槽，当需要在redis 集群中放置一个 key-value 时，redis先对key使用 crc16 算法算出一个结果，然后把结果对 16384 求余数，这样每个 key 都会对应一个编号在 0-16383 之间的哈希槽，redis会根据节点数量大致均等的将哈希槽映射到不同的节点中。

### Redis Cluster 容错

​	容错性，是指软件检测应用程序运行的软件或者硬件中发生的错误并从错误中恢复的能力

#### 1 什么时候判断master 不可用？

​	投票机制，投票过程是集群中所有的 master 参与，如果半数以上 master 节点与 master 节点通讯超时(cluster-node-timeout) 认为当前 master 节点挂掉

#### 2 什么时候整个集群不可用

​	如果集群中任意 master 挂掉，且当前 master 没有 slave 集群进入 fail 状态，也可以理解成集群的 slot 映射[0-16383] 不完整时 进入 fail 状态，如果集群超过半数以上的 master 挂掉，无论是否有 slave ，集群都会进入 `挂掉状态`

### Redis Cluster 节点分配

​	`Redis Cluster 采用虚拟的槽分区`，所有的键根据哈希函数映射到 0-16383 个整数的槽，每个节点负责维护一部分的槽以及槽所印映射的键值数据

​	三个主节点分别是A、B、C三个节点，它们可以是一台机器上的三个端口，也可以是三台不同的服务器，那么，采用哈希槽（hash slot）的方式来分配 16384 个slot的话，他们三个节点分别承担的 slot 区间是

> 节点 A 覆盖 0-5460
>
> 节点 B 覆盖 5461-10922
>
> 节点 C 覆盖 10923-16383

## Redis 集群

集群搭建参考官网：[https://redis.io/topics/cluster-tutorial](https://redis.io/topics/cluster-tutorial)

redis 集群至少需要三个 master 节点，我们这里搭建三个 master 节点，并且给每一个 master 在搭建一个 slave 节点，总共 6 个 redis 节点，这里用一台机器部署这 6 个节点。三主三从模式，Redis 5.0 以上。
### 1、创建 Redis 节点安装路径
```
[root@localhost ~]# mkdir /usr/local/redis_cluster 
```

### 2、创建 7000 -7005 文件夹，分配redis.conf
```
[root@localhost ~]# cd /usr/local/redis_cluster/
[root@localhost redis_cluster]# mkdir 7000 7001 7002 7003 7004 7005
[root@localhost redis_cluster]# wget http://download.redis.io/redis-stable/redis.conf
[root@localhost redis_cluster]# ls
7000  7001  7002  7003  7004  7005  redis.conf
[root@localhost redis_cluster]# cp redis.conf ./7000
[root@localhost redis_cluster]# cp redis.conf ./7001
[root@localhost redis_cluster]# cp redis.conf ./7002
[root@localhost redis_cluster]# cp redis.conf ./7003
[root@localhost redis_cluster]# cp redis.conf ./7004
[root@localhost redis_cluster]# cp redis.conf ./7005
```
### 3、配置每一个的 redis.conf
```
# 关闭保护模式 用于公网访问
protected-mode no
# 端口
port 7000

# 开启集群模式
cluster-enabled yes
cluster-config-file nodes-7000.conf
cluster-node-timeout 5000

# 后台启动
daemonize yes
pidfile /var/run/redis_7000.pid
logfile "7000.log"

# dir /redis/data
# bind 127.0.0.1

# 连接从节点密码
masterauth 123456
# 设置密码
requirepass 123456
```
### 4、redis 启动
```
[root@localhost redis_cluster]# ./redis-5.0.8/src/redis-server ./7000/redis.conf
[root@localhost redis_cluster]# ps -ef|grep -i redis
[root@localhost redis_cluster]# ./redis-5.0.8/src/redis-server ./7001/redis.conf
[root@localhost redis_cluster]# ./redis-5.0.8/src/redis-server ./7002/redis.conf
[root@localhost redis_cluster]# ./redis-5.0.8/src/redis-server ./7003/redis.conf
[root@localhost redis_cluster]# ./redis-5.0.8/src/redis-server ./7004/redis.conf
[root@localhost redis_cluster]# ./redis-5.0.8/src/redis-server ./7005/redis.conf
```

### 5、创建 redis 集群
> 官网告知：
> 创建集群
> 现在我们有许多实例正在运行，我们需要通过将一些有意义的配置写入节点来创建集群。
> 如果您使用的是Redis 5，这很容易完成，这是因为嵌入到中的Redis Cluster命令行实用程序为我们提供了帮助，该实用程序redis-cli可用于创建新集群，检查或重新分片现有集群等。

命令：  `./redis-5.0.8/src/redis-cli --cluster create -a 123456 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 --cluster-replicas 1`

```
[root@localhost redis_cluster]# ./redis-5.0.8/src/redis-cli --cluster create -a 123456 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 --cluster-replicas 1
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 127.0.0.1:7004 to 127.0.0.1:7000
Adding replica 127.0.0.1:7005 to 127.0.0.1:7001
Adding replica 127.0.0.1:7003 to 127.0.0.1:7002
>>> Trying to optimize slaves allocation for anti-affinity
[WARNING] Some slaves are in the same host as their master
M: d48cfd2e99a71377e42629495529fcbcfd0d9543 127.0.0.1:7000
   slots:[0-5460] (5461 slots) master
M: 370cf09b9d18445cb9ce10dea9466f95cc0394d3 127.0.0.1:7001
   slots:[5461-10922] (5462 slots) master
M: 309eb1db4b3265f8898d144b0cfccd3f5e468574 127.0.0.1:7002
   slots:[10923-16383] (5461 slots) master
S: 052e1e68b84e54901df10159b04fbd9afd05aedd 127.0.0.1:7003
   replicates d48cfd2e99a71377e42629495529fcbcfd0d9543
S: ee4dba83f69c7bcde1b0266b4d995ece7a1bc79c 127.0.0.1:7004
   replicates 370cf09b9d18445cb9ce10dea9466f95cc0394d3
S: 488163d590240d9ff325b8fbf94c877aef588117 127.0.0.1:7005
   replicates 309eb1db4b3265f8898d144b0cfccd3f5e468574
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
..
>>> Performing Cluster Check (using node 127.0.0.1:7000)
M: d48cfd2e99a71377e42629495529fcbcfd0d9543 127.0.0.1:7000
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 309eb1db4b3265f8898d144b0cfccd3f5e468574 127.0.0.1:7002
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: ee4dba83f69c7bcde1b0266b4d995ece7a1bc79c 127.0.0.1:7004
   slots: (0 slots) slave
   replicates 370cf09b9d18445cb9ce10dea9466f95cc0394d3
S: 488163d590240d9ff325b8fbf94c877aef588117 127.0.0.1:7005
   slots: (0 slots) slave
   replicates 309eb1db4b3265f8898d144b0cfccd3f5e468574
S: 052e1e68b84e54901df10159b04fbd9afd05aedd 127.0.0.1:7003
   slots: (0 slots) slave
   replicates d48cfd2e99a71377e42629495529fcbcfd0d9543
M: 370cf09b9d18445cb9ce10dea9466f95cc0394d3 127.0.0.1:7001
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.

```

### 集群验证
在某台机器上 连接任意一个redis 客户端 就可以拿到所有的数据。

```
[root@localhost redis_cluster]# ./redis-5.0.8/src/redis-cli -h 127.0.0.1 -a 123456 -c -p 7000
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
127.0.0.1:7000> keys *
(empty list or set)
127.0.0.1:7000> info replication
```

```
127.0.0.1:7000> cluster nodes
309eb1db4b3265f8898d144b0cfccd3f5e468574 127.0.0.1:7002@17002 master - 0 1586487109560 3 connected 10923-16383
ee4dba83f69c7bcde1b0266b4d995ece7a1bc79c 127.0.0.1:7004@17004 slave 370cf09b9d18445cb9ce10dea9466f95cc0394d3 0 1586487109360 5 connected
488163d590240d9ff325b8fbf94c877aef588117 127.0.0.1:7005@17005 slave 309eb1db4b3265f8898d144b0cfccd3f5e468574 0 1586487109000 6 connected
052e1e68b84e54901df10159b04fbd9afd05aedd 127.0.0.1:7003@17003 slave d48cfd2e99a71377e42629495529fcbcfd0d9543 0 1586487108353 4 connected
d48cfd2e99a71377e42629495529fcbcfd0d9543 127.0.0.1:7000@17000 myself,master - 0 1586487108000 1 connected 0-5460
370cf09b9d18445cb9ce10dea9466f95cc0394d3 127.0.0.1:7001@17001 master - 0 1586487108857 2 connected 5461-10922
```
**注意：** 

- 主节点写入的时候，不会立马同步到 slave 分支，异步的去获取 key-value 值。
- 启动一定要加入 -c 

### Redis Cluster 总结

 Redis Cluster 为了保证数据的高可用，加入了主从模式，一个主节点对应一个或者多个从节点，主节点提供数据的存取，从节点则是从主节点拉取数据进行备份，当这个主节点挂掉之后，就会有这个从节点选取一个来从当主节点，从儿保证集群不会挂掉。

集群有ABC三个主节点，如果这3个节点都没有加入从节点，如果B挂掉了，我们就无法访问真个集群了，A和C的slot 也无法访问了。

所以在创建集群的时候，一定要为每一个主节点都添加一个从节点，比如像这样，主节点分别为A、B、C，从节点A1、B1、C1。这样保证A挂掉之后也可以继续正常工作。

A1 节点替换 A 节点，所以 Redis 集群会选择 A1 作为新的主节点，集群将会继续正确的提供服务，当B重新开启后，他会变成A1 的从节点。

如果 A 和 A1 同时挂掉，那么 Redis 集群将无法工作

### Redis cluster 关闭集群

```
/usr/local/redis_cluster/src/redis-cli -c -h 127.0.0.1 -p 7000 shutdown
/usr/local/redis_cluster/src/redis-cli -c -h 127.0.0.1 -p 7001 shutdown
```



```
[root@localhost redis_cluster]# vi redis-shutdown.sh
[root@localhost redis_cluster]# chmod u+x redis-shutdown.sh
```

