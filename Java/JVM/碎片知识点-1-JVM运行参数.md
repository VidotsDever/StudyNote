#### 常见的Java命令



* `jps`：显示正在运行的Java进程；`jps -l`
* `jinfo -flags <进程id>` ： 查看指定进程的JVM参数；
* 



#### JVM运行参数



标准参数：

* -help
* -version



`-X`参数(非标准参数)：

* -Xint：解释模式执行
* -Xcomp：编译模式执行
* -Xmixed：混合模式执行(默认)



`-XX`参数(使用率较高)：主要用于JVM的调优和Debug操作

* -XX:newSize
* -xx:+UseSerialGC

```
-XX参数的使用有两种方式，一种是boolean类型，一种是非boolean类型

1. boolean类型
  格式： -XX:[+-]<name> 表示启用或禁用<name>属性
  如：-XX:+DisableExplicitGC表示禁用手动调用GC操作
  
 2. 非boolean类型
   格式： -XX:<name>=<value> 表示<name>属性的值为<value>
   如： -XX:NewRatio=1表示新生代和老年代的比值
```





`-Xms`：设置JVM的堆内存的初始大小；

```
-Xms512m 等价于-XX:InitialHeapSize，设置JVM初始堆内存为512M；
```



`-Xmx`：设置JVM的堆内存的最大大小；

```
-Xmx2048m 等价于-XX:MaxHeapSize，设置JVM最大堆内存为2048M；
```



