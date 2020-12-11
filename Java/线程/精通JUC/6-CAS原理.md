#### CAS介绍

`CAS`，即`Compare and Swap`，比较再交换。该方法认为V的值应该是A，如果是的话就把它改为B，如果不是的话(说明被别人修改过)就不修改。

CAS有三个操作数：内存值V、预期值A、要修改的值B，当且仅当预期值A和内存值V相同时，才将内存值修改为B，否则什么都不做；最后返回现在的V值。



#### 应用场景

* 乐观锁；
* 并发容器；
* 原子类；



在Java中是如何利用CAS实现原子操作的？

* AtomicInteger加载Unsafe工具，用来直接操作内存数据
* 用 Unsafe来实现底层操作
* 用 volatile修饰value字段，保证可见性
* getAndAddInt方法分析

`Unsafe`是CAS的核心类。Java无法直接访问底层操作系统，而是通过本地（ native）方法来访问。不过尽管如此，JVM还是开了一个后门，JDK中有一个类 Unsafe，它提供了硬件级别的原子操作。

`valueOffset`表示的是变量值在内存中的偏移地址，因为`Unsafe`就是根据内存偏移地址获取数据的原值的，这样我们就能通过 `unsafe`来实现CAS了。



























