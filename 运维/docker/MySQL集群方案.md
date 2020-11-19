常见的MySQL集群方案：

* Replication

<img src="D:\Github\StudyNote\assets\docker-mysql-1.png" style="zoom:60%;" />

* PXC

<img src="D:\Github\StudyNote\assets\docker-mysql-2.png" style="zoom:60%;" />



#### PXC原理

* PXC(`Percona XtraDB Cluster`)

* 建立PXC使用`PerconaServer`(MySQL改进版，性能提升很大)

* 强一致性：同步复制，事务在所有集群节点要么同时提交，要么不提交；

  

<img src="D:\Github\StudyNote\assets\docker-mysql-3.png" style="zoom:60%;" />

#### Replication

* 采用异步复制，无法保证数据的一致性；

  

<img src="D:\Github\StudyNote\assets\docker-mysql-4.png" style="zoom:60%;" />



* 下载PXC镜像：`docker pull percona/percona-xtradb-cluster:5.7.21`；(最新的是MySQL8，会出现`New joining cluster node didn't find needed SSL artifacts`，建议使用MySQL5)
* 为镜像改名：`docker tag percona/percona-xtradb-cluster:5.7.21 pxc`

* 创建网段：`docker network create --subnet=172.18.0.0/24 net1`;
* 创建数据卷：`docker volume create --name v1`



#### PXC集群



创建第一个MySQL节点：

```
docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=weichuang -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=weichuang -v v1:/var/lib/mysql -v backup:/data --privileged --name=node1 --net=net1 --ip 172.18.0.2 pxc

注意：v1和backup都是创建的数据卷；net1是创建的网段
```



创建第二个MySQL节点：

```
docker run -d -p 3307:3306 -e MYSQL_ROOT_PASSWORD=weichuang -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=weichuang -e CLUSTER_JOIN=node1 -v v2:/var/lib/mysql -v backup:/data --privileged --name=node2 --net=net1 --ip 172.18.0.3 pxc
```



创建第三个MySQL节点：

```
docker run -d -p 3308:3306 -e MYSQL_ROOT_PASSWORD=weichuang -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=weichuang -e CLUSTER_JOIN=node1 -v v3:/var/lib/mysql -v backup:/data --privileged --name=node3 --net=net1 --ip 172.18.0.4 pxc
```



#### 数据库负载均衡

虽然搭建了集群，但是如果不使用数据库负载均衡，单节点处理所有请求，负载高，性能差。

使用Haproxy做负载均衡，请求被均匀分发给每个节点，单节点负载低，性能好。



![](D:\Github\StudyNote\assets\docker-mysql-5.png)

<img src="D:\Github\StudyNote\assets\docker-mysql-6.png" style="zoom:75%;" />



* 安装Haproxy：`docker pull haproxy`；

* 创建Haproxy配置文件(在宿主机里)：`touch ~/soft/haproxy/haproxy.cfg`

* 运行Haproxy：`docker run -it -d -p 4001:8888 -p 4002:3306 -v /home/root/haproxy:/usr/local/etc/haproxy --name h1 --privileged --net=net1 --ip 172.18.0.7 haproxy`
* 在主数据库中创建用户`haproxy`(在配置文件中使用)：`CREATE USER 'haproxy'@'%' IDENTIFIED BY '';`
* 进入Haproxy所在的容器：`docker exec -it h1 bash`

```
1. 启动Haproxy：haproxy -f /usr/local/etc/haproxy/haproxy.cfg

2. 安装keepalived
   apt-get update
   apt-get install keepalived
```

