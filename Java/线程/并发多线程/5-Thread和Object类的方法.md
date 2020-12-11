* 为什么线程通信的方法`wait( )`， `notify( )`和 `notifyAll( )`被定义在`Object`类里？而`sleep`定义在 `Thread`类里？
* `join`、`sleep`和`wait`期间线程的状态分别是什么？为什么？



![](D:\Github\StudyNote\assets\thread-object-thread.png)



`wait()`是`Object`类的方法，让当前的线程处于等待状态(释放了当前获取到的锁)，直到在另一个线程中该对象调用`notify()`或者`notifyAll()`方法；注意：当前的线程必须获取到这个对象的monitor。`A monitor is mechanism to control concurrent access to an object.`



```
public static Object object = new Object();
    static class Thread1 extends Thread {
        @Override
        public void run() {
            synchronized (object) {
                System.out.println("线程" + Thread.currentThread().getName() +"开始执行");
                try {
                    object.wait(); // 释放锁
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("线程 " + Thread.currentThread().getName() + " 获得了锁");
            }
        }
    }

    static class Thread2 extends Thread {
        @Override
        public void run() {
            synchronized (object) {
                object.notify(); // 唤醒休息的线程；当前线程执行完成后，Thread1从中断的地方继续执行
                System.out.println("线程" + Thread.currentThread().getName() + "调用了Notify");
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread1 t1 = new Thread1();
        Thread2 t2 = new Thread2();
        t1.start();
        Thread.sleep(200);
        t2.start();
    }
```



