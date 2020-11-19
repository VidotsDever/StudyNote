#### JDK7内存模型



![](D:\Github\StudyNote\assets\JDK-7-内存.png)

#### JDK8内存模型



![](D:\Github\StudyNote\assets\JDK-8-内存.png)





#### `jstat`命令

查看堆内存使用情况：查看堆内存各部分的使用量，以及加载类的数量

* `jstat -class <进程id>`：查看类加载统计；
* `jstat -compiler <进程id>`：查看编译统计；
* `jstat -gc <进程id>`：查看垃圾回收统计；



#### jmap命令

获取更加详细的内容，如：内存使用情况的汇总、对内存溢出的定位与分析

* `jmap -heap <进程id>`：查看内存使用情况；
* `jmap -histo <进程id> | more`：查看内存中对象数量及大小；
* `jmap -dump:format=b,file=dumpFileName <进程id>`：将内存使用情况dump到文件中



#### jstack命令

将正在运行的JVM的线程情况进行快照，并且打印出来；



#### VisualVM

在jdk安装目录下的bin目录下，找到`jvisualvm.exe` ；



VisualVM不仅可以监控本地JVM进程，还可以监控远程的JVM进程，需要借助于JMX技术实现；







