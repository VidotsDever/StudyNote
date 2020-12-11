#### JVM参数类型

* 标准参数

```
-help
-server  -client
-version  -showversion
-cp  -classpath

-D<name>=<value> 设置系统属性
```

* X参数：非标准化参数

```
-Xint：解释执行；
-Xcomp：第一次使用就编译成本地代码；
-Xmixed：混合模式，JVM自己来决定是否编译成本地代码；
```



* XX参数：非标准化参数、相对不稳定、主要用于JVM调优和Debug

```
Boolean类型
格式：-XX:[+-]<name>表示启用或禁用name属性；
如： -XX:+UseG1GC

非Boolean类型
格式：-XX:<name>=<value>表示name属性的值是value
如：-XX:GCTimeRatio=19

注意：-Xmx和-Xms不是X参数，而是XX参数
-Xms等价于-XX:InitialHeapSize
-Xmx等价于-XX:MaxHeapSize
```

