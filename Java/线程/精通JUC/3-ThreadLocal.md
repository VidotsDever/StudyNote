使用场景：

* 每个线程需要一个独享的对象(通常是工具类，并且这些工具类不是线程安全的，典型需要使用的类有`SimpleDateFormat`和`Random`)；
* 每个线程内需要保存全局变量(例如在拦截器中获取用户信息)，可以让不同方法直接使用，避免参数传递的麻烦。



#### 每个线程需要一个独享的对象

每个Thread内有自己的实例副本，不共享；



下面给出错误的代码示例：

```
public class ThreadLocalNomal {
    public static ExecutorService threadPool = Executors.newFixedThreadPool(10);
    public static SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
    public static void main(String[] args) {
        for (int i = 0; i < 1000; i++) {
            final int finalI = i;
            threadPool.submit(new Runnable() {
                public void run() {
                    String date = new ThreadLocalNomal().dateStr(finalI);
                    System.out.println(date);
                }
            });
        }
        threadPool.shutdown();
    }

    public String dateStr(int seconds) {
        Date date = new Date(seconds * 1000);
        return format.format(date);
    }
}
```

上面的代码打印的日期将会出现相同的情况，这是错误的结果。`SimpleDateFormat`不是线程安全的，而上面的代码中所有的线程都共用同一个simpleDateFormat对象，所以出现错误的结果。



使用`ThreadLocal`解决以上的问题：每个线程都会持有SimpleDateFormat对象，而线程池中有固定数量的线程。

```
public class ThreadLocalNomal {

    public static ExecutorService threadPool = Executors.newFixedThreadPool(10);
    
    public static void main(String[] args) {
        for (int i = 0; i < 1000; i++) {
            final int finalI = i;
            threadPool.submit(new Runnable() {
                public void run() {
                    String date = new ThreadLocalNomal().dateStr(finalI);
                    System.out.println(date);
                }
            });
        }
        threadPool.shutdown();
    }

    public String dateStr(int seconds) {
        Date date = new Date(seconds * 1000);
        SimpleDateFormat format = ThreadSafeFormatter.dateFormatThreadLocal.get();
        return format.format(date);
    }

}

class ThreadSafeFormatter {
    public static ThreadLocal<SimpleDateFormat> dateFormatThreadLocal = new ThreadLocal<SimpleDateFormat>() {
        @Override
        protected SimpleDateFormat initialValue() {
            return new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
        }
    };
}
```



#### 当前用户信息需要被线程内所有方法共享

每个线程内需要保存全局变量，可以让不同方法直接使用，避免参数传递的麻烦；使用ThreadLocal，可以达到保存当前线程对应的用户信息的目的。



```
class UserContextHolder {
    public static ThreadLocal<User> holder = new ThreadLocal<User>();
}

public class TLSecond {
    public static void main(String[] args) {
        new Service1().process();
    }
}


class Service1 {
    public void process() {
        User user = new User("vidots");
        UserContextHolder.holder.set(user);
        new Service2().process();
    }
}

class Service2 {
    public void process() {
        User user = UserContextHolder.holder.get();
        System.out.println(user.getName());
    }
}

class User {
    private String name;

    public User(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```





#### ThreadLocal原理

ThreadLocal的两个作用：

* 让某个需要用到的对象在线程间隔离(每个线程都有自己的独立对象)
* 在任何方法中都可以轻松获取到该对象；



每个Thread对象中都持有一个ThreadLocalMap成员变量，而ThreadLocalMap的键类型是ThreadLocal。

ThreadLocal的主要方法：

* `T initialvalue()`:初始化
* `void set(T t)`:为这个线程设置一个新值
* `T get()`:得到这个线程对应的value。如果是首次调用`get()`，则会调用` initialize`来得到这个值
* `void remove()`:删除对应这个线程的值



`ThreadLocalMap`的每个`Entry`都是一个对`key`的弱引用，同时每个`Entry`都包含了一个对value的强引用，当key被垃圾回收后，就导致了value的泄露。解决方法：调用 `remove`方法，就会删除对应的 `Entry`对象，可以避免内存泄漏，所以使用完 `Threadlocal`之后，应该调用 `remove`方法。



注意点：

* 如果可以不使用 `ThreadLocal`就解决问题，那么不要强行使用，例如在任务数很少的时候，在局部变量中可以新建对象就可以解决问题，那么就不需要使用到 `ThreadLocal`

* 优先使用框架的支持，而不是自己创造，例如在 Spring中，如果可以使用 `RequestContextHolder`，那么就不需要自己维护 `ThreadLocal`，因为自己可能会忘记调用`remove()`方法等，造成內存泄漏

* 每次HTTP请求都对应一个线程，线程之间相互隔离，这就是 `ThreadLocal`的典型应用场景

  













