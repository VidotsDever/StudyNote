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
* 为镜像改名：`docker tag percona/percona-xtradb-cluster pxc`

* 创建网段：`docker network create --subnet=172.18.0.0/24 net1`;
* 创建数据卷：`docker volume create --name v1`

#### PXC集群



创建第一个MySQL节点：

```
docker run -d -p 4306:3306 -e MYSQL_ROOT_PASSWORD=weichuang -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=weichuang -v v1:/var/lib/mysql -v backup:/data --privileged --name=node1 --net=net1 --ip 172.18.0.2 pxc

注意：v1和backup都是创建的数据卷；net1是创建的网段
```



创建第二个MySQL节点：

```
docker run -d -p 4307:3306 -e MYSQL_ROOT_PASSWORD=weichuang -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=weichuang -e CLUSTER_JOIN=node1 -v v2:/var/lib/mysql -v backup:/data --privileged --name=node2 --net=net1 --ip 172.18.0.3 pxc
```









