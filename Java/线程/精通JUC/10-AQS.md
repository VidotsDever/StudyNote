`AQS`，全称`AbstractQueuedSynchronizer`：是一个用于构建锁、同步器、协作工具类的工具类（框架），有了AQS以后，更多的协作工具类都可以很方便得被写出来。

`AbstractQueuedSynchronizer`是 Doug Lea写的，从JDK1.5加入的—个基于FIFO等待队列实现的一个用于实现同步器的基础框架。

#### AQS最核心的就是三大部分:

* **state(volatile int)**：这里的 state的具体含义，会根据具体实现类的不同而不同，比如在 Semaphore里，它表示“剩余的许可证的数量”，而在 CountDownLatch里，它表示“还需要倒数的数量”；
* **控制线程抢锁和配合的FIFO队列**：这个队列用来存放“等待的线程”，AQS就是“排队管理器”，当多个线程争用同一把锁时，必须有排队机制将那些没能拿到锁的线程串在一起。当锁释放时，锁管理器就会挑选个合适的线程来占有这个刚刚释放的锁；
* 期望协作工具类去实现的获取/释放等重要方法：这里的获取和释放方法，是利用AQS的协作工具类里最重要的方法，是由协作类自己去实现的，并且含义各不相同；获取操作会依赖 state变量，经常会阻塞（比如获取不到锁的时候）；

#### AQS用法

* 第一步：写—个类，想好协作的逻辑，实现获取/释放方法；
* 第二步：内部写一个`Sync`类继承`AbstractQueuedSynchronizer`；
* 第三步：根据是否独占来重写 `tryAcquire`/ `tryRelease`或`tryAcquireShared(int acquires)`和`tryReleaseShared(int releases)`等方法，在之前写的获取/释放方法中调用AQS的acquire/ release或者 Shared方法



